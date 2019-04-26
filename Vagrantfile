# -*- mode: ruby -*-
# vi: set ft=ruby :

# make sure the vagrant-proxyconf plugin is installed
# see https://stackoverflow.com/questions/19492738/demand-a-vagrant-plugin-within-the-vagrantfile
required_plugins = %w(vagrant-proxyconf)
plugins_to_install = required_plugins.select { |plugin| not Vagrant.has_plugin? plugin }
if not plugins_to_install.empty?
  puts "Installing plugins: #{plugins_to_install.join(' ')}"
  if system "vagrant plugin install #{plugins_to_install.join(' ')}"
    exec "vagrant #{ARGV.join(' ')}"
  else
    abort "\e[31mERROR:Installation of one or more plugins has failed. Aborting.\e[0m"
  end
end

if ENV["http_proxy"].nil?
  abort "\e[31mERROR: you have to set the $http_proxy environment variable\e[0m"
end

# 10.0.2.2 is the "special IP" to access host from within vagrant box
# this requires a proxy to be always running on the host,
# no matter whether we are in BCN or not
box_proxy = "http://10.0.2.2:3128"

molecule_box_name = 'moclecule-test'
molecule_box_ip = '192.168.33.39'

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|

  # these proxy settings are used by Vagrant itself
  # since we are working with docker, please check https://github.com/tmatilai/vagrant-proxyconf/issues/97
  config.proxy.http = ENV["http_proxy"]
  config.proxy.https = ENV["http_proxy"]
  config.proxy.no_proxy = ENV["no_proxy"]

  config.vm.define molecule_box_name do |machine|
    machine.vm.hostname = molecule_box_name
    machine.vm.network :private_network, ip: molecule_box_ip
    machine.vm.provider "virtualbox" do |v, override|
      # these proxy settings are used from within the box
      # e.g. yum config, curl, ...
      override.proxy.http = box_proxy
      override.proxy.https = box_proxy
      v.name = molecule_box_name
      v.memory = 2048
      v.cpus = 2
    end
  end

  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/bionic64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    sudo apt-get --assume-yes install git
    sudo apt-get --assume-yes install     apt-transport-https     ca-certificates     curl     gnupg-agent     software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
       $(lsb_release -cs) \
       stable"
    sudo apt-get update
    sudo apt-get --assume-yes install docker-ce
    sudo usermod -aG docker vagrant
    sudo pip install molecule
    sudo apt-get --assume-yes install python-pip
    sudo pip install --upgrade pip
    sudo pip install molecule
    sudo pip install docker-py
  SHELL
end
