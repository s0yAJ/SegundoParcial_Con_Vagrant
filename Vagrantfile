# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # Configuración de servidor web1
  config.vm.define "web1" do |web1|
    web1.vm.box = "ubuntu/bionic64"   # Puedes cambiar la versión de Ubuntu si lo deseas
    web1.vm.network "private_network", ip: "192.168.56.10"  # IP estática para web1
    web1.vm.hostname = "web1"
    web1.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end

    # Provisionamiento de Apache en web1
    web1.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
    SHELL

    # Sincronización del directorio local con el directorio de Apache
    web1.vm.synced_folder "./web1", "/var/www/html"
  end

  # Configuración de servidor web2
  config.vm.define "web2" do |web2|
    web2.vm.box = "ubuntu/bionic64"
    web2.vm.network "private_network", ip: "192.168.56.11"  # IP estática para web2
    web2.vm.hostname = "web2"
    web2.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end

    # Provisionamiento de Apache en web2
    web2.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
    SHELL

    # Sincronización del directorio local con el directorio de Apache
    web2.vm.synced_folder "./web2", "/var/www/html"
  end

  # Configuración de servidor loadbalancer
  config.vm.define "loadbalancer" do |lb|
    lb.vm.box = "ubuntu/bionic64"
    lb.vm.network "private_network", ip: "192.168.56.12"  # IP estática para loadbalancer
    lb.vm.hostname = "loadbalancer"
    lb.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end

    # Provisionamiento de Nginx en loadbalancer y configuración del balanceador
    lb.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y nginx
      # Configuración del balanceo de carga en Nginx
      echo '
      upstream backend {
          server 192.168.56.10;
          server 192.168.56.11;
      }

      server {
          listen 80;

          location / {
              proxy_pass http://backend;
          }
      }
      ' > /etc/nginx/sites-available/default
      systemctl restart nginx
    SHELL
  end
end
