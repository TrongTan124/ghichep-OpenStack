#I. Cài đặt controller
<h1>1. Thiết lập cấu hình cần thiết</h1>
<ul>Dựa theo file script <i>ctl-1-ipadd.sh</i></ul>
<ul>Backup lại file cấu hình interface
	<p><i>cp /etc/network/interfaces /etc/network/interfaces.bk</i></p>
</ul>
<ul>Sửa file cấu hình interface
	<li><i>vi /etc/network/interfaces</i> và sửa như sau: </li>
```
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
</ul>
<ul>Sửa lại file hostname
	<li><i>vi /etc/hostname</i></li>
	<li>Sửa thành dòng sau: <i>controller</i></li>
</ul>
<ul>Sửa lại file hosts
	<li>Backup lại cấu hình: <i>cp /etc/hosts /etc/hosts.bk</i></li>
	<li><i>vi /etc/hosts</i></li>
	<li>Sửa thành dòng sau: 
	<p>
		127.0.0.1		localhost controller
		10.10.10.80		$HOST_CTL
		10.10.10.81		compute1
		10.10.10.82		compute2
		10.10.10.83		cinder
	</p>
	</li>
</ul>
<ul>Thêm repo cho OpenStack Mitaka:</ul>
```
apt-get install software-properties-common -y
add-apt-repository cloud-archive:mitaka -y
apt-get -y update && apt-get -y upgrade && apt-get -y dist-upgrade
```

<ul>Khởi động lại server sau khi đã upgrade xong.</ul>


<h1>2. Cài đặt các thành phần hỗ trợ</h1>
<h2>a. Cài đặt CRUDINI</h2>
<ul><i>apt-get -y install python-pip</i></ul>
<ul><i>pip install https://pypi.python.org/packages/source/c/crudini/crudini-0.7.tar.gz</i></ul>

<h2>b. Cài đặt python client</h2>
<ul><i>apt-get -y install python-openstackclient</i></ul>

<h2>c. Cài đặt NTP</h2>
<ul><i>apt-get -y install chrony</i></ul>
<ul>Back up lại cấu hình chrony
	<li>cp /etc/chrony/chrony.conf /etc/chrony/chrony.conf.bk</li>
</ul>
<ul>Sửa file cấu hình
	<li>Comment các dòng sau:
	<p>
		server 0.debian.pool.ntp.org offline minpoll 8
		server 1.debian.pool.ntp.org offline minpoll 8
		server 2.debian.pool.ntp.org offline minpoll 8
		server 3.debian.pool.ntp.org offline minpoll 8
	</p>
	</li>
	<li>Khởi động lại Chrony: <i>/etc/init.d/chrony restart</i></li>
</ul>

<h2>d. Cài đặt RabbitMQ</h2>
<ul>Cài đặt bằng lệnh: <i>apt-get install rabbitmq-server -y</i></ul>
<ul>Tạo và set quyền cho user dùng Rabbit:
	<li><i>rabbitmqctl add_user openstack tan124</i></li>
	<li><i>rabbitmqctl set_permissions openstack ".*" ".*" ".*"</i></li>
</ul>
<ul>Khởi động lại rabbitmq: <i>service rabbitmq-server restart</i></ul>

<h2>e. Cài đặt MySQL</h2>
<ul>Cài đặt bằng lệnh: <i>apt-get -y install mariadb-server python-mysqldb curl</i>
	<li>Lưu ý: password mặc định cho mọi cài đặt là: tan124</li>
</ul>
<ul>Cấu hình MySQL
	<li>Tạo file cấu hình cho OpenStack: <i>touch /etc/mysql/conf.d/mysqld_openstack.cnf</i></li>
	<li>Thêm đoạn sau vào file
	<p>
		[mysqld]
		bind-address = 0.0.0.0

		default-storage-engine = innodb
		innodb_file_per_table
		collation-server = utf8_general_ci
		init-connect = 'SET NAMES utf8'
		character-set-server = utf8
	</p>
	</li>
</ul>
<ul>Restart lại MySQL: <i>service mysql restart</i></ul>

<h1>3. Cài đặt Keystone</h1>
<ul>Khởi tạo DB cho keystone
	<li>CREATE DATABASE keystone;</li>
	<li>GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' IDENTIFIED BY 'tan124';</li>
	<li>GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' IDENTIFIED BY 'tan124';</li>
	<li>FLUSH PRIVILEGES;</li>
</ul>
<ul>Cài đặt
	<li>Không cho keystone khởi động sau khi cài: "echo "manual" > /etc/init/keystone.override"</li>
	<li>Cài đặt các gói cần thiết:<i>apt-get -y install keystone apache2 libapache2-mod-wsgi memcached python-memcache</i></li>
</ul>
<ul>Cấu hình:
	<li>Backup lại file cấu hình memcache: <i>cp /etc/memcached.conf /etc/memcached.conf.bk</i></li>
	<li>Sửa lại dòng sau trong file /etc/memcached.conf: <i>-l 127.0.0.1 --> -l 10.10.10.80</i></li>
	<li>Backup lại file cấu hình keystone: <i>cp /etc/keystone/keystone.conf /etc/keystone/keystone.conf.bk</i></li>
	<li>Sửa lại file /etc/keystone/keystone.conf:
		<ul>
			<li>Trong thẻ [DEFAULT]
				<p>
				admin_token = tan124				
				</p>
			</li>
			<li>Trong thẻ [database], tìm và sửa:
				<p>
				connection mysql+pymysql://keystone:tan124@10.10.10.80/keystone
				</p>
			</li>
			<li>Trong thẻ [token]
				<p>
				provider = fernet
				</p>
			</li>
		</ul>
	</li>
	<li>Thực hiện dump DB keystone vào MySQL: <i>su -s /bin/sh -c "keystone-manage db_sync" keystone</i></li>
	<li>Tạo token: <i>keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone</i></li>
	<li>Mở file <i>/etc/apache2/apache2.conf</i> và thêm dòng sau vào cuối file: <i>ServerName 10.10.10.80</i></li>
	<li>Tạo file cấu hình apache cho keystone:
		<ul>
			<li>vi /etc/apache2/sites-available/wsgi-keystone.conf</li>
```
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
		</ul>
	</li>
	<li>Tạo liên kết cho apache load file config vừa tạo: <i>ln -s /etc/apache2/sites-available/wsgi-keystone.conf /etc/apache2/sites-enabled</i></li>
	<li>Khởi động lại apache2 <i>service apache2 restart</i></li>
	<li>Xóa file DB không cần thiết của keystone: <i>rm -f /var/lib/keystone/keystone.db</i></li>
	<li>Tạo 03 biến môi trường để gen endpoint cho keystone:
		<ul>
			<p>
			export OS_TOKEN=tan124
			export OS_URL=http://10.10.10.80:35357/v3
			export OS_IDENTITY_API_VERSION=3
			</p>
		</ul>
	</li>
	<li>Tạo các endpoint, project, user, group cho OpenStack
		<ul>
			<li>openstack service create --name keystone --description "OpenStack Identity" identity</li>
			<li>openstack endpoint create --region RegionOne identity public http://10.10.10.80:5000/v3</li>
			<li>openstack endpoint create --region RegionOne identity internal http://10.10.10.80:5000/v3</li>
			<li>openstack endpoint create --region RegionOne identity admin http://10.10.10.80:35357/v3</li>
			<li>openstack domain create --description "Default Domain" default</li>
			<li>openstack project create --domain default --description "Admin Project" admin</li>
			<li>openstack user create admin --domain default --password tan124</li>
			<li>openstack role create admin</li>
			<li>openstack role add --project admin --user admin admin</li>
			<li>openstack project create --domain default --description "Service Project" service</li>
			<li>openstack project create --domain default --description "Demo Project" demo</li>
			<li>openstack user create demo --domain default --password tan124</li>
			<li>openstack role create user</li>
			<li>openstack role add --project demo --user demo user</li>
		</ul>
	</li>
	<li>Xóa 02 biến môi trường: <i>unset OS_TOKEN OS_URL</i></li>
	<li>Tạo file biến môi trường cho user admin: <i>vi admin-openrc</i>
	<p>
export OS_PROJECT_DOMAIN_NAME=default
export OS_USER_DOMAIN_NAME=default
export OS_PROJECT_NAME=admin
export OS_USERNAME=admin
export OS_PASSWORD=tan124
export OS_AUTH_URL=http://10.10.10.80:35357/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
	</p>
	</li>
	<li>thực thi file biến môi trường vừa tạo: <i>source admin-openrc</i></li>
	<li>Tạo file biến môi trường cho user demo: <i>vi demo-openrc</i>
	<p>
export OS_PROJECT_DOMAIN_NAME=default
export OS_USER_DOMAIN_NAME=default
export OS_PROJECT_NAME=demo
export OS_USERNAME=demo
export OS_PASSWORD=tan124
export OS_AUTH_URL=http://10.10.10.80:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
	</p>
	</li>
	<li>Check lại keystone sau khi cài đặt: <i>openstack token issue</i></li>
</ul>

<h1>4. Cài đặt Glance</h1>
<ul>Khởi tạo DB cho glance
	<li>CREATE DATABASE glance;</li>
	<li>GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' IDENTIFIED BY 'tan124';</li>
	<li>GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' IDENTIFIED BY 'tan124';</li>
	<li>FLUSH PRIVILEGES;</li>
</ul>
<ul>Tạo user và endpoint cho Glance
	<li>Export biến môi trường: <i>source admin-openrc</i></li>
	<li>Tạo user và endpoint:
		<ul>
			<li>openstack user create glance --domain default --password tan124</li>
			<li>openstack role add --project service --user glance admin</li>
			<li>openstack service create --name glance --description "OpenStack Image service" image</li>
			<li>openstack endpoint create --region RegionOne image public http://10.10.10.80:9292</li>
			<li>openstack endpoint create --region RegionOne image internal http://10.10.10.80:9292</li>
			<li>openstack endpoint create --region RegionOne image admin http://10.10.10.80:9292</li>
		</ul>
	</li>
</ul>
<ul>Cài đặt package Glance <i>apt-get -y install glance</i></ul>
<ul>Cấu hình GLANCE API
	<li>Backup lại file config: <i>cp /etc/glance/glance-api.conf /etc/glance/glance-api.conf.bk</i></li>
	<li>Sửa file cấu hình <i>vi /etc/glance/glance-api.conf</i>
		<ul>
			<li>Sửa thẻ [database], xóa dòng <i>sqlite_db</i> và thêm dòng: 
				<p>
					connection  mysql+pymysql://glance:tan124@10.10.10.80/glance
				</p>
			</li>
			<li></li>
			<li></li>
			<li></li>
			<li></li>
			<li></li>
		</ul>
	</li>
	<li></li>
	<li></li>
	<li></li>
</ul>


<h1></h1>
<ul></ul>
<ul></ul>
<ul></ul>

#II. Cài đặt Compute

<h1>Tham khảo</h1>
<ul></ul>
<ul></ul>
<ul></ul>
<ul></ul>


