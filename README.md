# bonding-config-in-ubuntu

This project is inteded as an experiment to learn about network interface `Bonding` in Ubuntu.

The aim of the experiment is to combine 4 network interfaces into a sigle link with a single IP address, using the steps found in the [Ubuntu Documentation](https://help.ubuntu.com/community/UbuntuBonding), and to develop a set of `Ansible` playbooks that automate this process as much as possible.


## Testbed 

`Vagrant` and `VirtualBox` are used to setup a VM called `testbed` running Ubuntu 22.04 that is configured with 5 network interfaces with static IP adresses. One of these is the service interface with IP `192.168.30.10` by which the VM can be acessed.


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

## Playbooks to setup a bond

To configure bonding with Ansible, a file called `inventory` should contain the `IP address` of the server, a `username` with sudo permission and the path to the ssh `private key`. After that, the set of ansible playbooks can be executed with the `ansible-playbook` command.

- #### `show_interfaces` playbook
  This playbook is used check how the network is configured on the server and serves as a reference for the `setup_bonding` playbook. It executes the `ip` command with the appropiate arguments to print out network interfaces and IP information.

  To execute playbook, run:
  ```bash
  ansible-playbook playbooks/show_interfaces.yml -i inventory 
  ```


- #### `setup_bonding` playbook
  This playbook needs to be provided with the folowing variables: 
  - `interfaces`: a comma separated list of interfaces 
  - `bond_ip`: a static IP in the CIDR notation 
  - `bond_gateway`: default gateway IP 
  
  It performs the following tasks:
  - Ensures kernel support for bonding in the OS
  - Binds the provided `interfaces` in a new interface called `bond0`
    Uses the template file `templates/netplan-config.j2` to create the `/etc/netplan/01-bond-cfg.yaml` netplan configuration file in the server.
    The template defines `bond0` with the folowing parameters:
     - `mode`: 802.3ad
     - `bond_lacp_rate`: fast
     - `mii-monitor-interval`: 100
     - `transmit-hash-policy`: layer2

    and assigns it a static IP address with the provided `bond_ip` and `bond_gateway`.
  - Applies the netplan config file by executing the `netplan apply` command
  
  To execute playbook, run:
  ```bash
  # replace the placholders with the intended values and run below to setup the bond
  ansible-playbook playbooks/setup_bonding.yml \
     -i inventory 
     -e interfaces="if_name_1,if_name_2,if_name_n" \
     -e bond_ip="*.*.*.*/*" \
     -e bond_gateway="*.*.*.*" 
  ```


## Example workflow to configure a 4 interface bond on the testbed:
1. Create a file called inventory and add the testbed access information to it
   ```bash
   touch inventory
   echo "[testbed_server]\n" > inventory
   echo "192.168.30.10 ansible_user=vagrant ansible_ssh_private_key_file=.vagrant/machines/testbed/virtualbox/private_key\n" >> inventory
   ```

2. Print out the network interfaces and ip information
   ```bash
   ansible-playbook playbooks/show_interfaces.yml \
      --ssh-extra-args="-o StrictHostKeyChecking=no" \
      -i inventory 
   ```

3. Configure a bond with the provided interfaces
   ```bash
   ansible-playbook playbooks/setup_bonding.yml \
      --ssh-extra-args="-o StrictHostKeyChecking=no" \
      -i inventory \
      -e interfaces="enp0s9,enp0s10,enp0s16,enp0s17" \
      -e bond_ip="192.168.30.11/24" \
      -e bond_gateway="192.168.30.1" 
   ```

4. Print out the network interfaces and ip information again to verify the bond is correctly configured
   ```bash
   ansible-playbook playbooks/show_interfaces.yml \
      --ssh-extra-args="-o StrictHostKeyChecking=no" \
      -i inventory 
   ```
  
