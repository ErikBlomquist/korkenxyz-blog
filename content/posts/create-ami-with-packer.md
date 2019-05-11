---
title: "How To Create A Customized AMI with Packer"
date: 2019-04-19T11:52:38-07:00
draft: false
layout: 'posts'
tags: ["Erik Blomquist", "BEATS", "Week 27"]
---

- [Download Packer](#download-packer)
- [Template](#template)
- [Validate and Build](#validate-and-build)


## Download Packer
Packer is a lightweight tool that is used to create pre-configured operating systems also called images (AMIs). You can download packer using the link or alternative methods below.

https://www.packer.io/downloads.html

**Alternative methods**

-  OSX: ``brew install packer``
-  Windows: ``choco install packer``

## Template
Packer uses templates in the format of JSON in order to configure and create images. 
- **variables** are necessary so that we can pass our AWS credentials for authentication.
- **builders** block is responsible for creating the AMI. You can specify which region, base image, names, ssh settings and much more.

1. Start by creating a new file called ``packer.json`` and paste the following and save the file:

        {
        "variables": {
            "aws_access_key": "",
            "aws_secret_key": ""
        },
        "builders": [{
            "type": "amazon-ebs",
            "access_key": "{{user `aws_access_key`}}",
            "secret_key": "{{user `aws_secret_key`}}",
            "region": "us-west-2",
            "source_ami_filter": {
            "filters": {
                "virtualization-type": "hvm",
                "name": "ubuntu/images/*ubuntu-xenial-16.04-amd64-server-*",
                "root-device-type": "ebs"
            },
            "owners": ["099720109477"],
            "most_recent": true
            },
            "instance_type": "t2.micro",
            "ssh_username": "ubuntu",
            "ami_name": "packer-example {{timestamp}}"
        }]
        }


## Validate and Build
Next step is to validate our file to make sure everything is correct and then build the AMI.

### Validate

        packer validate packer.json
        Template validated successfully.

### Build
    packer build packer.json

If you have not configured your AWS credentials you can pass them like this:

    packer build \
        -var 'aws_access_key=YOUR ACCESS KEY' \
        -var 'aws_secret_key=YOUR SECRET KEY' \
        packer.json

You should get a similar message as the one below after you run the build command. 

    ==> Builds finished. The artifacts of successful builds are:
    --> amazon-ebs: AMIs were created:
    us-west-2: ami-026944ee72e527fce

Good job! You can now find your new AMI when you logon to your AWS console -> EC2 -> AMIs.