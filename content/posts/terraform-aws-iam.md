---
title: "Install and Setup Terraform with AWS"
date: 2019-02-05T14:49:54-08:00
draft: false
tags: ["terraform", "aws", "iam"]
menu: "blog"
---

### Table of contents
- Install and Setup Terraform with AWS
  - Download and Install Terraform
  - Link AWS IAM
  - Build a VPC
  - Create subnets
  - Create an Internet Gateway

## Install and Setup Terraform with AWS
### Download and Install Terraform
  1. Download the binary package from:
     https://www.terraform.io/downloads.html
  2. Place unzip the content into a new folder called "Terraform" under C:\ for example.
  3. cd into your new folder and type `terraform version` and you should get a similar output.

          c:\>cd C:\Terraform
          C:\Terraform>
          C:\Terraform>terraform version
          Terraform v0.11.8

  4. You now have Terraform installed!

### Link AWS IAM 
This step assume that you:
- Have an IAM user setup with Access Key and Secret Key (https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html). 
- Read about AWS IAM Best Practices (https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#lock-away-credentials)
- Have AWS CLI installed. (https://docs.aws.amazon.com/signer/latest/developerguide/gs-cli.html)

1. You need to link your IAM user to Terraform and you do that by open your aws cli and type `aws configure` and fill out the information. Replace **[YOUR ACCESS KEY]** and **[YOUR SECRET KEY]** for your IAM user.

          $ aws configure
          AWS Access Key ID [None]: [YOUR ACCESS KEY]
          AWS Secret Access Key [None]:  [YOUR SECRET ACCES KEY]
          Default region name [None]: us-west-2
          Default output format [None]: json

### Build a VPC
  1. Once Terraform has been successfully installed and linked to your aws IAM account you are ready to go. Create a new file inside your terraform folder and call it `main.tf`.
  2. Add the following code to `main.tf` and save it.
  
          # VPC
          resource "aws_vpc" "main" {
          	cidr_block = "172.31.0.0/16"
          	  tags {
          		Name = "Main-VPC"
          	}
          }

  3. Open your cmd and navigate to C:\Terraform and then type `terraform apply` and then `yes` to run all of your .tf files inside your Terraform folder.

          Do you want to perform these actions?
          Terraform will perform the actions described above.
          Only 'yes' will be accepted to approve.

          Enter a value: yes
  4. You should now be able to sign in to your aws console and find a VPC there.


### Create subnets
   1. Here is how you create 3 public and 3 private subnets in different availability zones.

           resource "aws_subnet" "privsubnet1" {
             vpc_id     = "${aws_vpc.main.id}"
             cidr_block = "172.31.32.0/19"
             availability_zone       = "us-west-2a"
             tags {
               Name = "Privsubnet1"
             }
           }

           resource "aws_subnet" "privsubnet2" {
             vpc_id     = "${aws_vpc.main.id}"
             cidr_block = "172.31.64.0/19"
             availability_zone       = "us-west-2b"
             tags {
               Name = "Privsubnet2"
             }
           }

           resource "aws_subnet" "privsubnet3" {
             vpc_id     = "${aws_vpc.main.id}"
             cidr_block = "172.31.96.0/19"
             availability_zone       = "us-west-2c"
             tags {
               Name = "Privsubnet3"
             }
           }

           resource "aws_subnet" "pubsubnet1" {
             vpc_id     = "${aws_vpc.main.id}"
             cidr_block = "172.31.128.0/19"
             availability_zone       = "us-west-2a"
             tags {
               Name = "Pubsubnet1"
            }
           }

           resource "aws_subnet" "pubsubnet2" {
             vpc_id     = "${aws_vpc.main.id}"
             cidr_block = "172.31.160.0/19"
             availability_zone       = "us-west-2b"
             tags {
               Name = "Pubsubnet2"
             }
           }

           resource "aws_subnet" "pubsubnet3" {
             vpc_id     = "${aws_vpc.main.id}"
             cidr_block = "172.31.192.0/19"
             availability_zone       = "us-west-2c"
             tags {
               Name = "Pubsubnet3"
             }
        
### Create an Internet Gateway
An Internet Gateway is need to allow instances in our VPC to communicate with the internet. 

           resource "aws_internet_gateway" "gw" {
              vpc_id = "${aws_vpc.main.id}"
            }

