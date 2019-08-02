---
title: "Setup Terraform"
date: 2019-07-01T14:49:54-08:00
draft: false
tags: ["terraform", "aws"]
---
## Setup Terraform 

- [Windows 10](#windows-10)
- [Mac](#windows-10)
- [Linux](#windows-10)
- [Check Version](#check-version)

### Windows 10
1. Download Terraform
from https://www.terraform.io/downloads.html
2. Extract the file you downloaded to **c:/terraform**
3. Setup path

    3.1 Open up **Control Panel** -> **System and Security** -> **System** -> **Advanced system settings** -> **Environment Variables...**

    3.2 Under System variables, select **Path** and then click **Edit**

    3.3 Click **New** and enter **C:\terraform** in the new field and then **OK** to close down your windows.

### Mac	
1. Download and Install
https://www.terraform.io/downloads.html or install it with brew from your terminal

    ```
    $ brew install terraform
    ```

2. Extract files **$HOME/Downloads/terraform**
3. Copy files
    ```
    cp $HOME/Downloads/terraform /usr/local/bin/
    ```

### Linux
1. Download latest version

    ```
    $ wget https://releases.hashicorp.com/terraform/0.xx.x/terraform_0.xx.x_linux_amd64.zip
    ```

2. Install Unzip

    ```
    $ sudo apt-get install unzip
    ```

3. Unzip file
    
    ```
    $ unzip terraform_0.11.1_linux_amd64.zip
    ```

4. Move file

    ```
    $ sudo mv terraform /usr/local/bin/
    ```

### Check Version 
Open your command-line and type the following command to see which version you have installed.
```
$ terraform -version
Terraform v0.12.6
```


