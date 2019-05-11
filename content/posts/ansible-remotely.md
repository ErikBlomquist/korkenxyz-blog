---
title: "Erik's Blog Week 25"
date: 2019-04-05T10:11:03-07:00
draft: false
tags: ["Erik Blomquist", "BEATS", "Week 25"]
---

## How To Run Ansible Playbooks Remotely
Ansible is a great automation platform that allows you to control and configure multiple instances from your local machine without having to login to them. 

- [Pre-req](#pre-req)
- [Install Ansible](#install-ansible)
- [Hosts file](#hosts-file) 
- [Test Playbook](#test-playbook)

### Pre-req
I have one Amazon Linux instance running that I will make changes to. You'll need the following:
- Public IP to your instance
- PEM key 

### Install Ansible
Make sure that your running instance have Ansible installed already or run the following: 

    sudo amazon-linux-extras install ansible2


### Hosts file
The hosts file allows us to categorize our hosts into groups so that we can run a playbook on all hosts belonging to a group. It varies where your hosts file is located depending how you installed Ansible. I'm going to create a new folder called `Ansible` inside my projects folder and also create a new file called `hosts.ini` inside that folder.

        mkdir Ansible
        touch hosts.ini

Open the newly created file with the vi editor and add the following:

        vi Ansible/hosts.ini

Press `i` to enter edit mode.


    [NAME-OF-GROUP-GOES-HERE]
    IP-ADDRESS ansible_ssh_private_key_file=PATH-TO-PEM-FILE


In my case:

    [test]
    54.148.168.64 ansible_ssh_private_key_file=ansible-test.pem

Press `esc` and then `:` followed by `x` in order to save the changes.

We can simply repeat the process if we want to add more hosts/groups to control. Like this:

    [test]
    54.148.168.64 ansible_ssh_private_key_file=ansible-test.pem
    54.148.168.65 ansible_ssh_private_key_file=ansible-test.pem

### Test Playbook
Next step is to create a simple Ansible playbook that creates a new folder so that we can see our changes. Create a new file called `test.yml` and open it with the vi editor.

    touch test.yml 
    vi test.yml

Add the following and save the file: 

    ---
    - hosts: test
    become: true
    tasks:
        - name: Test
            shell: |
            mkdir /home/ec2-user/test
Ansible reads the hosts file and applies these changes to all IPs belonging to the group specified, the group `test` in our case. It is now time to run our test.yml script to see if things are working properly. From your projects folder, run the following:

    ansible-playbook test.yml -i ansible/hosts.ini --user=ec2-user

Your output should be similar to this.


    54.148.168.64              : ok=2    changed=1    unreachable=0    failed=0   

Yay, you just made a new folder on your instance without even logging in!