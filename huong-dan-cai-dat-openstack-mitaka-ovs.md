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
		10.10.10.80		controller
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
connection = mysql+pymysql://glance:tan124@10.10.10.80/glance
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

##5. Cài đặt Nova
- Tạo DB cho Nova
```sh
CREATE DATABASE nova_api;
CREATE DATABASE nova;
GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'localhost' IDENTIFIED BY 'tan124';
GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'%' IDENTIFIED BY 'tan124';
GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'localhost' IDENTIFIED BY 'tan124';
GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%' IDENTIFIED BY 'tan124';
FLUSH PRIVILEGES;
```

- Tạo user và end point cho Nova
```sh
openstack user create nova --domain default  --password tan124

openstack role add --project service --user nova admin

openstack service create --name nova --description "OpenStack Compute" compute

openstack endpoint create --region RegionOne \
    compute public http://10.10.10.80:8774/v2.1/%\(tenant_id\)s

openstack endpoint create --region RegionOne \
    compute internal http://10.10.10.80:8774/v2.1/%\(tenant_id\)s

openstack endpoint create --region RegionOne \
    compute admin http://10.10.10.80:8774/v2.1/%\(tenant_id\)s
```

- Cài đặt Nova
```sh
apt-get -y install nova-api nova-cert \
    nova-conductor nova-consoleauth \
    nova-novncproxy nova-scheduler
```

- Backup file cấu hình
```sh
cp /etc/nova/nova.conf /etc/nova/nova.conf.bk
```

- Chỉnh sửa file cấu hình 
```sh
vi /etc/nova/nova.conf
```
- Chỉnh sửa [DEFAULT], thêm các dòng sau:
```sh
log-dir = /var/log/nova
enabled_apis = osapi_compute,metadata
rpc_backend = rabbit
auth_strategy = keystone
rootwrap_config = /etc/nova/rootwrap.conf
my_ip = 10.10.10.80
use_neutron = True
firewall_driver = nova.virt.firewall.NoopFirewallDriver
```
- Trong [DEFAULT] xóa 2 dòng cấu hình sau: 
```sh
logdir
verbose
```

- Thêm thẻ [api_database]
```sh
connection = mysql+pymysql://nova:tan124@10.10.10.80/nova_api
```

- Thêm thẻ [database]
```sh
connection = mysql+pymysql://nova:tan124@10.10.10.80/nova
```

- Thêm thẻ [oslo_messaging_rabbit]
```sh
rabbit_host = 10.10.10.80
rabbit_userid = openstack
rabbit_password = tan124
```

- Thêm thẻ [keystone_authtoken]
```sh
auth_uri = http://10.10.10.80:5000
auth_url = http://10.10.10.80:35357
memcached_servers = 10.10.10.80:11211
auth_type = password
project_domain_name = default
user_domain_name = default
project_name = service
username = nova
password = tan124
```

- Thêm thẻ [vnc]
```sh
vncserver_listen = $my_ip
vncserver_proxyclient_address = $my_ip
```

- Thêm thẻ [glance]
```sh
api_servers = http://10.10.10.80:9292
```

- Thêm thẻ [oslo_concurrency]
```sh
lock_path = /var/lib/nova/tmp
```

- Thêm thẻ [neutron]
```sh
url = http://10.10.10.80:9696
auth_url = http://10.10.10.80:35357
auth_type = password
project_domain_name = default
user_domain_name = default
region_name = RegionOne
project_name = service
username = neutron
password = tan124
service_metadata_proxy = True
metadata_proxy_shared_secret = tan124
```

- Thêm thẻ [cinder]
```sh
os_region_name = RegionOne
```

- Xóa DB defaul của nova
```sh
rm /var/lib/nova/nova.sqlite
```

- Đồng bộ DB Nova, lênh này sẽ tạo bảng cho DB nova
```sh
su -s /bin/sh -c "nova-manage api_db sync" nova
su -s /bin/sh -c "nova-manage db sync" nova
```

- Khởi động lại Nova
```sh
service nova-api restart
service nova-cert restart
service nova-consoleauth restart
service nova-scheduler restart
service nova-conductor restart
service nova-novncproxy restart
```

- Kiểm tra dịch vụ Nova
```sh
openstack compute service list
```

##6. Cài đặt Neutron
###a. Cài đặt Open vSwitch theo provider
- Cấu hình network forward cho VMs
```sh
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
echo "net.ipv4.conf.all.rp_filter=0" >> /etc/sysctl.conf
echo "net.ipv4.conf.default.rp_filter=0" >> /etc/sysctl.conf
sysctl -p
```

- Tạo DB cho Neutron
```sh
CREATE DATABASE neutron;
GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'localhost' IDENTIFIED BY 'tan124';
GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'%' IDENTIFIED BY 'tan124';
FLUSH PRIVILEGES;
```

- Tạo user và endpoint cho Neutron
```sh
openstack user create neutron --domain default --password tan124

openstack role add --project service --user neutron admin

openstack service create --name neutron \
    --description "OpenStack Networking" network

openstack endpoint create --region RegionOne \
    network public http://10.10.10.80:9696

openstack endpoint create --region RegionOne \
    network internal http://10.10.10.80:9696

openstack endpoint create --region RegionOne \
    network admin http://10.10.10.80:9696
```

- Cài đặt Neutron sử dụng Open vSwitch
```sh
apt-get -y install neutron-server neutron-plugin-ml2 \
    neutron-plugin-openvswitch-agent neutron-dhcp-agent \
    neutron-metadata-agent python-neutronclient ipset
```

- Backup lại file cấu hình neutron.conf
```sh
cp /etc/neutron/neutron.conf /etc/neutron/neutron.conf.bk
```
- Chỉnh sửa file */etc/neutron/neutron.conf*
- Tại thẻ [DEFAULT]
```sh
core_plugin = ml2
service_plugins = router
allow_overlapping_ips = True
auth_strategy = keystone
rpc_backend = rabbit
notify_nova_on_port_status_changes = True
notify_nova_on_port_data_changes = True
```

- Tại thẻ [database], chỉnh sửa:
```sh
connection = mysql+pymysql://neutron:tan124@10.10.10.80/neutron
```

- Tại thẻ [keystone_authtoken]
```sh
auth_uri = http://10.10.10.80:5000
auth_url = http://10.10.10.80:35357
memcached_servers = 10.10.10.80:11211
auth_type = password
project_domain_name = default
user_domain_name = default
project_name = service
username = neutron
password = tan124
```

- Tại thẻ [oslo_messaging_rabbit]
```sh
rabbit_host = 10.10.10.80
rabbit_userid = openstack
rabbit_password = tan124
```

- Tại thẻ [nova]
```sh
auth_url = http://10.10.10.80:35357
auth_type = password
project_domain_name = default
user_domain_name = default
region_name = RegionOne
project_name = service
username = nova
password = tan124
```

- Backup lại file cấu hình ml2
```sh
cp /etc/neutron/plugins/ml2/ml2_conf.ini /etc/neutron/plugins/ml2/ml2_conf.ini.bk
```

- Chỉnh sửa file */etc/neutron/plugins/ml2/ml2_conf.ini*
- Tại thẻ [ml2]
```sh
type_drivers = flat,vlan,vxlan,gre
tenant_network_types = vlan
mechanism_drivers = openvswitch
extension_drivers = port_security
```

- Tại thẻ [ml2_type_flat]
```sh
flat_networks = external
```

- Tại thẻ [ml2_type_vlan]
```sh
network_vlan_ranges = external
```

- Tại thẻ [securitygroup]
```sh
enable_ipset = True
firewall_driver = neutron.agent.linux.iptables_firewall.OVSHybridIptablesFirewallDriver
enable_security_group = True
```

- Backup lại file cấu hình openvswitch_agent
```sh
cp /etc/neutron/plugins/ml2/openvswitch_agent.ini /etc/neutron/plugins/ml2/openvswitch_agent.ini.bk
```

- Tại thẻ [ovs]
```sh
bridge_mappings = external:br-ex
```

- Backup cấu hình DHCP AGENT
```sh
cp /etc/neutron/dhcp_agent.ini /etc/neutron/dhcp_agent.ini.bk
```

- Tại thẻ [DEFAULT]
```sh
interface_driver = neutron.agent.linux.interface.OVSInterfaceDriver
dhcp_driver = neutron.agent.linux.dhcp.Dnsmasq
enable_isolated_metadata = True
```

- Backup cấu hình METADATA AGENT
```sh
cp /etc/neutron/metadata_agent.ini /etc/neutron/metadata_agent.ini.bk
```

- Tại thẻ [DEFAULT]
```sh
nova_metadata_ip = 10.10.10.80
metadata_proxy_shared_secret = tan124
```

- Dump DB cho Neutron
```sh
su -s /bin/sh -c "neutron-db-manage --config-file /etc/neutron/neutron.conf \
--config-file /etc/neutron/plugins/ml2/ml2_conf.ini upgrade head" neutron
```

- Khởi động lại dịch vụ Nova
```sh
service nova-api restart
service nova-scheduler restart
service nova-conductor restart
```

- Khởi động lại dịch vụ Neutron
```sh
service neutron-server restart
service neutron-openvswitch-agent restart
service neutron-dhcp-agent restart
service neutron-metadata-agent restart
```

- Xóa DB mặc định
```sh
rm -f /var/lib/neutron/neutron.sqlite
```

- Kiểm tra dịch vụ Neutron
```sh
neutron agent-list
```

- Cấu hình IP cho các switch OVS br-ex
- Backup lại cấu hình network
```sh
cp /etc/network/interfaces /etc/network/interfaces.bk
```

- Chỉnh sửa lại file */etc/network/interfaces* có định dạng như sau
```sh
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto br-ex
iface br-ex inet static
address 172.16.69.80
netmask 255.255.255.0
gateway 172.16.69.1
dns-nameservers 8.8.8.8

auto eth1
iface eth1 inet manual
   up ifconfig $IFACE 0.0.0.0 up
   up ip link set $IFACE promisc on
   down ip link set $IFACE promisc off
   down ifconfig $IFACE down

auto eth0
iface eth0 inet static
address 10.10.10.80
netmask 255.255.255.0
```

- Add br-ex
```sh
ovs-vsctl add-br br-ex
ovs-vsctl add-port br-ex eth1
```

- Restart network
```sh
ifdown -a && ifup -a
route add default gw 172.16.69.1 br-ex
```

- Add thêm DNS
```sh
nameserver 8.8.8.8 8.8.4.4
```

#II. Cài đặt Compute

##Tham khảo



