# bonding-config-in-ubuntu

This project is inteded an an experiment to learn about `Bonding` network interfaces in Ubuntu.

The aim of the experiment is to combine 4 network interfaces into a sigle link with a single IP address, using the steps found in the [Ubuntu Documentation](https://help.ubuntu.com/community/UbuntuBonding), and to develop a set of `Ansible` playbooks that automate this process as much as possible.

## Development
- [x] configure testbed VM
- [ ] ensure_kernel_supports_bonding playbook
- [x] show_interfaces playbook
- [ ] setup_bonding playbook

## Testbed 

`Vagrant` and `VirtualBox` are used to setup a VM called `testbed` running Ubuntu 22.04 that is configured with 5 network interfaces with static IP adresses. One of these is the service interface with IP `192.168.30.10` by wich the VM can be acessed. 

The commands below can be used to manage the `testbed`:
```bash
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

## Playbooks to setup bonding

After the `testbed` VM is up, it can be interacted with using Ansible. This is done by first defining a file called `inventory` that should contain the `IP adress` of the server, a `username` with sudo permission and the path to the ssh `private key`. 

After that, the set of ansible playbooks can be executed with the `ansible-playbook` command.

Here are the steps to configure bonding on the `testbed`:

1. Ensure kernel support for bonding
   ```bash
   ansible-playbook -i inventory playbooks/ensure_kernel_supports_bonding.yml
   ```

2. List the network interfaces on the server:
   ```bash
   ansible-playbook -i inventory playbooks/show_interfaces.yml
   ```

3. Configure bonding on the intended interfaces:
   ```bash
   ansible-playbook -i inventory -e "IF1=<if_name> IF2=<if_name> IF3=<if_name> IF4=<if_name>" playbooks/setup_bonding.yml