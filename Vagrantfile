# -*- mode: ruby -*-
# vi: ft=ruby :

MACHINES = {
    :"kernel-update" => {
        :box_name => "ubuntu/jammy64",
        :box_version => "0",
        :cpus => 4,
        :memory => 4096,
    }
}

Vagrant.configure("2") do |config|
    MACHINES.each do |boxname, boxconfig|
        config.vm.define boxname do |box|
            box.vm.box = boxconfig[:box_name]
            box.vm.box_version = boxconfig[:box_version]
            box.vm.host_name = boxname.to_s;
            box.vm.provider "virtualbox" do |v|
	        v.memory = boxconfig[:memory]
                v.cpus = boxconfig[:cpus]
            end
        end    
    end
    config.vm.provision "shell", inline: <<-SHELL
    cd /vagrant
    wget https://kernel.ubuntu.com/mainline/v6.5/amd64/linux-headers-6.5.0-060500-generic_6.5.0-060500.202308271831_amd64.deb
    wget https://kernel.ubuntu.com/mainline/v6.5/amd64/linux-headers-6.5.0-060500_6.5.0-060500.202308271831_all.deb
    wget https://kernel.ubuntu.com/mainline/v6.5/amd64/linux-image-unsigned-6.5.0-060500-generic_6.5.0-060500.202308271831_amd64.deb
    wget https://kernel.ubuntu.com/mainline/v6.5/amd64/linux-modules-6.5.0-060500-generic_6.5.0-060500.202308271831_amd64.deb
    sudo dpkg -i linux-headers-*.deb
    sudo apt -f install
    sudo dpkg -i linux-modules-*.deb
    sudo dpkg -i linux-image-*.deb
    rm -f *.deb    
    sudo reboot
    SHELL
end
