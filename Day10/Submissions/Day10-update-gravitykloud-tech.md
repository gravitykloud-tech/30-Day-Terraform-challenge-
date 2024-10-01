
# Day 10: Terraform Loops and Conditionals

## Participant Details

- **Name:** Gus
- **Task Completed:** Learned how the Terraform arguments `count` and `for_each` are used to create multiple resources or modules based on a set of values, and how they are used in slightly different ways depending on the use case.
- **Date and Time:** 10/01/2024 12:30 PM

## Terraform Code: 
This configuration creates a iam user and instance using the count parameter
## Loop with  count
```hcl
# Loop.tf
#Note you can use count.index to get the index of each “iteration”
# IAM 
###################################################################
resource "aws_iam_user" "Matrix" {
    count = length(var.user_names)
    name  = "var.user_names${count.index}"
}
# Instance 
##################################################################
resource "aws_instance" "app_server" {
  ami           = "ami-0e64c0b934d72ced5"
  instance_type = "t2.micro"
  count = 1

  tags = {
    Name = "ExampleAppServerInstance"
  }
}
```
## Creating 3 IAM Users with `for_each` Argument Loops
In this configuration,three IAM users are created using the `for_each` argument, `user_names` variables. Additionally outputing all user ARNs
## Loop with for_each Expression
```hcl
#Note The for_each expression allows you to loop over lists, sets, and maps, it becomes a map of resources, rather than just one resource
# for_each.tf
#################################################################################
resource "aws_iam_user" "Matrix" {
    for_each = length(var.user_names)
    name  = "ecach.value"
}
#################################################################################
}
# variables.tf
#This file defines iam usernames
#################################################################################
# Terraform will create three IAM users, each with a unique, readable name:
variable "user_names" {
description = "Create IAM users with these names"
type = list(string)
default = ["neo", "trinity", "morpheus", ]
}
#################################################################################
#Output the ARNs of those users as follows:
output "all_users" {
value = aws_iam_user.Matrix[*].arn
}
```
## Refactor my existing Terraform code to use loops and basic conditional expressions.
This configuration creates two seprate environments based on the users input, The user declares a variable that utalizes boolean type that is either true or false
## Note-Conditional_expression =>condition ? true_value : false
```hcl
# Dev
##################################################################
# ec2_instance.tf
resource "aws_instance" "dev" {
    count = var.is_dev_env == true ? 1 : 0
    ami = "ami-0e64c0b934d72ced5"
    instance_type = "t2.nano"
    tags = {
      Name = "dev-ec2"
    }
}
# Prod
####################################################################
resource "aws_instance" "prod" {
    count = var.is_dev_env == true ? 1 : 0
    ami = "ami-0e64c0b934d72ced5"
    instance_type = "t2.micro"
    tags = {
      Name = "prod-ec2"
    }
}
# Variable.tf # bool
###################################################################
variable "is_dev_env" {
  type = bool
  default = false
}
```
