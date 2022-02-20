# Vagrant setup to create a local docker lab (for Swarm)
In order to test locally some swarm cluster features, I created this repo to ease a docker swarm cluster creation and deletion with virtual machines.

## Requirements

### Host packages requirements
The host must have :
* __[Git](https://git-scm.com/)__ to clone this repository (you can download and unzip too)
* __[Vagrant](https://www.vagrantup.com/)__ to handle virtual machines
* __[VirtualBox](https://www.virtualbox.org/)__ as Hypervisor
* __[Ansible](https://docs.ansible.com/ansible/latest/index.html#)__ for provisionning

### Host system requirements
* OS : Linux *I use pop OS 21.10*
* CPUs number : 8 (at least)
* RAM : 8GB (at least)
* Free disk space : about 75GB...

*__Notes__*: I use virtualbox as virtual machine hypervisor.

As I have disk space limitations on my desktop, I did the following setup via CLI to use my external drive as a disk space extension:

```sh
# Check the current default path for the VMs
vboxmanage list systemproperties | grep 'Default machine folder'

# Set a new path for the VMs to be created.
# Check the external drive(s) mount path with `df -h` or `fdik -l`
vboxmanage setproperty machinefolder <path/to/the/external_drive>

# Ensure that the new setting is applied
vboxmanage list systemproperties | grep 'Default machine folder'
```

## Clone the repo
```sh
# Clone the repo and change directory
git clone git@github.com:thkevin/vagrant_swarm_vms.git

# Get into the project directory
cd vagrant_swarm_vms
```

## Create the cluster nodes
```sh
# Vagrant up!
vagrant up
```

## Access a node in the cluster

```sh
# To connect with one of the nodes, use the names manager1, worker2, worker3
# Example 1
vagrant ssh worker-2

# Example 2
vagrant ssh manager-1
```

## Setup the swarm cluster
I didn't set up the swarm cluster during the provisionning for learning purposes.

I think that the user should start from the beginning and create it himself with `docker swarm init` and `docker swarm join` commands.

However, you can uncomment the line `ansible.tags = "swarm"` in the ansible provisioner section in the Vagrantfile to automate the cluster setup during ansible provisioning.

## Destroy the cluster
This command will power off and destroy all the virtual machines of the cluster.

```sh
vagrant destroy [-f]
```
