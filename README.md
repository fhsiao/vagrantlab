# vagrantlab

Pre-Install
Vagrant 2.2.2
VirtualBox 5.2.22

Set-up vagrant keys: (For docker-machine to ssh to the VM)

Generate a new Key:

ssh-keygen -t rsa -f .vagrant/machines/default/virtualbox/private_key

Generate public key from private key:

ssh-keygen -y -f .vagrant/machines/default/virtualbox/private_key > .vagrant/machines/default/virtualbox/private_key.pub

# Run:
vagrant up

vagrant ssh

or

ssh -i .vagrant/machines/default/virtualbox/private_key vagrant@192.168.99.100


# Branch: zabbix

Install zabbix

# zabbix credentials
DB uname: zabbix

DB pword: zabbix

Web uname: Admin

Web pword: zabbix

# Branch: kafka

Set up Docker environment using docker-machine with "default Centos7 VM" instead of boot2docker 

Execute: docker-machine.build
