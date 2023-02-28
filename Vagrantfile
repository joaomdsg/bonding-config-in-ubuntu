Vagrant.configure("2") do |config|
    
    config.vm.define "testbed" do |testbed|

        testbed.vm.box = "ubuntu/focal64"

        config.vm.network "private_network", ip: "192.168.30.10" #service

        config.vm.network "private_network", ip: "192.168.30.11" #pair1_if1
        config.vm.network "private_network", ip: "192.168.30.12" #pair1_if2

        config.vm.network "private_network", ip: "192.168.30.13" #pair2_if1
        config.vm.network "private_network", ip: "192.168.30.14" #pair2_if2

        testbed.vm.provider "virtualbox" do |vb|
            vb.memory = "512"
            vb.cpus = 1
            
        end
    end
end