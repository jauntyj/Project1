## Automated ELK Stack Deployment
The files in this repository were used to configure the network depicted below.
Images/ Project1_rev2.pdf
These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the _____ file may be used to install only certain pieces of it, such as Filebeat.
Playbook to install Elk docker:
---
- name: Configure Elk VM with Docker
  hosts: elkservers
  remote_user: elk
  become: true
  tasks:
    # Use apt module
  - name: Install docker.io
    apt:
      force_apt_get: yes
      name: docker.io
      state: present

    # Use apt module
  - name: Install python-pip
    apt:
      force_apt_get: yes
      name: python-pip 
      state: present 

    # Use pip module
  - name: Install Docker module
    pip:
      name: docker 
      state: present

    # Use command module
  - name: Increase virtual memory
    command: sysctl -w vm.max_map_count=262144

    # Use docker_container module
  - name: download and launch a docker elk container
    docker_container:
      name: elk
      image: sebp/elk
      state: started
    # Please list the ports that ELK runs on
      published_ports:
        - 5601:5601
        - 9200:9200
        - 5044:5044

Playbook to install Filebeat:
---
- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

  - name: download filebeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb
 
  - name: install filebeat deb
    command: dpkg -i filebeat-7.6.1-amd64.deb

  - name: drop in filebeat.yml 
    copy:
      src: /etc/ansible/files/filebeat.yml
      dest: /etc/filebeat

  - name: enable and configure system module
    command: /usr/bin/filebeat modules enable system

  - name: setup filebeat
    command: /usr/bin/filebeat setup

  - name: start filebeat service
    command: /usr/bin/filebeat -e


Playbook to install Metricbeat:
---
- name: installing and launching metricbeat
  hosts: webservers
  become: yes
  tasks:

  - name: download metricbeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.6.1-amd64.deb
 
  - name: install metricbeat deb
    command: dpkg -i metricbeat-7.6.1-amd64.deb

  - name: drop in metricbeat.yml 
    copy:
      src: /etc/ansible/files/metricbeat.yml
      dest: /etc/metricbeat/

  - name: enable and configure system module
    command: /usr/bin/metricbeat modules enable system

  - name: setup metricbeat
    command: /usr/bin/metricbeat setup

  - name: start metricbeat service
    command: /usr/bin/metricbeat –e

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly _avilable____, in addition to restricting _access____ to the network.
- _TODO: What aspect of security do load balancers protect? What is the advantage of a jump box?_
Load balancer protect the internal web server and allow traffic from the firewall to only traverse from defined source (IP or subnet) to destination load balanced server depend of availability of port 80 and weightage used by load balancer.
Jump box allow us to install and configure the internal server without exposing all the ports to the outer world.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the _files____ and system _logs____.
- _TODO: What does Filebeat watch for?
Filebeat watch for system log files
- _TODO: What does Metricbeat record?_
Metricbeat record the system parameter like cpu memory, system summery and much more.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     		| Function | IP Address | Operating System |
|----------		|----------		|------------|------------------|
| Jump Box 		| Gateway  		| 10.0.0.4   		| Linux            |
| LB    			|       Load Balancer   	|      52.240.56.158      	|       Azure Load Balancer        |
| DVWA-VM1     	| dvwa docker         	|         10.0.0.5            	|      Ubuntu Linux 18.04            |
| DVWA-VM2          	|      dvwa docker         	|   10.0.0.6                  	|     Ubuntu Linux 18.04 	    |
| DVWA-VM3          	|      dvwa docker         	|   10.0.0.7                  	|     Ubuntu Linux 18.04 	    |
| DVWA-VM4          	|      ELK agent client      	|   10.0.0.9                  	|     Ubuntu Linux 18.04 	    |
| DVWA-VM4          	|      ELK Server      	|   10.0.0.8                  	|Ubuntu 18.04/ELK docker  |


### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jumpbox, ELK Server & Load Balancer machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
99.254.30.145

Machines within the network can only be accessed by ssh.
My Client machine - 99.254.30.145

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes              | 99.254.30.145|
|       LB   |    Yes                 |        99.254.30.145              |
|       Elk   |      yes               |       99.254.30.145               |
|       dvwa-vm1   |      no              |      |
|       dvwa-vm1      |      no               |       |
|       dvwa-vm1     |      no              |       |
|       dvwa-vm1      |      no               |       |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
Advantage of using Ansible is it allow us to deploy a playbook which is consistent across the whole environment so if we are deploying multiple servers the same playbook can be used to deploy all the required component with a single play and all the servers will be exactly identical. Ansible also help us in doing any modification or upgrade to existing servers with just one playbook.

The playbook implements the following tasks:
Install the docker
Install the python pip
Install docker python module on pip
Install dvwa container  ...
- ...

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.


### Target Machines & Beats
This ELK server is configured to monitor the following machines:
10.0.0.9
We have installed the following Beats on these machines:
Filebeat and metricbeat

These Beats allow us to collect the following information from each machine:
Filebeat is configured to collect system file logs written by sys log server
Metricbeat is configured to collect internal metrics from kabana for cpu and memory

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the __filebeat.yml___ file to ___/etc/filebeat folder__.
- Update the __filebeat.yml___ file to include...kabana server IP address and port to connect to kabana server in this case it is 10.0.0.8 port 9200

Copy the __metricbeat.yml___ file to ___/etc/metricbeat folder__.
- Update the __metricbeat.yml___ file to include...kabana server IP address and port to connect to kabana server in this case it is 10.0.0.8 port 9200

- Run the playbook, and navigate to ____ to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?
We copy the metricbeat.yml and filebeat.yml in the respective /etc/filebeat and /etc/metricbeat folder_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?
Filebeat.yml and metricbeat.yml were modified with elk server ip address and port and dropped on the client machine which connects to elk server
Filebeat-playbook.yml and metricbeat-playbook.yml where the playbook that were downloading the agent(filebeat and metricbeat) from web to client machine dvwa-vm4 and running the agent.
- _Which URL do you navigate to in order to check that the ELK server is running?
http://52.240.58.3:5601

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc
- name: drop in filebeat.yml 
    copy:
      src: /etc/ansible/files/filebeat.yml
      dest: /etc/filebeat
 

  - name: drop in metricbeat.yml 
    copy:
      src: /etc/ansible/files/metricbeat.yml
      dest: /etc/metricbeat/
