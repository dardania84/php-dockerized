# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.require_version ">= 1.6.0"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "yungsang/coreos"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 2375, host: 2375

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  config.vm.network "public_network"

  # If true, then any SSH connections made will enable agent forwarding.
  # Default value: false
  # config.ssh.forward_agent = true

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder ".", "/home/core/vagrant",
    type: "nfs",
    id: "core",
    mount_options: ["nolock", "vers=3", "udp"]

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Don't boot with headless mode
  #   vb.gui = true
  #
  #   # Use VBoxManage to customize the VM. For example to change memory:
  #   vb.customize ["modifyvm", :id, "--memory", "1024"]
  # end
  #
  # View the documentation for the provider you're using for more
  # information on available options.

  config.vm.define "lnpp" do |lnpp|
    config.vm.provision "docker", run: "always" do |d|
      d.build_image "/home/core/vagrant/images/memcached",
        args: "-t lnpp/memcached"

      d.build_image "/home/core/vagrant/images/store",
        args: "-t lnpp/store"

      d.build_image "/home/core/vagrant/images/mysql",
        args: "-t lnpp/mysql"

      d.build_image "/home/core/vagrant/images/nginx",
        args: "-t lnpp/nginx"

      d.run "lnpp-cache",
        image: "lnpp/memcached",
        args: "-p 11211:11211"

      d.run "lnpp-store",
        image: "lnpp/store"

      d.run "lnpp-db",
        image: "lnpp/mysql",
        args: "-p 3306:3306 \
        -e MYSQL_PASS=password \
        --volumes-from lnpp-store"

      d.run "lnpp-front",
        image: "lnpp/nginx",
        args: "-p 80:80 \
        -v /home/core/vagrant/www:/var/www \
        -v /home/core/vagrant/sites:/etc/nginx/sites-enabled \
        --link lnpp-db:db \
        --link lnpp-cache:cache"
    end
  end
end
