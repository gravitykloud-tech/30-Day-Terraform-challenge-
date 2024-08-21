# Day 3: Deployying basic infrastructure with Terrafrom

## Participant Details
- **Name:** gus
- **Task Completed:** Branch name:Day3-Deploying-basic-infra-with-Terraform
Create new file Day3-update-grvitykloud-tech.md
Deploying a Single Server

- **Date and Time:** 08-21-2024

  provider "aws" {
  region = "us-west-1"
  profile ="dev-profile"
}

resource "aws_instance" "Terraform_Instance_AMI" {
  ami           = "ami-0e64c0b934d72ced5"
  instance_type = "t2.micro"

  tags = {
    Name = "Terraform Instance AMI"
  }
}
