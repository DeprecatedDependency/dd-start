# -*- mode: ruby -*-
# vi: set ft=ruby :

hostname        = "mb.dev"
server_ip       = "192.168.44.12"
server_timezone = "EST"

Vagrant.configure(2) do |config|

  config.vm.box = "chef/centos-6.6"
  config.vm.box_check_update = true

  config.vm.hostname = hostname

  # config.vm.network "forwarded_port", guest: 80, host: 8001
  config.vm.network "private_network", ip: server_ip

  config.vm.provider "virtualbox" do |v|
    host = RbConfig::CONFIG['host_os']

    if host =~ /darwin/
      cpus = `sysctl -n hw.ncpu`.to_i
      # sysctl returns Bytes and we need to convert to MB
      mem = `sysctl -n hw.memsize`.to_i / 1024 / 1024 / 4
    elsif host =~ /linux/
      cpus = `nproc`.to_i
      # meminfo shows KB and we need to convert to MB
      mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024 / 4
    else # sorry Windows folks, I can't help you
      cpus = 2
      mem = 1024
    end

    v.memory = mem
    v.cpus = cpus

    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
  end

  config.vm.provision "shell", path: "provisioning/yum_install.sh", privileged: true

  config.vm.provision "ansible" do |ansible|
      ansible.playbook = "provisioning/playbook.yml"
    end
end
