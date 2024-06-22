# Ansible Set-Up Documentation on Windows using Vagrant

#### Install Vagrant : https://developer.hashicorp.com/vagrant/downloads 

#### Pre-Req : Vagrant needs at least one Hyper-Visor (Oracle VirtualBox : https://www.virtualbox.org/wiki/Downloads)

<br> Vagrantfile </br>

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  
  # Will not check for box updates during every startup.
  config.vm.box_check_update = false


  # Master node where ansible will be installed
  config.vm.define "controller" do |controller|
    controller.vm.box = "geerlingguy/ubuntu2004"
    controller.vm.hostname = "controller.training.com"
    controller.vm.network "private_network", ip: "192.168.10.10"
    controller.vm.provider "virtualbox" do |vb|
      vb.memory = 1048
      vb.cpus = 1
    end
  end

  # Managed node ubuntu1
  config.vm.define "ubuntu1" do |ubuntu1|
    ubuntu1.vm.box = "geerlingguy/ubuntu2004"
    ubuntu1.vm.hostname = "ubuntu1.training.com"
    ubuntu1.vm.network "private_network", ip: "192.168.10.20"
    ubuntu1.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
      vb.cpus = 1
    end
  end

end


```
<br>Instructions to spin up VMs </br>
```
# Run this command from the folder where Vagrantfile is present
vagrant up
vagrant status
# Once both the VMs are running fine, get into the controller VM
vagrant ssh controller

```

Note: Default Vagrant VM credentials : vagrant/vagrant

<br> Instructions to Install Ansible in Ubuntu VM </br>
```
# Run these commands inside the 'controller' VM
sudo apt-get update -y
sudo apt-add-repository -y ppa:ansible/ansible
sudo apt-get update -y
sudo apt-get install ansible
ansible --version

# Let us try some adhoc commands against localhost
ansible localhost -m "ping"
ansible localhost -a "hostname"

```
<br> Ansible configurations to run play-books </br>

```
# Auto-generate /etc/ansible/ansible.cfg
# You would need root privilege to create & modify ansible.cfg
sudo su
cd /etc/ansible
ansible-config init --disabled > ansible.cfg

# In order to disable strict key checking, modify the following flag in ansible.cfg
vi ansible.cfg
# Search for the following line and edit it as follows
[defaults]
host_key_checking=False

```

<br> Let us make an entry in /etc/ansible/hosts file to add details about our 'ubuntu1' VM so that we can control it </br>

```
# Need root privilege for this
sudo su
cd /etc/ansible
vi hosts

# Append the following at the end of the file
[ubuntu1]
192.168.10.20

[ubuntu1:vars]
ansible_connection=ssh
ansible_user=vagrant
ansible_ssh_pass=vagrant

```

<br> All settings are now done, let us try running some ad-hoc commands against the server ubuntu1

```
# You can run these commands from any location and you do not need root privilege
# -vvvv is for printing verbose information
ansible ubuntu1 -m ping -vvvv
ansible ubuntu1 -a "hostname"
ansible ubuntu1 -a "date"
ansible ubuntu1 -a "whoami"

# Let us copy a file from our controller to ubuntu1
# First, create a file in the home folder - hello.txt - with some contents

echo "Hello World" >> hello.txt
ansible ubuntu1 -m copy -a "src=~/hello.txt dest=~"

# Let us check if this file has been copied onto ubuntu1 (inside home folder)
ansible ubuntu1 -m stat -a "path=~/hello.txt"

# Create a group
ansible ubuntu1 -b -m group -a "name=accenture state=present"
# Add a user to this group
ansible ubuntu1 -b -m user -a "name=anand group=accenture createhome=yes"

```
