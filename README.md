# ELK-stack
The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly stable, in addition to restricting traffic to the network.

Load balancers do very good job preventing DDoS attacks. A jump box is used, which offers a simplified system to maintain, keeping updates and system security straightforward.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system traffic.

- Filebeat manages the flow of data, and forwards and centralizes log data.
- MetricBeat records data about the other containers on the system.

The configuration details of each machine may be found below.

*Note, internal IP addresses may vary, so expect to see slightly different IP addresses on the VMs



| Name                | Function    | IP Address              | Operating System |
|---------------------|-------------|-------------------------|------------------|
| JumpBox Provisioner | Gateway     | 10.0.0.1                | Linux            |
| Web 1               | Web access  | 10.0.0.5                | Linux            |
| Web 2               | Web access  | 10.0.0.6                | Linux            |
| ELK1                | Kibana host | 10.1.0.1 /[External IP] | Linux            |

*ELK1 VM external IP will vary, and will be needed to open Kibana

### Access Policies

The machines on the internal network are not exposed to the public Internet.

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:

- *This IP address should be considered to be whatever the IP of the user's computer. Naturally, this will vary from machine to machine*

Machines within the network can only be accessed by JumpBox.

- *The JumpBox machine driving output of the two Web machines.  See IP Address 10.0.0.4*

A summary of the access policies in place can be found in the table below.

| Name     | Publicly accessible | Allowed IP addresses |
| -------- | ------------------- | -------------------- |
| Jump Box | Yes, via SSH        | [Your IP address]    |
| Web 1    | No                  | 10.0.0.4             |
| Web 2    | No                  | 10.0.0.4             |
| ELK1     | Yes                 | 10.0.0.4 /[Your IP]  |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because Ansible streamlines the process and handles all the legwork of setup. 

The playbook implements the following tasks:

- Python and Docker are both installed, which ensures the needed functions are present on the virtual machines need for the ELK stack
- A container running the DVWA is created and running in and out on port 80
- Docker immediately begins running on the first running of the program

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.





### Target Machines & Beats

This ELK server is configured to monitor the following machines:

- 10.0.0.5, 10.0.07

We have installed the following Beats on these machines:

- Elasticsearch, Logstash, and Filebeat

These Beats allow us to collect the following information from each machine:

- Elasticsearch searches and stores data, with Logstash processing the data from Elasticsearch
- Filebeat aids Logstash in the function of logging and processing data, while being easy to monitor inside it's container.

### Using the Playbook

In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned, you will need to do the following.

SSH into the control node and follow the steps below:

- Copy the .yml file (Contents below) to nano.
- Update the .yml file to include the tasks.
- Run the playbook, and navigate to /etc to check that the installation worked as expected.


-The file for this configuration in ansible is labeled: `/etc/ansible/hosts`
- Then create a file called `pentest.yml`, and update it with the playbook at the bottom
- The http address of Kibana through 5601 is as follows; http://[ELK VM external IP]:5601/app/kibana#/home


Contained underneath is the configuration of pentest.yml, the file to configure Docker and Python:


```yaml
 name: Config Web VM with Docker
  hosts: webservers
  become: true
  tasks:

  - name: docker.io
    apt:
      force_apt_get: yes
      update_cache: yes
      name: docker.io
      state: present

  - name: Install pip3
    apt:
      force_apt_get: yes
      name: python3-pip
      state: present

  - name: Install Docker python module
    pip:
      name: docker
      state: present

  - name: download and launch a docker web container
    docker_container:
      name: dvwa
      image: cyberxsecurity/dvwa
      state: started
      published_ports: 80:80

  - name: Enable docker service
    systemd:
      name: docker
      enabled: yes
```

Also, you can find below the playbook for install-elk.yml, which has a specific detail of the ports that ELK runs on. I have included both for depth, but the .yml file configurations can be used and modified to keep the playbooks concise.

```yaml
 name: Configure Elk VM with Docker
  hosts: elk
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
        value: '262144'
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
```
