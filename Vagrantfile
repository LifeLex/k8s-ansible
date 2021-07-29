# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.

  #Box Settings
  config.vm.box = "centos/7"
  

  #Provider Settings
  config.vm.provider "hyperv" do |v|
    v.memory = 2048
    v.cpus = 2
    v.vm_integration_services = {
      guest_service_interface: true
    }    
  end

  #Machines Settings
  config.vm.define "master" do |master|
    master.vm.hostname= "master"
    master.vm.network "private_network", ip: "192.168.33.101"
  end

  config.vm.define "worker1" do |worker1|
    worker1.vm.hostname = "worker1"
    worker1.vm.network "private_network", ip: "192.168.33.102"
  end
  config.vm.provision "shell", inline: <<-SHELL
    sudo sed -i "s/^#*\s*PasswordAuthentication .*/PasswordAuthentication yes/g" /etc/ssh/sshd_config
    sudo systemctl restart sshd
  SHELL
  
 
end
