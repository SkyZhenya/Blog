Blog
============================================


## Download and Install Vagrant http://www.vagrantup.com/downloads.html

### install Vagrant plugins

```
vagrant plugin install vagrant-vbguest vagrant-cachier vagrant-hostsupdater vagrant-host-shell
```

## Install Ansible

```
sudo apt-add-repository ppa:rquillo/ansible
sudo apt-get update
sudo apt-get install ansible
```

## Install Virtual Box

https://www.virtualbox.org/wiki/Download_Old_Builds_4_3 (tested on VirtualBox VirtualBox 4.3.24 )

## Install packages required for Network File System

```
sudo apt-get install nfs-kernel-server nfs-common portmap
```

## Clone project

```
cd /var/www
git clone https://github.com/SkyZhenya/Blog.git
```

## Run vagrant

```
cd /var/www/Blog/
git pull origin master
vagrant up
```