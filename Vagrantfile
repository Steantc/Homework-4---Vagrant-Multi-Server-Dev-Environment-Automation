# -*- mode: ruby -*-
# vi: set ft=ruby :
# v2.0
# Author: Joe Axberg
# Comment:  This vagrant file rocks

Vagrant.configure("2") do |config|
    
  #define a  do block for the web server 
  config.vm.define "web" do |web|

   #use the ubuntu/bionic64 VM template - it is the official Ubuntu one
   #if you want to use Ubuntu 20.04, replace bionic64 with focal64 
   web.vm.box = "ubuntu/bionic64"

   #setting up the networking such that the web server
   #will have two network adapters:  a NAT network with a port forward
   #to allow the outside world to connect to the server
   #and also a private "host only" network to allow the web server
   #to talk to the db server 
   web.vm.network "forwarded_port", guest: 80, host: 80
   web.vm.network "private_network", ip: "192.168.56.10"

   #set up a provision block and put in commands so that vagrant
   #installs the Apache web server
   #this block creates a script that will be run on the VM
   #the commands ran are between the <<-SHELL and SHELL
   
   web.vm.provision "shell", inline: <<-SHELL
   apt-get update
   apt-get upgrade -y
   SHELL

   web.vm.provision "shell", inline: <<-SHELL
   apt-get update
   apt-get install -y apache2
   SHELL
   
   web.vm.provision "shell", inline: <<-SHELL
   apt-get update
   apt-get install -y libapache2-mod-php php-mysql
   systemctl restart apache2.service
   SHELL

   config.vm.provision "file", source: "phptest.php", destination: "phptest.php"

   web.vm.provision "shell", inline: <<-SHELL
   cp phptest.php /var/www/html
   SHELL

   end   #end of the web server do block

   #another block that defines the db server.
   #we'll call it simply "db"
   config.vm.define "db" do |db|

   db.vm.box = "ubuntu/bionic64"

   #the VM will automatically get a NAT network connection
   #(outbound only) to get to the internet.
   #here connect the VM to the same private "host only" as
   #the web server
   db.vm.network "private_network", ip: "192.168.56.11"

   #add your awesome bash commands to install as much of a mysql
   #database server as you can
   db.vm.provision "shell", inline: <<-SHELL
   apt-get update
   apt-get upgrade -y
   SHELL
   
   db.vm.provision "shell", inline: <<-SHELL
   apt-get update
   apt-get install -y mariadb-server
   ##systemctl status mariadb
   ##mysql -V
   ##mysql_secure_installation 
   systemctl restart mariadb
   SHELL

   db.vm.provision "shell", inline: <<-SHELL
   cd ~
   git clone https://github.com/datacharmer/test_db.git
   SHELL

   config.vm.provision "file", source: "50-server.cnf", destination: "50-server.cnf"

   db.vm.provision "shell", inline: <<-SHELL
   cp 50-server.cnf /etc/mysql
   SHELL

  end  #endof the db block

end
