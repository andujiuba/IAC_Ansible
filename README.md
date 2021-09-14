# Infrastruture as Code using Ansible

## Ansible controller and agent nodes set up guide

<br>

We will use 18.04 ubuntu for ansible controller and agent nodes set up 
- **You may need to reinstall plugins or dependencies required depending on the OS you are using.**

<br>

1. Within the `Vagrantfile`, the code should look like this:
```vagrant 

# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what

# MULTI SERVER/VMs environment 
#
Vagrant.configure("2") do |config|

# creating first VM called web  
  config.vm.define "web" do |web|
    
    web.vm.box = "bento/ubuntu-18.04"
   # downloading ubuntu 18.04 image

    web.vm.hostname = 'web'
    # assigning host name to the VM
    
    web.vm.network :private_network, ip: "192.168.33.10"
    #   assigning private IP
    
   # config.hostsupdater.aliases = ["development.web"]
    # creating a link called development.web so we can access web page with this link instread of an IP   
        
  end
  
# creating second VM called db
  config.vm.define "db" do |db|
    
    db.vm.box = "bento/ubuntu-18.04"
    
    db.vm.hostname = 'db'
    
    db.vm.network :private_network, ip: "192.168.33.11"
    
   # config.hostsupdater.aliases = ["development.db"]     
  end

 # creating are Ansible controller
  config.vm.define "controller" do |controller|
    
    controller.vm.box = "bento/ubuntu-18.04"
    
    controller.vm.hostname = 'controller'
    
    controller.vm.network :private_network, ip: "192.168.33.12"
    
   # config.hostsupdater.aliases = ["development.controller"] 
    
  end

end
```

2. In the directory with the `Vagrantfile`, run:
```bash
vagrant up
```
  - After, run `vagrant status` and check if all 3 VMs are running
  ```bash
  web                       running (virtualbox)
db                        running (virtualbox)
controller                running (virtualbox)
  ```

3. `vagrant ssh` into all 3 VMs and run:
```vagrant
sudo apt-get update -y
sudo apt-get upgrade -y
```
<!-- 4. `vagrant ssh` into the controller VM and ping the different VMs
```vagrant
ping 192.168.33.10 = *web*
ping 192.168.33.11 = *db*
```
and check for responses -->

5. Run these commands in the controller VM:
```vagrant
sudo apt-get install software-properties-common -y
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible -y
ansible --version
```

6. `ssh` into web VM from controller VM
```vagrant
ssh vagrant@192.168.33.10
```
then run the `update` and `upgrade` commands. Repeat for the db VM

7. Ping the machines
```vagrant
ansible all -m ping
ansible web -m ping
ansible db -m ping
```
8. If an error occurs, cd into `/etc/ansible/` and edit the `hosts` to add the IPs for the VMs.

```vagrant
[web]
192.168.33.10 ansible_connection=ssh ansible_user=vagrant ansible_ssh_pass=vagrant

[db]
192.168.33.11 ansible_connection=ssh ansible_user=vagrant ansible_ssh_pass=vagrant
```
9. Ping the VMs again


## Ansible Playbooks
--diagram--

Ansible playhooks are a completely different way to use ansible than ad-hoc task execution. Ansible playhooks are `.yml` files written in YAML (Yet Another Markup Language) 

1. Create a playbook with the `.yml` extention
```bash
# Create a playbook to install nginx web server on web machine
# web 192.168.33.10
# Add the 3 dashes to start the YAML
---
# To do:
# add name of the host --> where we are installing
- hosts: web

# gather facts about the installation steps
  gather_facts: yes

# we need admin access
  become: true

# add instructions to install nginx on web machine (state=present/absent)
  tasks:
  - name: Install Nginx
    apt: pkg=nginx state=present

# ensure the nginx server is running
```
2. Create the same playbook in contoller VM in /etc/ansible/ directory

3. Code `ansible-playbook playbook_name.yml`

4. Check if playbook worked:
```vagrant
ansible web -a "sudo systemctl status nginx"
```
5. Enter web IP in browser and see if page is running


**Tasks:**

- Create a playbook to install/configure node on the web machine
- ceate a playbook to install/configure mongodb in the db machine
- Get the nodeapp to work on web up with /posts
- Configure nginx reverse proxy (port 3000)
(could add code to reverse proxy in playbook, must install node app first)

### Installing/Configuring Node.js on Web Machine



### Installing/Configuring MongoDB on DB Machine



### Configuring Reverse Proxy



<br>
<br>

### **Troubleshooting**
- If there are problems setting up the VMs, check the `Vagrantfile` and make sure all indentations are in the correct places
- Make comments are all commented-out
- Delete the running VMS and start up again
```bash
vagrant destroy
rm -rf .vagrant
vagrant up
```
- Go into VM `VirtualBox` and delete all running VMs


<br>

## To Research

**What is Infrastructure as Code?**


**What are the 2 types of IAC?**

Configuration Management and Orchetration

*Config management:* push and pull config management

*Orchestration:* Cloud formation (AWS), Ansible and Terraform

**Which tools are used for push config and pull config?**

**What are `Ad-Hoc Commands` in Ansible?**
ad hoc commands - commands that act on multiple agents

**What is Ansible? What era its benefits?**


**Why should we use Ansible?**


**Ansible: prem, public and hybrid architecture**
--diagrams--

**What is the default directory structure for Ansible?**


**What is the Inventory/Hosts file and what is its purpose?**




# Tuesday 14/9

**Tasks:**
- Launch an EC2 instance on AWS using Ansible playbook
  - AWS keys: sre_key.pem
  sre_key sre_key.pub
  Ansible vault configuration
- We need AWS account
- IAM role with access to EC2

- Create a ec2_create.yml file in ansible controller
    
    **IDs needed:** <br> ami ID number<br> 
    type of instance<br> 
    vpc ID number<br> 
    subnet ID number<br> 
    security group ID number<br> 
    key name<br> 
    public IP

- Automate ssh  