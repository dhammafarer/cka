# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

NUM_MASTER_NODE = 1
NUM_WORKER_NODE = 2

IP_NW = "192.168.56."
MASTER_IP_START = 10
NODE_IP_START = 20

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "generic/debian11"
  config.ssh.insert_key = false
  config.vm.provider "libvirt"

  config.vm.synced_folder '.', '/vagrant', disabled: true

  (1..NUM_MASTER_NODE).each do |i|
    config.vm.define "kubemaster#{i}" do |node|
      config.vm.provider :libvirt do |lv|
        lv.qemu_use_session = false
        lv.memory = 2048
        lv.cpus = 2
      end
      node.vm.hostname = "kubemaster#{i}"
      node.vm.network :private_network, ip: IP_NW + "#{MASTER_IP_START + i}"
      node.vm.network "forwarded_port", guest: 22, host: "#{2710 + i}"

      if i == NUM_MASTER_NODE
         node.vm.provision :ansible do |ansible|
           ansible.inventory_path = "inventory.ini"
           ansible.limit = "kubemasters"
           ansible.playbook = "playbooks/provision/main.yml"
         end
       end
    end
  end

  (1..NUM_WORKER_NODE).each do |i|
    config.vm.define "kubenode0#{i}" do |node|
      config.vm.provider :libvirt do |lv|
        lv.qemu_use_session = false
        lv.memory = 2048
        lv.cpus = 2
      end
      node.vm.hostname = "kubenode0#{i}"
      node.vm.network :private_network, ip: IP_NW + "#{NODE_IP_START + i}"
      node.vm.network "forwarded_port", guest: 22, host: "#{2720 + i}"

      if i == NUM_WORKER_NODE
         node.vm.provision :ansible do |ansible|
           ansible.inventory_path = "inventory.ini"
           ansible.limit = "kubenodes"
           ansible.playbook = "playbooks/provision/main.yml"
         end
       end
    end
  end
end
