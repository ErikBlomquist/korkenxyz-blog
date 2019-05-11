---
title: "Erik Blog Week 28."
date: 2019-04-26T12:17:21-07:00
draft: false
layout: 'posts'
tags: ["Erik Blomquist", "BEATS", "Week 28"]
---

# Create Infrastructure for StackStorm
We are going to create an infrastructure with Terraform that automatically installs StackStorm onto our instance. 

- [Variables](#variables)
    - [AWS](#aws)
    - [StackStorm](#stackstorm)
- [Main](#main)
    - [Provider](#provider)
    - [AMI](#ami)
    - [Security Groups](#security-groups)
    - [EC2 Instance](#ec2-instance)
- [Execute](#execute)


## Variables
### AWS
1. Create a new file to store our variables called `variables.tf` and open it with your favorite text editor.

2. We need to add variables for our AWS keys and we can do so with the following:

        variable "aws_access_key" {}

        variable "aws_secret_key" {}

        variable "aws_key_pair" {}

        variable "aws_private_key_pem" {}

3. Next step is to specify which region we want to use.

        variable "aws_region" {
        default = "us-west-2"
        }

4. StackStorm recommends that t2.medium is the minimum requirements for testing. We can set the instance type with the following:

        variable "aws_instance_type" {
        default = "t2.medium"
        }
    (https://docs.stackstorm.com/install/system_requirements.html)

### StackStorm
5. It is now time set our stackstorm username & password so that we can login later.

        variable "stackstorm_username" {
          default = "st2admin"
        }

        variable "stackstorm_password" {}

## Main
1. We now have all the variables that we need so the next step is to create a new file called ``main.tf``.

### Provider
2. The following code will use the variables that we defined in ``variables.tf``. 

        provider "aws" {
          access_key = "${var.aws_access_key}"
          secret_key = "${var.aws_secret_key}"
          region     = "${var.aws_region}"
        }

### AMI
3. Next step is to specify which type of AMI that we want to use for our OS. I picked ubuntu 16.04 because it was recommended by StackStorm.

        data "aws_ami" "ubuntu1604" {
        most_recent = true

        filter {
            name   = "name"
            values = ["ubuntu/images/hvm-ssd/ubuntu-xenial-16.04-amd64-server-*"]
        }

        filter {
            name   = "virtualization-type"
            values = ["hvm"]
        }

        owners = ["099720109477"] # Canonical
        }

### Security Group
4. Security Groups are needed to control the traffic to and from the instance. We are going to allow SSH and HTTPS as incoming traffic and we do so with the following:

resource "aws_security_group" "stackstorm_allow_ssh_https" {
  name        = "stackstorm_allow_ssh_https"
  description = "Allow SSH and HTTPS inbound to the StackStorm test instance"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

(You can specify the CIDR blocks with your own IP to to only allow yourself access)

### EC2 Instance
5. Last step within ``main.tf`` is to create the instance itself and then remotely installing StackStorm. 

        resource "aws_instance" "stackstorm" {
        ami           = "${data.aws_ami.ubuntu1604.id}"
        instance_type = "${var.aws_instance_type}"
        key_name      = "${var.aws_key_pair}"

        security_groups = ["${aws_security_group.stackstorm_allow_ssh_https.name}"]


        provisioner "remote-exec" {
            inline = [
            "curl -sSL https://stackstorm.com/packages/install.sh | bash -s -- --user=${var.stackstorm_username} --password='${var.stackstorm_password}'"
            ]
            
            connection {
            type        = "ssh"
            user        = "ubuntu"
            private_key = "${file(var.aws_private_key_pem)}"
            }
        }
        }

### Execute
You should now have two files (main.tf & variables.tf). CD into your projects folder where you have your terraform files and run: 

1. ``terraform init``

2. If everything looks good with no errors, you can continue to pass environment variables one at the time like this:

        export TF_VAR_aws_access_key='ACCESS-KEY-GOES-HERE'
        export TF_VAR_aws_secret_key='SECRET-KEY-GOES-HERE'
        export TF_VAR_aws_region='us-west-2'
        export TF_VAR_aws_key_pair='test'
        export TF_VAR_aws_private_key_pem='~/ChatOps/test.pem'
        export TF_VAR_stackstorm_password="abc123"

3. Make sure that you have the .pem file needed and in with the correct path.

4. ``terraform plan``
5. ``terraform apply``
6. Tada! Your instance now has StackStorm installed and configured. 


Complete code code can be found on github https://github.com/ErikBlomquist/CIT-481-BOT/tree/master/terraform
