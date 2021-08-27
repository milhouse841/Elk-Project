## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.
diagramelk.jpg

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because Ansible is easy to use, agentless and playbooks are written in yaml

my_playbook2.yml 

---

- name: Configure Elk VM with Docker
  hosts: Elk
  remote_user: azadmin
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

    # Use pip module(It will default to pip3)
    - name: Install Docker module
      pip:
        name: docker
        state: present

    # Use Command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

    # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: 262144
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

    # Use systemd module
    - name: Enable service docker on boat
      systemd:
        name: docker
        enabled: true
### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly efficient, in addition to restricting access to the network.
- What aspect of security do load balancers protect? What is the advantage of a jump box
	Load Balancers defend an organization aganist distributed DDos attacks
	An advantage of a jumpbox is it can manage devices in a seperate security zone

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the _____ and system _____.
- _What does Filebeat watch for? Monitors the log files/locations as specified
- _What does Metricbeat record? Collects metric data from the operating system

The configuration details of each machine may be found below.
| Name     | Functon | IP Address | Operating system |   |
|----------|---------|------------|------------------|---|
| Jump Box | Gateway | 10.0.0.4   | Linux            |   |
| Web-1    | VM      | 10.0.0.7   | Linux            |   |
| web-2    | VM      | 10.0.0.8   | Linux            |   |
| ELK      | VM      | 10.1.0.4   | Linux            |   |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box Provisoner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 40.78.148.198

Machines within the network can only be accessed by the Jump Box VM

A summary of the access policies in place can be found in the table below.

| Name     | Functon | IP Address | 
|----------|---------|------------|
| Jump Box | Gateway | 10.0.0.4   |     
| Web-1    | VM      | 10.0.0.7   |       
| web-2    | VM      | 10.0.0.8   |       
| ELK      | VM      | 10.1.0.4   |   
The playbook implements the following tasks:
- Install ELK 
- Create filebeat configuration file
- Create metricbeat playbook

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

elkscreenshot.jpg


### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- web-1 10.0.0.7
- web-2 10.0.0.8
We have installed the following Beats on these machines:
- web-1 10.0.0.7
- web-2 10.0.0.8
These Beats allow us to collect the following information from each machine:
1- filebeat collects linux logs. This allows us to track things like user logon events, failed access attempts, service start and stops.
2- Metricbeat collects system metrics from the webservers. We look for things like cpu usage, memory usage, drive space usage and drive space avaiable for each host. 

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 
---

- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

  - name: download filebeat deb
    command: curl -L -O http://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb

  - name: install filebeat deb
    command: dpkg -i filebeat-7.6.1-amd64.deb

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
     
  - name: enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: true
      
SSH into the control node and follow the steps below:
- Copy the ansible.cfg file to etc/ansible.
- Update the host file to include the IP addresses of the VM's
- Run the playbook, and navigate to Ansible/yml to check that the installation worked as expected.

- _Which file is the playbook? Where do you copy it?filebeat-playbook.yml
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?  http://40.116.64.5:5601/app/kibana
