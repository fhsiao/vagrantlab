# vagrantlab

Pre-Install
Vagrant 2.2.2
VirtualBox 5.2.22

Set-up vagrant keys: (For docker-machine to ssh to the VM)
ssh-keygen -t rsa -f .vagrant/machines/default/virtualbox/private_key

Run:
vagrant up

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
