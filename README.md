# Elk-Stacks-Repository
Elk Stacks Repository for Cybersecurity
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

https://drive.google.com/file/d/1NtJKVKFVulbJh2MDPsa5m7QrmUa8LECv/view?usp=sharing

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the _____ file may be used to install only certain pieces of it, such as Filebeat.

---
- name: Configure Elk VM with Docker
  hosts: elk
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module (It will default to pip3)
    - name: Install Docker module
      pip:
        name: docker
        state: present

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        # Please list the ports that ELK runs on
        published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044


---
- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

  - name: download filebeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb

  - name: install filebeat deb
    command: dpkg -i filebeat-7.4.0-amd64.deb

  - name: drop in filebeat.yml
    copy:
      src: /etc/ansible/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

  - name: enable and configure system module
    command: filebeat modules enable system

  - name: setup filebeat
    command: filebeat setup

  - name: start filebeat service
    command: service filebeat start


This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
Load balancers protect the availability of servers by making sure that servers are available whenever needed. They restrict unsecure connections and allow secure connections to and from the public internet.
The advantage of a jump box is to funnel all conections through the jump box. Jump boxes also allow infrasture modifications to go through smoothly and without issues arising. This also limits the attack service by only exposing one machine to the public internet.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the metirs and system traffic.
Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing.
Metricbeat takes the metrics and statistics of the machines and sends the data to you.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| Web-1    |  Server  | 10.0.0.5   | Linux            |
| Web-2    |  Server  | 10.0.0.7   | Linux            |
| Web-3    |  Server  | 10.0.0.6   | Linux            |
| ELK      |  Monitor | 10.2.0.4   | Linux            |
### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jumb box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
 IP addresses that are whitelisted: 66.190.137.83

Machines within the network can only be accessed by Jump box.
Only the Jump box can access the machines. The IP for the Jump box is 10.0.0.4.
A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 66.190.137.83        |
| Web-1    | No                  | 10.0.0.4             |
| Web-2    | No                  | 10.0.0.4             |
| Web-3    | No                  | 10.0.0.4             |
| ELK      | No                  | 10.0.0.4             |
### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
 The main advantage to using automated configuration with ansible is that it allows one to put multiple commands into several servers from one playbook.

The playbook implements the following tasks:

1. install: docker.io
2. install: python-pip
3. install: docker
4. command sysctl -w vm.max_map

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

Elk-project-sebp-pic.png

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
_
Web-1 10.0.0.5, Web-2 10.0.0.7, Web-3 10.0.0.6

We have installed the following Beats on these machines:
I successfully installed Filebeats onto all three machines.

These Beats allow us to collect the following information from each machine:

Filebeat collects the changes done to the system files and logs.
### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the install_elk.yml file to /etc/ansible/roles.
- Update the hosts file to include...
 [webservers]
## alpha.example.org
## beta.example.org
## 192.168.1.100
## 192.168.1.110
10.0.0.6 ansible_python_interpreter=/usr/bin/python3
10.0.0.5 ansible_python_interpreter=/usr/bin/python3
10.0.0.7 ansible_python_interpreter=/usr/bin/python3

 [elk]
10.2.0.4 ansible_python_interpreter=/usr/bin/python3

- Run the playbook, and navigate to https://www.[elkboxpublicIP]:5607/app/kibana to check that the installation worked as expected.


The playbook file is /etc/ansible/file/filebeat-configuration.yml.
The file you update to make Ansible run is /etc/ansible/host and you add the webserver/ elk server to the IP addresses.
The address to check that the Elk server is running is is www.[elkboxpublicIP]:5607/app/kibana

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
