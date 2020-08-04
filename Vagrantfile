# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.box_check_update = true

  config.vm.define "dns" do |dns|
    dns.vm.provision "file", source: "./bind", destination: "/tmp/bind"
    dns.vm.network "public_network", ip: "192.168.0.121", bridge: "en9"
    dns.vm.provision "shell", inline: <<-SHELL
      apt-get update
      # apt-get upgrade -y
      apt-get install -y bind9 dnsutils
      hostnamectl set-hostname vgrnx21.nx-services.local
      cp -r /tmp/bind/. /etc/bind
      systemctl restart bind9
    SHELL
  end
  config.vm.define "dns2" do |dns2|
    dns2.vm.provision "file", source: "./bind2", destination: "/tmp/bind"
    dns2.vm.network "public_network", ip: "192.168.0.122", bridge: "en9"
    dns2.vm.provision "shell", inline: <<-SHELL
      apt-get update
      # apt-get upgrade -y
      apt-get install -y bind9 dnsutils
      hostnamectl set-hostname vgrnx22.nx-services.local
      cp -r /tmp/bind/. /etc/bind
      systemctl restart bind9
    SHELL
  end

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = "1"
    vb.memory = "512"
  end

  (1..2).each do |i|
    config.vm.define "pmp#{i}" do |node|
      node.vm.network "public_network", ip: "192.168.0.9#{i}", bridge: "en9"
      node.vm.provision "shell", inline: <<-EOF
        apt-get update
        apt-get install -y unzip
        hostnamectl set-hostname pmp#{i}.nx-services.local
      EOF
      node.vm.provider "virtualbox" do |vb|
        vb.cpus = "2"
        vb.memory = "2024"
      end
    end
  end

end
