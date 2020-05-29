# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.box_check_update = true

  config.vm.define "dns" do |dns|
    dns.vm.provision "file", source: "./bind", destination: "/tmp/bind"
    dns.vm.network "public_network", ip: "192.168.0.121"
    dns.vm.provision "shell", inline: <<-SHELL
      apt-get update
      # apt-get upgrade -y
      apt-get install -y bind9 dnsutils
      hostnamectl set-hostname vgrnx21.nx-services.local
      cp -r /tmp/bind/. /etc/bind
      systemctl restart bind9
    SHELL
  end

  config.vm.define "web" do |web|
    web.vm.provision "file", source: "./apache2", destination: "/tmp/apache2"
    web.vm.provision "file", source: "./www", destination: "/tmp/www"
    web.vm.network "public_network", ip: "192.168.0.122"
    web.vm.provision "shell", inline: <<-SHELL
      apt-get update
      # apt-get upgrade -y
      apt-get install -y apache2 dnsutils
      hostnamectl set-hostname vgrnx22.nx-services.local
      mkdir /var/www/www.nx-services.local
      mkdir /var/www/wwwin.nx-services.local
      cp /tmp/www/www.nx-services.local-index.html /var/www/www.nx-services.local/index.html
      cp /tmp/www/wwwin.nx-services.local-index.html /var/www/wwwin.nx-services.local/index.html
      cp /tmp/apache2/*.conf /etc/apache2/sites-available
      a2ensite $(ls /etc/apache2/sites-available/)
      apache2ctl graceful
    SHELL
  end

  config.vm.define "dns2" do |dns2|
    dns2.vm.provision "file", source: "./bind2", destination: "/tmp/bind"
    dns2.vm.network "public_network", ip: "192.168.0.124"
    dns2.vm.provision "shell", inline: <<-SHELL
      apt-get update
      # apt-get upgrade -y
      apt-get install -y bind9 dnsutils
      hostnamectl set-hostname vgrnx24.nx-services.local
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
      node.vm.network "public_network", ip: "192.168.0.9#{i}"
      node.vm.provision "shell", inline: <<-EOF
        hostnamectl set-hostname pmp#{i}
        sed -i "s/127\.0\.1\.1.*/127\.0\.1\.1 pmp#{i} pmp#{i}.nx-services.local/g" /etc/hosts
        wget -q https://www.manageengine.com/products/passwordmanagerpro/8621641/ManageEngine_PMP_64bit.bin
      EOF
      node.vm.provider "virtualbox" do |vb|
        vb.cpus = "1"
        vb.memory = "1024"
      end
    end
  end

end
