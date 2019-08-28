# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.customize ["modifyvm", :id, "--groups", "/AI21ST Development"]
  end

  config.vm.define "k8s-master01" do |k8scluster|
      k8scluster.vm.box = "generic/debian10"
      k8scluster.vm.hostname = "k8s-master01"
      k8scluster.vm.network "private_network", ip: "192.168.33.10"
      k8scluster.vm.provider "virtualbox" do |vb|
          vb.name = "k8s-master01"
          vb.cpus = 4
          vb.memory = "2048"
      end
      k8scluster.vm.provision "shell", inline: <<-SHELL
            hostnamectl status
      SHELL
    end

    config.vm.define "k8s-master02" do |k8scluster|
      k8scluster.vm.box = "generic/debian10"
      k8scluster.vm.hostname = "k8s-master02"
      k8scluster.vm.network "private_network", ip: "192.168.33.11"
      k8scluster.vm.provider "virtualbox" do |vb|
          vb.name = "k8s-master02"
          vb.cpus = 4
          vb.memory = "2048"
      end

      k8scluster.vm.provision "shell", inline: <<-SHELL
            hostnamectl status
      SHELL
    end

    config.vm.define "k8s-master03" do |k8scluster|
      k8scluster.vm.box = "generic/debian10"
      k8scluster.vm.hostname = "k8s-master03"
      k8scluster.vm.network "private_network", ip: "192.168.33.12"
      k8scluster.vm.provider "virtualbox" do |vb|
          vb.name = "k8s-master03"
          vb.cpus = 4
          vb.memory = "2048"
      end

      k8scluster.vm.provision "shell", inline: <<-SHELL
            hostnamectl status
      SHELL
    end

    config.vm.define "worker01" do |k8scluster|
        k8scluster.vm.box = "generic/debian10"
        k8scluster.vm.hostname = "worker01"
        k8scluster.vm.network "private_network", ip: "192.168.33.20"
        k8scluster.vm.provider "virtualbox" do |vb|
            vb.name = "worker01"
            vb.cpus = 4
            vb.memory = "4096"
        end

        k8scluster.vm.provision "shell", inline: <<-SHELL
              hostnamectl status
        SHELL
      end


    config.vm.define "worker02" do |k8scluster|
        k8scluster.vm.box = "generic/debian10" 
        k8scluster.vm.hostname = "worker02"
        k8scluster.vm.network "private_network", ip: "192.168.33.21"
        k8scluster.vm.provider "virtualbox" do |vb|
            vb.name = "worker02"
            vb.cpus = 4
            vb.memory = "4096"
        end
 
        k8scluster.vm.provision "shell", inline: <<-SHELL
              hostnamectl status
        SHELL
      end

      config.vm.define "harbor" do |k8scluster|
        k8scluster.vm.box = "generic/debian10" 
        k8scluster.vm.hostname = "harbor"
        k8scluster.vm.network "private_network", ip: "192.168.33.9"
        k8scluster.vm.provider "virtualbox" do |vb|
            vb.name = "harbor"
            vb.cpus = 4
            vb.memory = "4096"
        end

        k8scluster.vm.provision "shell", inline: <<-SHELL
              hostnamectl status
        SHELL
      end

      config.vm.define "remotecontrol01" do |k8scluster|
          k8scluster.vm.box = "bento/centos-7.6" 
          k8scluster.vm.hostname = "remotecontrol01"
          k8scluster.vm.network "private_network", ip: "192.168.33.8"
          k8scluster.vm.provider "virtualbox" do |vb|
              vb.name = "remotecontrol01"
              vb.memory = "1024"
          end
          k8scluster.vm.provision "ansible_local" do |ansible|
            ansible.playbook = "provisioning/deploy.yml"
            ansible.become = true
            ansible.compatibility_mode = "2.0"
            ansible.version = "2.8.4"
            ansible.extra_vars = {
                    node_ip: "192.168.33.8",
                }
          end
          k8scluster.vm.provision "shell", inline: <<-SHELL
                hostnamectl status
          SHELL
        end
end
