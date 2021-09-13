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

3. `vagrant ssh` into all 3 VMs and run:
```vagrant
sudo apt-get update -y
sudo apt-get upgrade -y
```
4. `vagrant ssh` into the controller VM and ping the different VMs
```vagrant
ping 192.168.33.10 = *web*
ping 192.168.33.11 = *db*
```
and check for responses

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