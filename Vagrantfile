$yum = <<SCRIPT
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm
setenforce 0
sudo sed -i 's/^SELINUX=.*/SELINUX=permissive/g' /etc/selinux/config
cat /etc/selinux/config | grep SELINUX=
yum install -y net-tools vim git
# http://www.tecmint.com/install-php-5-6-on-centos-7/ 
yum-config-manager --enable remi-php56
yum install -y httpd
yum install -y php php-mcrypt php-cli php-gd php-curl php-mysql php-ldap php-zip php-fileinfo php-bcmath php-mbstring php-xml php-pear php-cgi php-common php-snmp php-gettext
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

$common = <<SCRIPT
export DOCKER_HOST_IP=192.168.99.100
SCRIPT

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"

  config.vm.provider :virtualbox do |virtualbox, override|
    virtualbox.memory = 1024
    override.vm.box_download_checksum_type = "sha256"
    override.vm.box_download_checksum = "b24c912b136d2aa9b7b94fc2689b2001c8d04280cf25983123e45b6a52693fb3"
    override.vm.box_url = "https://cloud.centos.org/centos/7/vagrant/x86_64/images/CentOS-7-x86_64-Vagrant-1809_01.VirtualBox.box"
  end

  config.vm.provision "shell", inline: $yu

  config.vm.provision "shell", inline: $commonm

  config.vm.provision "shell", inline: $sql

  config.vm.provision "shell", inline: $zabbix

  config.vm.network "private_network", ip: "192.168.99.100" 
#  config.vm.network "public_network", ip: "192.168.99.100"
end
