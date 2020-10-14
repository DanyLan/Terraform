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

We are going to work on Centos. Go to root then run the following updates and [download the 64 bit version](https://www.terraform.io/downloads.html)

    sudo -s

    yum update -y
    
    yum install wget
    
    wget https://releases.hashicorp.com/terraform/0.13.4/terraform_0.13.4_linux_amd64.zip
    
    yum install unzip
    
    unzip terraform_0.13.4_linux_amd64.zip
    
Now we are going to move the terraform package to downloads directory

    mkdir downloads
    
    mv terraform downloads/
    
After that we are going to edit .bash_profile in order to setup our environment to use terraform

    # .bash_profile

    # Get the aliases and functions
    if [ -f ~/.bashrc ]; then
            . ~/.bashrc
    fi

    # User specific environment and startup programs

    PATH=$PATH:$HOME/.local/bin:$HOME/bin

    export PATH
    export PATH="$PATH:/home/[name]/downloads"
   
Now run terraform to see if application is working

    source .bash_profile
    
    terraform
    
# Create a Service account

Create a service account (you may give it owner/editor role) and upload key to VM this way. Then move the key to a folder of your choice.

![](https://github.com/DanyLan/Terraform/blob/main/Screenshot%202020-10-09%20at%2014.51.44.png)

# Create a Service account

Create the main.tf file.

    provider "google" {
      version = "3.5.0"

      credentials = file("<NAME>.json")

      project = "<PROJECT_ID>"
      region  = "us-central1"
      zone    = "us-central1-c"
    }

    resource "google_compute_network" "vpc_network" {
      name = "terraform-network"
    } 
    
    
    
    

    
    
    
    
    
