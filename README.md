## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Network Diagram](https://github.com/arice1606/Cybersecurity-Project-One/blob/main/Diagrams/Elk%20Azure%20Network%20Diagram.drawio.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the [Ansible](https://github.com/arice1606/Cybersecurity-Project-One/tree/main/Ansible) file may be used to install only certain pieces of it, such as Filebeat.

  - [DVWA-Playbook](https://github.com/arice1606/Cybersecurity-Project-One/blob/main/Ansible/DVWA-Playbook.yml)
  - [ELK-Playbook](https://github.com/arice1606/Cybersecurity-Project-One/blob/main/Ansible/elk-playbook.yml)
  - [Filebeat-Playbook](https://github.com/arice1606/Cybersecurity-Project-One/blob/main/Ansible/filebeat-playbook.yml)
  - [Metric-Playbook](https://github.com/arice1606/Cybersecurity-Project-One/blob/main/Ansible/metricbeat-playbook.yml)

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
- Load balancers protect the availability of an application. They are placed infront of a backend pool and distribute traffic evenly among these servers to make sure that servers do not become overloaded. 

- Implementing a jump box prevents the underlying architecture from being exposed to the public, in turn shrinking your attack surface. Even if you SSH into the jump box with your own ssh key, this key does not allow you to SSH into the web servers. This can only be accomplished using a separate docker container which has its own SSH key.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the metrics and system logs.
- Filebeat watches for suspicious changes to files and when those changes occurred.
- Metricbeat records different metrics about the machine, such as uptime and CPU usage.

The configuration details of each machine may be found below.

| Name                 | Function       | Public IP Address | Private IP Address | Operating System |
|----------------------|----------------|-------------------|--------------------|------------------|
| Jump-Box-Provisioner | Gateway        | 40.117.91.21      | 10.0.0.8           | Linux            |
| Web-1                | Web Server     | 40.117.170.84     | 10.0.0.6           | Linux            |
| Web-2                | Web Server     | 40.117.170.84     | 10.0.0.7           | Linux            |
| ELK-VM               | Log Monitoring | 13.64.152.202     | 10.3.0.4           | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump-Box-Provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- My public IP address

Machines within the network can only be accessed by Ansible on the Jump-Box-Provisioner.
- The ELK VM, Web-1, and Web-2 can only be accessed by SSH via the Ansible docker container which is running on the Jump-Box-Provisioner.

A summary of the access policies in place can be found in the table below.

| Network Security Group | Name              | Port | Protocol | Source       | Destination    |
|------------------------|-------------------|------|----------|--------------|----------------|
| RedTeamNSG             | SSH to Jump-Box   | 22   | TCP      | My public IP | 10.0.0.8       |
| RedTeamNSG             | SSH from Jump-Box | 22   | TCP      | 10.0.0.8     | VirtualNetwork |
| RedTeamNSG             | RedTeam-Host2VN   | 80   | TCP      | My public IP | VirtualNetwork |
| ELK-VM-NSG             | TCP_5601          | 5601 | TCP      | My public IP | 10.3.0.4       |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- You can configure multiple machines using the same playbook
- Each machine will be configured the same way using this playbook

The playbook implements the following tasks:
- Installing Docker.io, python3-pip, and Docker module
- Increase virtual memory in current status and reload
- Download and launch a Docker ELK container

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Docker ps](https://github.com/arice1606/Cybersecurity-Project-One/blob/main/Images/Elk%20docker%20ps.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- 10.0.0.6 - Web-1
- 10.0.0.7 - Web-2

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat collects system log data from the VMs that it is monitoring. Examples of exported fields include authorization logs and events created by the sudo command. 
- Metricbeat records different metrics about the machine, such as uptime and CPU usage.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat-playbook.yml, metricbeat-playbook.yml, and elk-playbook.yml files to /etc/ansible/roles.
- Copy the filebeat-config.yml and metricbeat-config.yml to /etc/ansible/files
- Update the /etc/ansible/hosts file to include the private IP addresses of your webservers and add ansible_python_interpreter=/usr/bin/python3 after the IP addresses.
- While in the hosts file, also add a group called [elk] and add the IP address of this server with ansible_python_interpreter=/usr/bin/python3 after the IP address. 


- Run the DVWA-Playbook.yml and navigate to http://[load.balancer.public.ip]/setup.php to check that the installation worked as expected.

If successful, you should see something like this:

![DVWA](https://github.com/arice1606/Cybersecurity-Project-One/blob/main/Images/DVWA.png)

- Run the playbook elk-playbook.yml and navigate to http://[elk.server.public.IP]:5601/app/kibana to check that the installation worked as expected.

If successful, you should see something like this:

![Kibana](https://github.com/arice1606/Cybersecurity-Project-One/blob/main/Images/Kibana%20Home.png)

- Run the playbook filebeat-playbook.yml and navigate to http://[elk.server.public.IP]:5601/app/kibana#/home/tutorial/systemLogs. Click the "check data" button at the bottom of the page to check that the installation worked as expected.

If successful, you should see something like this:

![SysLogs](https://github.com/arice1606/Cybersecurity-Project-One/blob/main/Images/Kibana%20Home.png)

- Run the playbook metricbeat-playbook.yml and navigate to http://[elk.server.public.IP]:5601/app/kibana#/home/tutorial/dockerMetrics. Click the "check data" button at the bottom of the page to check that the installation worked as expected.

If successful, you should see something like this:

![Metrics](https://github.com/arice1606/Cybersecurity-Project-One/blob/main/Images/Kibana%20Metricbeat%20Dashboard.png)
