# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "generic/centos8"

  # Create forwarded port for Odoo. This will enable public access to the
  # opened port.
  config.vm.network "forwarded_port", guest: 8069, host: 8069

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the third argument
  # creates the folder if it doesn't exist on the host.
  config.vm.synced_folder "data", "/vagrant_data", create: true

  # The bash script is added to the shared folder.
  config.vm.provision "file", source: "./start_odoo.sh", destination: "/vagrant_data/start-odoo"

  # Specific configuration for virtualbox
  config.vm.provider "virtualbox" do |vb|

  # Customize the amount of memory on the VM:
    vb.name = "odoo-vm"
    vb.memory = "1024"
  end

  # Shell script for setting up the VM with the required packages and
  # downloading Odoo
  config.vm.provision "shell", inline: <<-SHELL
     dnf update -y
     dnf groupinstall -y "Development tools"
     dnf install -y python3 python2 python3-devel python2-devel nfs-utils expect
     
     firewall-cmd --permanent --zone public --add-service mountd
     firewall-cmd --permanent --zone public --add-service rpc-bind
     firewall-cmd --permanent --zone public --add-service nfs
     firewall-cmd --permanent --zone=public --add-port=8069/tcp
     firewall-cmd --reload

     dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm 
     dnf -qy module disable postgresql
     dnf install -y postgresql12 postgresql12-server
     /usr/pgsql-12/bin/postgresql-12-setup initdb
     systemctl enable postgresql-12
     systemctl start postgresql-12
     sudo -u postgres createuser -s vagrant
     sudo -u createdb vagrant

     git clone -b 13.0 --single-branch https://github.com/odoo/odoo.git

     dnf install -y libxml2 libxslt libxml2-devel libxslt-devel openldap openldap-devel redhat-rpm-config
     sudo -u vagrant pip3 install --user -r odoo/requirements.txt

  SHELL
end
