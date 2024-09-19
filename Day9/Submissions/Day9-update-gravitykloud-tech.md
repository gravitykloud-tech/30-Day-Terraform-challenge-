## Participant Details
- **Name:** gus
- **Task Completed:** 
- **Date and Time:** 9/10/2024 15:30 PM

 # Create nested modules across multiple environments.

Embedding one or more submodule inside your current code base enables you to cleanly separate logical components of the primary module or to create a reusable code block that can be invoked multiple times during the execution of the calling module. In the following example, ec2-instance is an embedded module that the root main.tf can reference.
```hcl
root-module-directory
├── README.md
├── main.tf
└── ec2-instances
    └── main.tf
```
## Adding advanced features to reuse modules for versioning across multiple environments.

```hcl
#Terraform
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
    }
  }
}
# Terraform configuration
provider "aws" {
  region  = "us-west-1"
  profile = "Admin"
}
# Create EC2 instance
module "ec2_instances" {
  source  = "terraform-aws-modules/ec2-instance/aws"
  version = "~> 4.3.0" # Use the latest version
  count   = 1

  name = "my-terraform-ec2-instance"

  ami           = "ami-0e64c0b934d72ced5"
  instance_type = "t2.micro"

  tags = {
    Terraform   = "true"
    Environment = "dev"
  }
}
# Create an S3 bucket with versioning enabled and server-side encryption by default

resource "aws_s3_bucket" "terraform-state-bucket" {
bucket = "my-terraform-state-bucket007"
# Prevent accidental deletion of this S3 bucket
lifecycle {
prevent_destroy = false
}
}
#Terraform to store the state in my S3 bucket

terraform {
backend "s3" {
# Replace this with your bucket name!
bucket = "my-terraform-state-bucket007"
key = "global/s3/terraform.tfstate"
region = "us-west-1"
# Replace this with your DynamoDB table name!
dynamodb_table = "terraform-up-and-running-locks"
encrypt = true
}
}

# Enable versioning so you can see the full revision history of your state files

#resource "aws_s3_bucket_versioning" "enabled" {
#bucket = "aws_s3_bucket.terraform_state.id"
#versioning_configuration {
#status = "Enabled"
#}
#}

# Enable server-side encryption by default

#resource "aws_s3_bucket_server_side_encryption_configuration" "enabled" {
#bucket = "aws_s3_bucket.terraform_state.id"
#rule {
#apply_server_side_encryption_by_default {
#sse_algorithm = "AES256"
#}
#}
#}
# Explicitly block all public access to the S3 bucket

#resource "aws_s3_bucket_public_access_block" "public_access" {
#bucket = "aws_s3_bucket.terraform_state.id"
#block_public_acls = true
#block_public_policy = true
#ignore_public_acls = true
#restrict_public_buckets = true
#}

#DynamoDB table to use for locking.

resource "aws_dynamodb_table" "terraform_locks" {
name = "terraform-up-and-running-locks"
billing_mode = "PAY_PER_REQUEST"
hash_key = "LockID"
attribute {
name = "LockID"
type = "S"
}
}
# These variables will print out the Amazon Resource Name (ARN) of your S3 bucket and the name of your DynamoDB table

output "s3_bucket_arn" {
value = "arn:aws:s3:::my-terraform-state-bucket007/global/s3/terraform.tfstate"
description = "The ARN of the S3 bucket"
}
output "dynamodb_table_name" {
value = "terraform-up-and-running-locks"
description = "The name of the DynamoDB table"
}
