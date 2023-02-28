# bonding-config-in-ubuntu

This project is inteded an an experiment to learn about `Bonding` network interfaces in Ubuntu.

`Vagrant` and `VirtualBox` are used to setup a VM called `testbed` running Ubuntu 22.04 that is configured with 5 network interfaces with static IP adresses. One of the addresses is the service address, with the IP of `192.168.30.10`, by wich the VM can be acessed. 

The aim of the experiment is to combine 4 network interfaces into a sigle link with a single IP address using the steps found in this oficial [ubuntu bonding guide](https://help.ubuntu.com/community/UbuntuBonding) and, to create a set `Ansible` playbooks to automate this process as much as possible.

## Run the Testbed VM

The commands below can be used to manage the `testbed`:
```bash
# first, cd to the root of the project folder

# start
vagrant up

# connect with SSH
vagrant ssh

# suspend
vagrant suspend

# resume
vagrant resume

# stop and delete
vagrant destroy

```

## Setup Bonding

After the `testbed` VM is up, it can be interacted with using `Ansible`. This is done by first defining a file called `invantory` that should contain the `Ip adress` of the server, a `username` with sudo permission and the path to the ssh `private key`. Then, the set of ansible playbooks can be executed with the `ansible-playbook` command.

Here are the steps to configure linking and trunking on the `testbed`:

1. List the network interfaces on the server:
   ```bash
   # cd to the root of the project folder, then run:
   ansible-playbook -i inventory playbooks/show_interfaces.yml
   ```

2. Configure bonding on the intended interfaces:
   ```bash
   # cd to the root of the project folder, then run:
   ansible-playbook -i inventory -e "IF1=<if_name> IF2=<if_name> IF3=<if_name> IF4=<if_name>" playbooks/setup_bonding.yml