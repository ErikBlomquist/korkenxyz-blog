---
title: "How to Install a LAMP stack with Ansible and Docker"
date: 2019-03-08T10:19:16-08:00
draft: false
layout: 'posts'
---

We are going to create a simple Ansible script that install a LAMP stack + Composer. LAMP stands for:
- Linux
- Apache
- MySQL
- PHP

### Create and Login to Docker Container
The LAMP stack will be installed inside a Docker container that runs on Ubuntu so we first need to create that container.

1. Start Docker 
2. To create a new Docker container named lamp. Open your terminal and run:

        $ docker run -it -p 8080:80 --name lamp ubuntu:latest bash



3. Login to your newly created container. 

        $ docker exec -it lamp /bin/bash

4. You should now be logged in as root. 

        $ root@9ede87479cea:/#

### Install Ansible
Once logged in to the Docker container, we need to install Ansible. We can do that by running the following commands:

1. You should now be logged in as root. 

        $ apt-get update
        $ apt-get install software-properties-common
        $ apt-add-repository --yes --update ppa:ansible/ansible
        $ apt-get install ansible
        
### Create Ansible Playbook
It's time to create our playbook. 

1. Create a new file called **lamp.yml**.

        touch lamp.yml

2. Open your new file.

        vi lamp.yml

3. Paste the following:

        --- 
        # Lamp Stack
        - hosts: localhost
        tasks:
            - name: Install LAMP stack
            become: yes
            apt: 
                pkg:
                - apache2
                - mysql-client
                - php7.2
                state: present
                update_cache: yes

            - name: Start Apache2 Service
            become: yes
            service:
                name: apache2
                state: started
                enabled: yes

            - name: Download and Install Composer
            shell: |
                php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
                sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
                rm composer-setup.php

4. This code downloads and installs apache2, mysql-client, php7.2 and composer. Apache2 is also started so. 

5. If you are using the vi editor, press **esc** and then type **:x** to save and exit.

### Run Ansible Playbook
1. It's now time to run our playbook and see if everything works.

        ansible-playbook --connection=localhost lamp.yml --ask-sudo-pass

2. If everything ran without any errors, you should be able to enter http://localhost:8080/ in your browser and you'll see the default apache page. Good job!