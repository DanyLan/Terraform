# Terraform

Terraform is commonly used in the DevOps industry and uses Infrastructure as Code (IAC) in order to easily deploy resources that you need.

# Difference between Terraform and Google Deployment manager

Terraform

1. Can be used across the major cloud provider such as GCP, AWS and Azure.

2. Terraform uses HashiCorp's Configuration Language (HCL).

Google Deployment manager

1. Is a native GCP IAC tool.

2. Is used in Marketplace solutions.

3. Uses Python and Jinja2 as languages.


# Installing Terraform

We are going to work on [Centos](https://learn.hashicorp.com/tutorials/terraform/install-cli).

Create a folder

    mkdir network
    
# Create a Service account

Create a service account (you may give it owner/editor role) and upload key to VM this way. Then move the key to a folder of your choice.

![](https://github.com/DanyLan/Terraform/blob/main/Screenshot%202020-10-09%20at%2014.51.44.png)

# Create a VPC network

Create the main.tf file where 'name' is the name of your key.

    provider "google" {
      version = "3.5.0"

      credentials = file("<NAME>.json") // add your own credentials

      project = "<PROJECT_ID>"
      region  = "us-central1"
      zone    = "us-central1-c"
    }

    resource "google_compute_network" "vpc_network" {
      name = "terraform-network"
    } 
    
Run `terraform init` (if you get failure and make change use terraform init --reconfigure in order to start afresh)

In order to review changes that are going to happen please run 

    terraform plan
    
Now to create the vpc network and save your state in bucket/folder created, please run 

    terraform apply
    
In order to delete the VPC network created, apply

    terraform destroy
    
How to use `terraform output`. We are going to get the value of the static ip that we are going to attach to our created VM

  <pre>provider "google" {
  version = "3.5.0"
  credentials = file("<name>.json")
  project = "PROJECT_ID"
  region  = "us-central1"
  zone    = "us-central1-c"
}
resource "google_compute_network" "vpc_network" {
  name = "new-terraform-network"
}

resource "google_compute_instance" "vm_instance" {
  name         = "terraform-instance"
  machine_type = "f1-micro"
  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-9"
    }
  }
  network_interface {
    network = google_compute_network.vpc_network.name
    access_config {
    }
  }
}
resource "google_compute_address" "static_ip" {
  name = "terraform-static-ip"
}</pre>
    
Create an output file this way

    vim output.tf
    
    output "ip" {
      value = google_compute_address.static_ip.address
      }
    
 Before running `terraform apply`, please check the following beforehand
 
     terraform validate
     
     terraform plan
    
 This should create your instance and output the assigned IP address as follows
 
![](https://github.com/DanyLan/Terraform/blob/main/Screenshot%202020-10-19%20at%2017.36.28.png)

# CHALLENGE 1

Reserve an IP address and give it a name

'terraform destroy' after you check that resource has been successfully created.

If you are stuck: [hint](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/compute_address)

# Creating a GKE cluster with variable file

The concept of the variable file is so that you do not have to edit the main file everytime. So basically the variable file will be the input to your main file in order to build your resouces.

gke_cluster.tf

<pre>provider "google" {
  version = "3.5.0"
  credentials = file(var.creds) // add your own credentials
  project = var.projectid
}
resource "google_container_cluster" "primary" {
  name               = var.cluster_name
  location           = var.location
  initial_node_count = var.initial_node_count
  master_auth {
    username = ""
    password = ""
    client_certificate_config {
      issue_client_certificate = false
    }
  }
  node_config {
    oauth_scopes = [
      "https://www.googleapis.com/auth/cloud-platform"
    ]
    metadata = {
      disable-legacy-endpoints = "true"
    }
    labels = {
      foo = "bar"
    }
    tags = ["foo", "bar"]
  }
  timeouts {
    create = "30m"
    update = "40m"
     
  }
}
</pre>

variable.tf

<pre>
variable "cluster_name" {
  default = "clusterdan"
}
variable "projectid" {
  default = "danytest1"
}
variable "initial_node_count" {
  default = 1
}
variable "location" {
  default = "us-central1-c"
}
variable "creds" {
  default = "/home/yuineng/network/xxxxxxxxxxxxxxxxxx.json"
}
</pre>

 # CHALLENGE 2
 
 The above created a zonal cluster with only one node. Try to create a regional cluster now with 3 nodes in 3 different zones. [Hint](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/container_cluster)
 
