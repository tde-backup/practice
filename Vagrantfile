# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

    config.vm.provision "file", source: "files/vagrant_test.pub", destination: "/home/vagrant/.ssh/"
  
  
    config.vm.define "controlnode" do |controlnode|
      controlnode.vm.box = "ubuntu/focal64"
      controlnode.vm.hostname = "controlnode"
      controlnode.vm.network "private_network", ip: "192.168.50.4"
      controlnode.vm.synced_folder "./ansible","/home/vagrant/ansible"
      controlnode.vm.synced_folder "./files","/home/vagrant/files/"
      controlnode.vm.provision "file", source: "files/vagrant_test", destination: "/home/vagrant/.ssh/"
      controlnode.vm.provision "shell", inline: <<-SHELL
        sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/#g' /etc/ssh/sshd_config
        service ssh restart
  
        sudo add-apt-repository ppa:ansible/ansible
        sudo apt update -y && sudo apt -y install sshpass ansible python3-pip
        pip3 install --upgrade ansible
  
        chmod 600 /home/vagrant/.ssh/vagrant_test
        chmod 644 /home/vagrant/.ssh/vagrant_test.pub
        cat /home/vagrant/.ssh/vagrant_test.pub >> /home/vagrant/.ssh/authorized_keys
  
        sudo timedatectl set-timezone 'Europe/Kaliningrad'
      SHELL
    end
  
    config.vm.define "smphr_srv" do |smphr_srv|
      smphr_srv.vm.box = "generic/rocky9"
      smphr_srv.vm.hostname = "smphr-srv"
      smphr_srv.vm.network "private_network", ip: "192.168.50.12"
      smphr_srv.vm.provision "shell", inline: <<-SHELL
        sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/#g' /etc/ssh/sshd_config
        service ssh restart
  
        chmod 644 /home/vagrant/.ssh/vagrant_test.pub
        cat /home/vagrant/.ssh/vagrant_test.pub >> /home/vagrant/.ssh/authorized_keys
  
        sudo timedatectl set-timezone 'Europe/Kaliningrad'
  
      SHELL
    end
  
  end
  