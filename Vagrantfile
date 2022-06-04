# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
IMAGE_NAME = "centos/7"
K8S_NAME = "k8s-cluster"
MASTERS_NUM = 1
MASTERS_CPU = 2 
MASTERS_MEM = 2048
NODES_NUM = 0
NODES_CPU = 2
NODES_MEM = 2048
IP_BASE = "192.168.56."
VAGRANT_DISABLE_VBOXSYMLINKCREATE=1
JENKINS_NODE = false
JENKINS_NODE_CPU = 1
JENKINS_NODE_MEM = 2048

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false

  if Vagrant.has_plugin?("vagrant-vbguest") then
    config.vbguest.auto_update = false
  end

  master_nodes = []
  worker_nodes = []

  (1..MASTERS_NUM).each do |i|
    master_nodes.push("k8s-master-#{i}")
    config.vm.define "k8s-master-#{i}" do |master|

      master.vbguest.installer_options = { allow_kernel_upgrade: true }
      master.vm.box = IMAGE_NAME

      if Vagrant::Util::Platform::windows?
        master.vm.network "public_network"
      else
        master.vm.network "private_network", ip: "#{IP_BASE}#{i + 10}"
      end
      master.vm.hostname = "k8s-master-#{i}"
      master.vm.provider "virtualbox" do |v|
        v.name = "k8s-master-#{i}"
        v.memory = MASTERS_MEM
        v.cpus = MASTERS_CPU
      end

      if i == MASTERS_NUM && !Vagrant::Util::Platform::windows?
        # master.vm.provision "shell" do |shell|
        #   shell.inline= "ansible-galaxy collection install kubernetes.core"
        # end
        master.vm.provision "ansible_local" do |ansible|
          ansible.limit = "all"
          ansible.playbook = "playbook.yml"
          ansible.groups = {
            "masters" => master_nodes,
          }
          ansible.galaxy_command= "sudo ansible-galaxy collection install -r requirements.yml"
        end
      end
      # master.vm.provision "ansible" do |ansible|
      #   ansible.playbook = "playbook.yml"
      #   #Redefine defaults
      #   ansible.extra_vars = {
      #       k8s_cluster_name:       K8S_NAME,
      #       k8s_master_admin_user:  "vagrant",
      #       k8s_master_admin_group: "vagrant",
      #       k8s_master_apiserver_advertise_address: "#{IP_BASE}#{i + 10}",
      #       k8s_master_node_name: "k8s-m-#{i}",
      #       k8s_node_public_ip: "#{IP_BASE}#{i + 10}"
      #   }
      #end
    end
  end

  (1..NODES_NUM).each do |j|
    worker_nodes.push("k8s-node-#{j}")
    config.vm.define "k8s-node-#{j}" do |node|
        node.vm.box = IMAGE_NAME
        node.vm.network "private_network", ip: "#{IP_BASE}#{j + 10 + MASTERS_NUM}"
        node.vm.hostname = "k8s-node-#{j}"
        node.vm.provider "virtualbox" do |v|
            v.name = "k8s-node-#{j}"
            v.memory = NODES_MEM
            v.cpus = NODES_CPU
        end
        if j == NODES_NUM && Vagrant::Util::Platform::windows?
          node.vm.provision "ansible_local" do |ansible|
            ansible.limit = "all"
            ansible.playbook = "playbook.yml"
            ansible.groups = {
              "workers" => worker_nodes,
            }
          end
        end
        # node.vm.provision "ansible" do |ansible|
        #     ansible.playbook = "roles/k8s.yml"
        #     #Redefine defaults
        #     ansible.extra_vars = {
        #         k8s_cluster_name:     K8S_NAME,
        #         k8s_node_admin_user:  "vagrant",
        #         k8s_node_admin_group: "vagrant",
        #         k8s_node_public_ip: "#{IP_BASE}#{j + 10 + MASTERS_NUM}"
        #     }
        #end
    end
  end

  if JENKINS_NODE == true
    config.vm.define "jenkins-k8s" do |jenkins|
      jenkins.vm.box = IMAGE_NAME
      jenkins.vm.network "private_network", ip: "#{IP_BASE}#{1 + 10 + MASTERS_NUM + NODES_NUM}"
      jenkins.vm.hostname = "jenkins-k8s"
      jenkins.vm.provider "virtualbox" do |v|
        v.name = "jenkins-k8s"
        v.memory = JENKINS_NODE_MEM
        v.cpus = JENKINS_NODE_CPU
      end
      if !Vagrant::Util::Platform::windows?
        jenkins.vm.provision "ansible" do |ansible|
          ansible.limit = "all"
          ansible.playbook = "playbook.yml"
        end
      end
    end
  end

  #Provider Settings
  # config.vm.provider "virtualbox" do |v|
  #   v.memory = 2048
  #   v.cpus = 2
  #end

  # #Machines Settings
  # config.vm.define "master" do |master|
  #   master.vm.hostname= "master"
  #   master.vm.network "private_network", ip: "192.168.33.101"
  #end

  # config.vm.define "worker1" do |worker1|
  #   worker1.vm.hostname = "worker1"
  #   worker1.vm.network "private_network", ip: "192.168.33.102"
  #end
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo sed -i "s/^#*\s*PasswordAuthentication .*/PasswordAuthentication yes/g" /etc/ssh/sshd_config
  #   sudo systemctl restart sshd
  # SHELL
  
 
end
