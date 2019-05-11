---
title: "Beats Erik Week 26"
date: 2019-04-12T18:40:23-07:00
draft: false
tags: ["Erik Blomquist", "BEATS", "Week 26"]
---

# How To Install Prometheus with Ansible on Amazon Linux.

## Prometheus
We have previously downloaded, installed and configured Prometheus manually and it was a little tidious. We are going to create a playbook with Ansible that automates the process for us.

1. Create a new file with the name `setup_prometheus.yml` and add the following to update the yum repo and to create a prometheus user:

        ---
        - hosts: all
        become: true
        tasks:
            - name: Update yum repo
                yum:
                name: '*'
                state: latest

            - name: Create Prometheus user
                shell: |
                useradd --no-create-home --shell /bin/false prometheus
                mkdir /etc/prometheus
                mkdir /var/lib/prometheus
                chown prometheus:prometheus /etc/prometheus
                chown prometheus:prometheus /var/lib/prometheus

2. Next step is to download, extract and install prometheus and we can do that like this:

            - name: Download file from a file path
                get_url:
                url: https://github.com/prometheus/prometheus/releases/download/v2.3.2/prometheus-2.3.2.linux-amd64.tar.gz
                dest: /home/ec2-user/prometheus-2.3.2.linux-amd64.tar.gz

            - name: Extract tar
                unarchive:
                src: /home/ec2-user/prometheus-2.3.2.linux-amd64.tar.gz
                dest: /home/ec2-user/

            - name: Move Prometheus
                shell: |
                mv /home/ec2-user/prometheus-2.3.2.linux-amd64 prometheus-files

            - name: Move files
                shell: |
                cp prometheus-files/prometheus /usr/local/bin/
                cp prometheus-files/promtool /usr/local/bin/
                cp prometheus-files/promtool /usr/local/bin/
                chown prometheus:prometheus /usr/local/bin/prometheus
                chown prometheus:prometheus /usr/local/bin/promtool
                cp -r prometheus-files/consoles /etc/prometheus
                cp -r prometheus-files/console_libraries /etc/prometheus
                chown -R prometheus:prometheus /etc/prometheus/consoles
                chown -R prometheus:prometheus /etc/prometheus/console_libraries


3. We are going to move the configuration file for Prometheus that we are going to create later.

            - name: Move Prometheus.yml
                shell: |
                mv /home/ec2-user/prometheus.yml /etc/prometheus/prometheus.yml


4. We now need to change ownerships of the Prometheus files and also setup Prometheus as a service. The second part reloads and starts prometheus. 

            - name: Change ownership and Setup Service File
                shell: |
                chown prometheus:prometheus /etc/prometheus/prometheus.yml
                cp /home/ec2-user/prometheus.service /etc/systemd/system/prometheus.service

            - name: Reload Prometheuse to register changes
                shell: |
                systemctl daemon-reload
                systemctl start prometheus
                systemctl status prometheus

5. Create a new file called `prometheus.yml` inside your projects folder and add the following:

        global:
        scrape_interval: 10s
        
        scrape_configs:
        - job_name: 'prometheus'
            scrape_interval: 5s
            static_configs:
            - targets: ['localhost:9090']

6. Good job! You should now be able to run your playbook and then visit http://localhost:9090 once your prometheus server is up and running.

        ansible-playbook setup_prometheus.yml