$yum = <<SCRIPT
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm
yum install -y net-tools vim git 
SCRIPT

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"

  config.vm.provider :virtualbox do |virtualbox, override|
    virtualbox.memory = 4096
    virtualbox.cpus = 2
    override.vm.box_download_checksum_type = "sha256"
    override.vm.box_download_checksum = "b24c912b136d2aa9b7b94fc2689b2001c8d04280cf25983123e45b6a52693fb3"
    override.vm.box_url = "https://cloud.centos.org/centos/7/vagrant/x86_64/images/CentOS-7-x86_64-Vagrant-1809_01.VirtualBox.box"
  end

  config.vm.provision "shell", inline: $yum

  config.vm.network "forwarded_port", guest: 2181, host: 2181
  config.vm.network "forwarded_port", guest: 8081, host: 8081
  config.vm.network "forwarded_port", guest: 8082, host: 8082
  config.vm.network "forwarded_port", guest: 8088, host: 8088
  config.vm.network "forwarded_port", guest: 9021, host: 9021
  config.vm.network "forwarded_port", guest: 9092, host: 9092

  config.vm.network "private_network", ip: "192.168.99.100" 
#  config.vm.network "public_network", ip: "192.168.99.100"
end
