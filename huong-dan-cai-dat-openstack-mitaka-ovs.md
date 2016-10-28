#I. Cài đặt controller
##1. Thiết lập cấu hình cần thiết</h1>
- Dựa theo file script *ctl-1-ipadd.sh*
- Backup lại file cấu hình interface *cp /etc/network/interfaces /etc/network/interfaces.bk*

- Sửa file cấu hình interface
 - Mở *vi /etc/network/interfaces* và sửa như sau:

```sh
# LOOPBACK NET
auto lo
iface lo inet loopback

# MGNT NETWORK
auto eth0
iface eth0 inet static
address 10.10.10.80
netmask 255.255.255.0

# EXT NETWORK
auto eth1
iface eth1 inet static
address 172.16.69.80
netmask 255.255.255.0
gateway 172.16.69.1
dns-nameservers 8.8.8.8 8.8.4.4
```

- Sửa lại file hostname
 - *vi /etc/hostname*
 - Sửa thành dòng sau: *controller*

- Sửa lại file hosts
 - Backup lại cấu hình: 
```sh
cp /etc/hosts /etc/hosts.bk
```
 - Sửa *vi /etc/hosts* thành dòng sau: 
```sh 
		127.0.0.1		localhost controller
		10.10.10.80		$HOST_CTL
		10.10.10.81		compute1
		10.10.10.82		compute2
		10.10.10.83		cinder
```


- Thêm repo cho OpenStack Mitaka:
```sh
apt-get install software-properties-common -y
add-apt-repository cloud-archive:mitaka -y
apt-get -y update && apt-get -y upgrade && apt-get -y dist-upgrade
```
- Khởi động lại server sau khi đã upgrade xong.


##2. Cài đặt các thành phần hỗ trợ
###a. Cài đặt CRUDINI
```sh
apt-get -y install python-pip*
pip install https://pypi.python.org/packages/source/c/crudini/crudini-0.7.tar.gz
```

###b. Cài đặt python client
```sh
apt-get -y install python-openstackclient
```

###c. Cài đặt NTP
```sh
apt-get -y install chrony
```
- Back up lại cấu hình chrony
```sh
cp /etc/chrony/chrony.conf /etc/chrony/chrony.conf.bk
```

- Sửa file cấu hình, comment các dòng sau:
```sh 
server 0.debian.pool.ntp.org offline minpoll 8
server 1.debian.pool.ntp.org offline minpoll 8
server 2.debian.pool.ntp.org offline minpoll 8
server 3.debian.pool.ntp.org offline minpoll 8
```
 - Thêm vào các dòng sau:
```sh
server 1.vn.pool.ntp.org iburst
server 0.asia.pool.ntp.org iburst
server 3.asia.pool.ntp.org iburst
```
 - Khởi động lại Chrony: 
```sh
/etc/init.d/chrony restart
```


###d. Cài đặt RabbitMQ
- Cài đặt bằng lệnh: 
```sh
apt-get install rabbitmq-server -y
```
- Tạo và set quyền cho user dùng Rabbit:
```sh
rabbitmqctl add_user openstack tan124
rabbitmqctl set_permissions openstack ".*" ".*" ".*"
```

- Khởi động lại rabbitmq: 
```sh
service rabbitmq-server restart
```

###e. Cài đặt MySQL
- Cài đặt bằng lệnh: 
```sh
apt-get -y install mariadb-server python-mysqldb curl
```
 - Lưu ý: password mặc định cho mọi cài đặt là: tan124

- Cấu hình MySQL
- Tạo file cấu hình cho OpenStack: 
```sh
touch /etc/mysql/conf.d/mysqld_openstack.cnf
```
 - Thêm đoạn sau vào file
```sh 
		[mysqld]
		bind-address = 0.0.0.0

		default-storage-engine = innodb
		innodb_file_per_table
		collation-server = utf8_general_ci
		init-connect = 'SET NAMES utf8'
		character-set-server = utf8
```

- Restart lại MySQL: 
```sh
service mysql restart
```

##3. Cài đặt Keystone
- Khởi tạo DB cho keystone
```sh
CREATE DATABASE keystone;
GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' IDENTIFIED BY 'tan124';
GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' IDENTIFIED BY 'tan124';
FLUSH PRIVILEGES;
```

- Cài đặt
 - Không cho keystone khởi động sau khi cài:
```sh
echo "manual" > /etc/init/keystone.override
```
 - Cài đặt các gói cần thiết:
```sh
apt-get -y install keystone apache2 libapache2-mod-wsgi memcached python-memcache
```

- Cấu hình:
 - Backup lại file cấu hình memcache: 
```sh
cp /etc/memcached.conf /etc/memcached.conf.bk
```
 - Sửa lại dòng sau trong file /etc/memcached.conf: 
```sh
-l 127.0.0.1 --> -l 10.10.10.80
```
 - Backup lại file cấu hình keystone: 
```sh
cp /etc/keystone/keystone.conf /etc/keystone/keystone.conf.bk
```
- Sửa lại file /etc/keystone/keystone.conf:
- Trong thẻ [DEFAULT]
```sh
admin_token = tan124				
```
- Trong thẻ [database], tìm và sửa:
```sh
connection mysql+pymysql://keystone:tan124@10.10.10.80/keystone
```
- Trong thẻ [token]
```sh
provider = fernet
```

- Thực hiện dump DB keystone vào MySQL: 
```sh
su -s /bin/sh -c "keystone-manage db_sync" keystone
```
- Tạo token: 
```sh
keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone
```
- Mở file */etc/apache2/apache2.conf* và thêm dòng sau vào cuối file:
```sh
ServerName 10.10.10.80
```
- Tạo file cấu hình apache cho keystone: *vi /etc/apache2/sites-available/wsgi-keystone.conf*

```sh
Listen 5000
Listen 35357

<VirtualHost *:5000>
    WSGIDaemonProcess keystone-public processes=5 threads=1 user=keystone group=keystone display-name=%{GROUP}
    WSGIProcessGroup keystone-public
    WSGIScriptAlias / /usr/bin/keystone-wsgi-public
    WSGIApplicationGroup %{GLOBAL}
    WSGIPassAuthorization On
    <IfVersion >= 2.4>
      ErrorLogFormat "%{cu}t %M"
    </IfVersion>
    ErrorLog /var/log/apache2/keystone.log
    CustomLog /var/log/apache2/keystone_access.log combined

    <Directory /usr/bin>
        <IfVersion >= 2.4>
            Require all granted
        </IfVersion>
        <IfVersion < 2.4>
            Order allow,deny
            Allow from all
        </IfVersion>
    </Directory>
</VirtualHost>

<VirtualHost *:35357>
    WSGIDaemonProcess keystone-admin processes=5 threads=1 user=keystone group=keystone display-name=%{GROUP}
    WSGIProcessGroup keystone-admin
    WSGIScriptAlias / /usr/bin/keystone-wsgi-admin
    WSGIApplicationGroup %{GLOBAL}
    WSGIPassAuthorization On
    <IfVersion >= 2.4>
      ErrorLogFormat "%{cu}t %M"
    </IfVersion>
    ErrorLog /var/log/apache2/keystone.log
    CustomLog /var/log/apache2/keystone_access.log combined

    <Directory /usr/bin>
        <IfVersion >= 2.4>
            Require all granted
        </IfVersion>
        <IfVersion < 2.4>
            Order allow,deny
            Allow from all
        </IfVersion>
    </Directory>
</VirtualHost>
```
- Tạo liên kết cho apache load file config vừa tạo: 
```sh
ln -s /etc/apache2/sites-available/wsgi-keystone.conf /etc/apache2/sites-enabled
```
- Khởi động lại apache2:
```sh
service apache2 restart
```
- Xóa file DB không cần thiết của keystone: 
```sh
rm -f /var/lib/keystone/keystone.db
```
- Tạo 03 biến môi trường để gen endpoint cho keystone:
```sh
			export OS_TOKEN=tan124
			export OS_URL=http://10.10.10.80:35357/v3
			export OS_IDENTITY_API_VERSION=3
```
- Tạo các endpoint, project, user, group cho OpenStack
```sh
openstack service create --name keystone --description "OpenStack Identity" identity
openstack endpoint create --region RegionOne identity public http://10.10.10.80:5000/v3
openstack endpoint create --region RegionOne identity internal http://10.10.10.80:5000/v3
openstack endpoint create --region RegionOne identity admin http://10.10.10.80:35357/v3

openstack domain create --description "Default Domain" default
openstack project create --domain default --description "Admin Project" admin
openstack user create admin --domain default --password tan124

openstack role create admin
openstack role add --project admin --user admin admin

openstack project create --domain default --description "Service Project" service
openstack project create --domain default --description "Demo Project" demo
openstack user create demo --domain default --password tan124

openstack role create user
openstack role add --project demo --user demo user
```
	
- Xóa 02 biến môi trường: 
```sh
unset OS_TOKEN OS_URL
```
- Tạo file biến môi trường cho user admin: *vi admin-openrc*
```sh
export OS_PROJECT_DOMAIN_NAME=default
export OS_USER_DOMAIN_NAME=default
export OS_PROJECT_NAME=admin
export OS_USERNAME=admin
export OS_PASSWORD=tan124
export OS_AUTH_URL=http://10.10.10.80:35357/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
```
	
- Thực thi file biến môi trường vừa tạo: 
```sh
source admin-openrc
```
- Tạo file biến môi trường cho user demo: *vi demo-openrc*
```sh
export OS_PROJECT_DOMAIN_NAME=default
export OS_USER_DOMAIN_NAME=default
export OS_PROJECT_NAME=demo
export OS_USERNAME=demo
export OS_PASSWORD=tan124
export OS_AUTH_URL=http://10.10.10.80:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
```
	
- Check lại keystone sau khi cài đặt: 
```sh
openstack token issue
```


##4. Cài đặt Glance
- Khởi tạo DB cho glance
```sh
CREATE DATABASE glance;
GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' IDENTIFIED BY 'tan124';
GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' IDENTIFIED BY 'tan124';
FLUSH PRIVILEGES;
```

- Tạo user và endpoint cho Glance:
- Export biến môi trường: 
```sh
source admin-openrc
```
- Tạo user và endpoint:
```sh
openstack user create glance --domain default --password tan124
openstack role add --project service --user glance admin
openstack service create --name glance --description "OpenStack Image service" image
openstack endpoint create --region RegionOne image public http://10.10.10.80:9292
openstack endpoint create --region RegionOne image internal http://10.10.10.80:9292
openstack endpoint create --region RegionOne image admin http://10.10.10.80:9292
```
	

- Cài đặt package Glance 
```sh
apt-get -y install glance
```
- Cấu hình GLANCE API:
- Backup lại file config: 
```sh
cp /etc/glance/glance-api.conf /etc/glance/glance-api.conf.bk
```
- Sửa file cấu hình: 
```sh
vi /etc/glance/glance-api.conf
```
- Sửa thẻ [database], xóa dòng *sqlite_db* và thêm dòng: 
```sh
connection = mysql+pymysql://glance:tan124@10.10.10.80/glance
```

- Sửa thẻ [keystone_authtoken]
```sh
auth_uri = http://10.10.10.80:5000
auth_url = http://10.10.10.80:35357
memcached_servers = 10.10.10.80:11211
auth_type = password
project_domain_name = default
user_domain_name = default
project_name = service
username = glance
password tan124
```

- Sửa thẻ [paste_deploy]
```sh
flavor = keystone
```

- Sửa thẻ [glance_store]
```sh
default_store = file
stores = file,http
filesystem_store_datadir = /var/lib/glance/images/
```

- Backup lại file cấu hình */etc/glance/glance-registry.conf*
```sh
cp /etc/glance/glance-registry.conf /etc/glance/glance-registry.conf.bk
```
- Sửa file: /etc/glance/glance-registry.conf
- Thẻ [DEFAULT] thêm
```sh
verbose = True
```
- Thẻ [database] thêm
```sh
connection = mysql+pymysql://glance:$GLANCE_DBPASS@$CTL_MGNT_IP/glance
```
- Thẻ [database] xóa
```sh
sqlite_db
```
- Thẻ [keystone_authtoken] sửa
```sh
auth_uri = http://10.10.10.80:5000
auth_url = http://10.10.10.80:35357
memcached_servers = 10.10.10.80:11211
auth_type = password
project_domain_name = default
user_domain_name = default
project_name = service
username = glance
password = tan124
```
- Thẻ [paste_deploy] sửa
```sh
flavor = keystone
```

- Đồng bộ DB Glance
```sh
su -s /bin/sh -c "glance-manage db_sync" glance
```

- Restart Glance service
```sh
service glance-registry restart
service glance-api restart
```

- Kéo file cirros image:
```sh
mkdir images
cd images
wget http://download.cirros-cloud.net/0.3.4/cirros-0.3.4-x86_64-disk.img
```

- Import image to glance
```sh
openstack image create "cirros" \
    --file cirros-0.3.4-x86_64-disk.img \
    --disk-format qcow2 --container-format bare \
    --public
```

- Kiểm tra lại Glance image
```sh
openstack image list
```


#II. Cài đặt Compute

##Tham khảo



