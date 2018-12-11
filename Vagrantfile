require 'getoptlong'

opts = GetoptLong.new(
  [ '--stack', GetoptLong::OPTIONAL_ARGUMENT ]
)

stack=''

opts.each do |opt, arg|
  case opt
    when '--stack'
      stack=arg
  end
end

$yum = <<SCRIPT
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm
yum install -y net-tools vim git mlocate ngrep
SCRIPT

$yumZabbix = <<SCRIPT
# http://www.tecmint.com/install-php-5-6-on-centos-7/ 
yum-config-manager --enable remi-php56
yum install -y httpd
yum install -y php php-mcrypt php-cli php-gd php-curl php-mysql php-ldap php-zip php-fileinfo php-bcmath php-mbstring php-xml php-pear php-cgi php-common php-snmp php-gett    ext
echo $'ServerSignature Off\nServerTokens Prod' >> /etc/httpd/conf/httpd.conf
systemctl restart httpd.service
php -v
yum install -y mariadb-server mariadb-client mariadb-devel
sudo systemctl start mariadb
sudo systemctl status mariadb
sudo systemctl enable mariadb
rpm -i https://repo.zabbix.com/zabbix/4.1/rhel/7/x86_64/zabbix-release-4.1-1.el7.noarch.rpm
yum-config-manager --enable rhel-7-server-optional-rpms
yum install -y zabbix-server-mysql zabbix-agent zabbix-proxy-mysql zabbix-get zabbix-web-mysql
SCRIPT

$offSlinux = <<SCRIPT
setenforce 0
sudo sed -i 's/^SELINUX=.*/SELINUX=permissive/g' /etc/selinux/config
cat /etc/selinux/config | grep SELINUX=
SCRIPT

$sql = <<SCRIPT
mysql -u root <<-EOF
UPDATE mysql.user SET Password=PASSWORD('zabbix') WHERE User='root';
DELETE FROM mysql.user WHERE User='root' AND Host NOT IN ('localhost', '127.0.0.1', '::1');
DELETE FROM mysql.user WHERE User='';
DELETE FROM mysql.db WHERE Db='test' OR Db='test_%';
FLUSH PRIVILEGES;
create database zabbix character set utf8 collate utf8_bin;
grant all privileges on zabbix.* to zabbix@localhost identified by 'zabbix';
EOF
zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -pzabbix zabbix 
sudo systemctl restart mariadb
SCRIPT

$zabbix = <<SCRIPT
echo $'DBPassword=zabbix\n' >> /etc/zabbix/zabbix_server.conf
echo $'php_value date.timezone America/Denver\n' >> /etc/httpd/conf.d/zabbix.conf
systemctl restart zabbix-server zabbix-agent httpd
systemctl enable zabbix-server zabbix-agent httpd
SCRIPT

Vagrant.configure(2) do |config|
  case
  when stack == ''
    config.vm.box = "centos/7"
    config.vm.provider :virtualbox do |virtualbox, override|
      virtualbox.memory = 4096
      virtualbox.cpus = 2
      override.vm.box_download_checksum_type = "sha256"
      override.vm.box_download_checksum = "b24c912b136d2aa9b7b94fc2689b2001c8d04280cf25983123e45b6a52693fb3"
      override.vm.box_url = "https://cloud.centos.org/centos/7/vagrant/x86_64/images/CentOS-7-x86_64-Vagrant-1809_01.VirtualBox.box"
    end
    config.vm.provision "shell", inline: $yum
    config.vm.provision "shell", inline: $offSlinux
    config.vm.network "private_network", ip: "192.168.99.99"
    #config.vm.network "public_network", ip: "192.168.99.99"
  when stack == 'kafka'
    # multi-machine definitions is lazy loaded and better to use a constant variable
    # https://www.vagrantup.com/docs/vagrantfile/tips.html
    config.vm.define 'kafka' do |kafka|
      kafka.vm.box = "centos/7"
      kafka.vm.provider :virtualbox do |virtualbox, override|
        virtualbox.memory = 4096
        virtualbox.cpus = 2
        override.vm.box_download_checksum_type = "sha256"
        override.vm.box_download_checksum = "b24c912b136d2aa9b7b94fc2689b2001c8d04280cf25983123e45b6a52693fb3"
        override.vm.box_url = "https://cloud.centos.org/centos/7/vagrant/x86_64/images/CentOS-7-x86_64-Vagrant-1809_01.VirtualBox.box"
      end
    end
    config.vm.provision "shell", inline: $yum
    config.vm.provision "shell", inline: $offSlinux
    config.vm.network "forwarded_port", guest: 2181, host: 2181
    config.vm.network "forwarded_port", guest: 8081, host: 8081
    config.vm.network "forwarded_port", guest: 8082, host: 8082
    config.vm.network "forwarded_port", guest: 8088, host: 8088
    config.vm.network "forwarded_port", guest: 9021, host: 9021
    config.vm.network "forwarded_port", guest: 9092, host: 9092
    config.vm.network "private_network", ip: "192.168.99.100"
    #config.vm.network "public_network", ip: "192.168.99.100"
    config.trigger.after :all do |trigger|
      trigger.name = "Kafka Stack"
      trigger.info = "Installing Kafka stack!"
      trigger.run = {path: "docker-machine.build"}
    end
  when stack == 'zabbix'
    # multi-machine definitions is lazy loaded and better to use a constant variable
    # https://www.vagrantup.com/docs/vagrantfile/tips.html
    config.vm.define 'zabbix' do |zabbix|
      zabbix.vm.box = "centos/7"
      zabbix.vm.provider :virtualbox do |virtualbox, override|
        virtualbox.memory = 4096
        virtualbox.cpus = 2
        override.vm.box_download_checksum_type = "sha256"
        override.vm.box_download_checksum = "b24c912b136d2aa9b7b94fc2689b2001c8d04280cf25983123e45b6a52693fb3"
        override.vm.box_url = "https://cloud.centos.org/centos/7/vagrant/x86_64/images/CentOS-7-x86_64-Vagrant-1809_01.VirtualBox.box"
      end
    end
    config.vm.provision "shell", inline: $yum
    config.vm.provision "shell", inline: $yumZabbix
    config.vm.provision "shell", inline: $offSlinux
    config.vm.provision "shell", inline: $sql
    config.vm.provision "shell", inline: $zabbix
    config.vm.network "forwarded_port", guest: 80, host: 80
    config.vm.network "private_network", ip: "192.168.99.101"
    #config.vm.network "public_network", ip: "192.168.99.101"
  end
end
