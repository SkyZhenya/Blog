# -*- mode: ruby -*-
# vi: set ft=ruby :
# All Vagrant configuration is done here. For a complete reference,
# please see the online documentation at vagrantup.com.

VAGRANTFILE_API_VERSION = "2"
Vagrant.require_version ">= 1.7.2"

# CONSTANTS
BOX_NAME = "blog"
HOST_NAME = "blog.local"
HOST_NAME_ALIASES = ["blog.local", "db.blog.local"]
PRIVATE_IP = "192.168.18.18"
SYNCED_FOLDER = "Blog"
ANSIBLE_PLAYBOOK = "provision/dev.yml"
ANSIBLE_INVENTORY_PATH = "provision/dev"


#detect envirinmnet variables
host_os = RbConfig::CONFIG['host_os']
vagrant_command = ARGV[0]

if vagrant_command == "up"
    # check and install required Vagrant plugins
	required_plugins = %w( vagrant-vbguest vagrant-cachier vagrant-hostsupdater vagrant-host-shell )
	required_plugins.each do |plugin|
		unless Vagrant.has_plugin?(plugin)
			system "vagrant plugin install #{plugin}" unless Vagrant.has_plugin? plugin
		end
	end
	# install nfs-kernel-server in Linux to support synced_folder via nfs
    if host_os =~ /linux/
    	system "sudo apt-get install nfs-kernel-server nfs-common rpcbind"
    end
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.boot_timeout = 300
  config.vm.provider "virtualbox" do |vb|
	vb.gui = false
  end

  config.vm.define BOX_NAME do |pitapat|
	pitapat.vm.box = BOX_NAME
	# Configure some Virtual Box params
	pitapat.vm.provider :virtualbox do |pitapat|
		pitapat.customize ["modifyvm", :id, "--name", BOX_NAME]
		pitapat.customize ["modifyvm", :id, "--ostype", "Ubuntu_64"]
		pitapat.customize ["modifyvm", :id, "--memory", "1024"]
		pitapat.customize ["modifyvm", :id, "--cpuexecutioncap", "80"]
		pitapat.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        pitapat.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
		# Set VirtualBox guest CPU count to the number of host cores
		if host_os =~ /linux/
        	pitapat.customize ["modifyvm", :id, "--cpus", `grep "^processor" /proc/cpuinfo | wc -l`.chomp ]
        end
	end
	pitapat.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
	pitapat.vm.network "private_network", ip: PRIVATE_IP
	#share synced_folder via nfs for Mac nd Linux
	if host_os =~ /linux/ or host_os =~ /darwin/
		pitapat.vm.synced_folder "/var/www/#{SYNCED_FOLDER}", "/var/www/#{SYNCED_FOLDER}", type: "nfs", version: 3, nfs_udp: 1
	else
		pitapat.vm.synced_folder "/var/www/#{SYNCED_FOLDER}", "/var/www/#{SYNCED_FOLDER}", owner: "www-data", group: "www-data"
	end

	# PLUGINS
	# Set entries in hosts file
	# https://github.com/cogitatio/vagrant-hostsupdater
	if Vagrant.has_plugin?("vagrant-hostsupdater")
	  pitapat.hostsupdater.remove_on_suspend = true
	  pitapat.vm.hostname = HOST_NAME
	  pitapat.hostsupdater.aliases = HOST_NAME_ALIASES
	end
	if Vagrant.has_plugin?("vagrant-cachier")
	  pitapat.cache.auto_detect = true
	end

	# PROVISIONING
	# Ansible
	# To use Ansible provisioning you should have Ansible installed on your host machine
	# see here http://docs.ansible.com/intro_installation.html#installing-the-control-machine
	pitapat.vm.provision "ansible" do |ansible|
		# should be equal to host name in Ansible hosts file
		ansible.limit = "dev"
		ansible.playbook = ANSIBLE_PLAYBOOK
		ansible.inventory_path = ANSIBLE_INVENTORY_PATH
		# set to 'vvvv' for debug output in case of problems or leave it false
		ansible.verbose = 'vvvv'
		ansible.host_key_checking = false
	end

  end

end