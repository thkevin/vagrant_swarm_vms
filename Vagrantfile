# -*- mode: ruby -*-
# vi: set ft=ruby :

LAB_BOX = "centos/stream8"

MANAGERS = ["manager-1"]
WORKERS = ["worker-1", "worker-2"]
MANAGERS_COUNT = MANAGERS.size
WORKERS_COUNT = WORKERS.size

WORKERS_VM_NAMES = (1..WORKERS_COUNT).map{|i| "worker-#{i}"}
MANAGERS_VM_NAMES = (1..MANAGERS_COUNT).map{|i| "manager-#{i}"}

NODES = MANAGERS.concat(WORKERS)
NODES_COUNT = NODES.size

LAB_USER = "labdocker"
LAB_GROUP = "labdocker"

VAGRANT_DIR = File.dirname(__FILE__)
KEYS_DIR = "#{VAGRANT_DIR}/provisioning/files/keys"

INIT_PACKAGES = ["python3"]

Vagrant.require_version ">= 2.2.0"

Vagrant.configure("2") do |config|

  # Shared virtualbox settings
  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
    v.cpus = 2
  end

  # Install Python 3 for ansible interpreter
  config.vm.provision "shell" do |s|
    s.inline = "sudo yum install -y --nogpgcheck #{INIT_PACKAGES.join(" ")}"
  end

  # Set lab_user as default user to connect to VMs
  VAGRANT_COMMAND = ARGV[0]
  if VAGRANT_COMMAND == "ssh"
    config.ssh.username = "#{LAB_USER}"
    config.ssh.private_key_path = "#{KEYS_DIR}/userkey"
    config.ssh.insert_key = 'true'
  end

  # Nodes
  NODES.each_with_index do |n, index|
    config.vm.define "#{n}" do |node|
      node.vm.box = LAB_BOX
      node.vm.hostname = "#{n}"
      node.vm.network  :private_network,
        virtualbox__intnet: "lab_network",
        ip: "172.168.99.#{10 + index}",
        netmask: "255.255.255.0",
        auto_config: true
      node.vm.disk :disk, size: "25GB", primary: true
      node.vm.provider "virtualbox" do |v|
        v.name = NODES[index - 1]
      end

      if "#{index + 1}" == "#{NODES_COUNT}"
        node.vm.provision :ansible do |ansible|
          # Disable default limit to connect to all the machines
          ansible.limit = "all"
          ansible.playbook = "provisioning/docker_vms.yml"
          # Uncomment the following line to automate cluster initialization (init swarm in manager and join workers)
          # ansible.tags = "swarm"
          # Uncomment the following line for verbose mode
          # ansible.verbose = "vvv"
          ansible.groups = {
            "workers" => WORKERS_VM_NAMES,
            "managers" => MANAGERS_VM_NAMES
          }
          ansible.extra_vars = {
            lab_user: "#{LAB_USER}",
            lab_group: "#{LAB_GROUP}",
            keypair_dir: "#{KEYS_DIR}"
          }
        end
      end

    end
  end
end
