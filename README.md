# Обновление ядра Linux в дистрибутиве Ubuntu # 

### Обновить ядро Linux в дистрибутиве Ubuntu с помощью deb-пакетов. #####
&emsp; Для выполнения задания использовался образ (бокс) ubuntu/jammy64 версии 20240301.0.0,<br/> 
полученный по ссылке: <https://app.vagrantup.com/ubuntu/boxes/jammy64/><br/> 
&emsp; Для запуска виртуальной машины исполнен Vagrantfile следующего содержания:

``` ruby

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
  # переход в общую между хостовой и виртуальной машинами директорию, которая будет использоваться, как рабочая
  cd /vagrant

  # получение deb-пакетов необходимых для установки нового ядра
  wget https://kernel.ubuntu.com/mainline/v6.5/amd64/linux-headers-6.5.0-060500-generic_6.5.0-060500.202308271831_amd64.deb
  wget https://kernel.ubuntu.com/mainline/v6.5/amd64/linux-headers-6.5.0-060500_6.5.0-060500.202308271831_all.deb
  wget https://kernel.ubuntu.com/mainline/v6.5/amd64/linux-image-unsigned-6.5.0-060500-generic_6.5.0-060500.202308271831_amd64.deb
  wget https://kernel.ubuntu.com/mainline/v6.5/amd64/linux-modules-6.5.0-060500-generic_6.5.0-060500.202308271831_amd64.deb
	
  # блок команд, устанавливающих новое ядро из полученных deb-пакетов
  sudo dpkg -i linux-headers-*.deb
  sudo apt -f install
  sudo dpkg -i linux-modules-*.deb
  sudo dpkg -i linux-image-*.deb
	
  # удаление ранее загруженных deb-пакетов 
  rm -f *.deb

  # перезагрузка виртуальной машины
  sudo reboot
  SHELL
end

```
Вывод команды до обновления ядра:
```shell
vagrant@kernel-update:~$ uname -r
5.15.0-97-generic
```	
Вывод команды после обновления ядра:
```shell
vagrant@kernel-update:~$ uname -r 
6.5.0-060500-generic
```
&emsp; Для бокса ubuntu/jammy64 версии 20240301.0.0 после установки нового ядра манипуляции по восстановлению VirtualBox Shared Folders не потребовались. Указанный ресурс сохранил свою доступность и работоспособность.
