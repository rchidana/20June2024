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
