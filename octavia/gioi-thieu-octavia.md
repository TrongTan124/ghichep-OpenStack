﻿## Tổng quan

### Giới thiệu về Octavia

Octavia là một mã nguồn mở, giải pháp cân bằng tải được thiết kế để làm việc với OpenStack. Octavia được đưa ra khỏi dự án Neutron LBaaS. 
Sự hình thành của nó ảnh hưởng đến sự chuyển đổi của dự án Neutron LBaaS, khi Neutron LBaaS chuyển từ phiên bản 1 sang phiên bản 2. Bắt đầu với việc phát hành 
OpenStack bản Liberty, Octavia đã trở thành ứng dụng tham chiếu cho phiên bản Neutron LBaaS.

Octavia thực hiện việc cung cấp dịch vụ cân bằng tải bằng cách quản lý các VM, container, hoặc BM - gọi chung là amphorae - tùy yêu cầu. Tính năng mở rộng theo yêu cầu và 
theo chiều ngang này giúp phân biệt Octavia với các giải pháp cân bằng tải khác, làm cho Octavia phù hợp với "Cloud".

### Trường hợp Octavia phù hợp với hệ sinh thái OpenStack

Cân bằng tải cần thiết để kích hoạt khả năng mở rộng và khả năng phân phối đơn giản, tự động. Đổi lại, quy mô phân phối và cung cấp ứng dụng phải được coi là các 
tính năng quan trọng của bất kỳ cloud nào.

Do đó, chúng tôi coi Octavia là thiết yếu như Nova, Neutron, Glance hay bất kỳ một dự án "cốt lõi" nào khác cho phép các tính năng thiết yếu của một OpenStack cloud.

Để hoàn thành vai trò của mình, Octavia sử dụng các dự án OpenStack khác:

- **Nova**: Quản lý vòng đời của các amphora và quay vòng tính toán các nguồn lực theo yêu cầu
- **Neutron**: Cung cấp kết nối mạng giữa amphora, môi trường tenant và mạng bên ngoài.
- **Barbican**: Quản lý chứng chỉ và thông tin TLS, khi TLS được cấu hình trên amphorae
- **Keystone**: Để xác thực đối tới octavia API và để Octavia xác thực với các dự án OpenStack khác.
- **Glance**: Để lưu trữ image amphora
- **Oslo**: Để truyền thông giữa các bộ phận điều khoeenr Octavia, làm cho Octavia hoạt động trong framework OpenStack tiêu chuẩn và hệ thống đánh giá, cấu trúc mã dự án
- **Taskflow**: Về mặt kỹ thuật là một phần của Oslo, tuy nhiên, Octavia sử dụng rộng dãi hệ thống taskflow khi điều chỉnh cấu hình và quản lý dịch vụ back-end.

Octavia được thiết kế để tương tác với các thành phần được liệt kê ở trên. Trong mỗi trường hợp, chúng tôi đã quan tâm để xác định tương tác này thông qua giao diện điều khiển. 
Bằng cách đó, các thành phần bên ngoài có thể được thay thế bằng các bộ phận thay thế có chứ năng tương đương mà không cần phải cơ cấu lại các thành phần chính của Octavia. 
Ví dụ, nếu bạn sử dụng một giải pháp SDN khác Neutron trong môi trường của bạn, bạn nên viết một trình điều khiển network Octavia trong môi trường SDN của bạn. 
Một sự thay thế cho trình điều khiển của Neutron tiêu chuẩn trong Octavia.

Điều quan trọng là bạn phải biết rằng, Octavia không nhất thiết được thiết kế như là một sự thay thế hoàn toàn cho dự án Neutron LBaaS. Có nghĩa là, Octavia được thiết kế 
để "cắm vào" với Neutron LBaaS theo cách tương tự như với bất kỳ giải pháp của nhà cung cấp nào: thông qua giao diện điều khiển Neutron LBaaS v2. Bạn có thể nghĩ Octavia là "nhà 
cung cấp mã nguồn mở" cho Neutron LBaaS chứ không phải là một sự thay thế cho Neutron LBaaS. Vì lý do này, chúng tôi khuyên các khách hàng nên cấu hình dịch vụ cân bằng tải 
với Octavia thông qua CLI và API của Neutron LBaaS.

Thời gian tới, Octavia sẽ thay thế Neutron LBaaS như một giải pháp cân bằng tải cho OpenStack. Vào thời điểm đó, các trình điều khiển của nhà cung cấp thứ 3 mà hiện tại 
"cắm vào" Neutron LBaaS sẽ thay thế vào Octavia. Đối với người dùng cuối, quá trình chuyển đổi này tương đối liền mạch vì Octavia hỗ trợ Neutron LBaaS v2 API và nó có một 
giao diện CLI tương tự.

### Ký hiệu Octavia

Trước khi tiếp tục bài giới thiệu, vui lòng lưu ý sau:

Kinh nghiệm cho thấy rằng, trong ngành CNTT tạo ra, triển khai và sử dụng thiết bị cân bằng tải hoặc dịch vụ - thuật ngữ được sử dụng không nhất quán. 
Để giảm bớt sự nhầm lẫn, nhóm Octavia đã tạo một bảng thuật ngữ khi chúng được định nghĩa và sử dụng trong khuôn khổ dự án Octavia và Neutron LBaaS v2. Bản chú thích trong 
[link](https://docs.openstack.org/octavia/pike/reference/glossary.html)

Nếu bạn đã quen thuộc với các điều khoản và cách sử dụng Neutron LBaaS v1, đặc biệt, quan trọng bạn phải hiểu ý nghĩa của thuật ngữ "VIP", “load balancer,” and “load balancing,” 
đã được thay đổi trọng Neutron LBaaS v2.

Việc sử dụng các thuật ngữ này phải phù hợp với bảng thuật ngữ Octavia trong các tài liệu của Octavia, trong các cuộc thảo luận do các thành viên của Octavia tổ chức 
trên các email cộng đồng, kênh IRC, và các hội nghị. Để tránh hiểu nhầm, bạn nên tự làm quen với những định nghĩa thuật ngữ.

### Tổng quan về các thành phần của Octavia

![octavia1](../Images/octavia1.png)

Octavia v0.9 bao gồm các thành phần chính sau:

- **amphorae**: Amphorae là những máy ảo, container, hoặc BM server để hoàn thành việc cung cấp các dịch vụ cân bằng tải cho các môi trường ứng dụng. 
Trong Octavia v0.8 Việc tham chiếu amphorae là một máy ảo Ubuntu chạy HAProxy.
- **controller**: Controller là "bộ não" của Octavia. Nó bao gồm 4 thành phần nhỏ, đó là các deamon riêng biệt. Chúng có thể được chạy trên các hạ tầng back-end riêng biệt nếu muốn.
    - **API Controller**: Như tên gọi, phần này sử dụng API của Octavia. Cần phải có các yêu cầu API, thực hiện việc xử lý đơn giản và chuyển yêu cầu tới Controller Worker thông qua Oslo.
    - **Controller Worker**: Thành phần này sẽ nhận các lệnh API đã được xử lý từ API controller và thực hiện các hành động cần thiết để hoàn thành yêu cầu API.
    - **Health Manager**: Phần này theo dõi từng amphorae để đảm bảo chúng hoạt động và tốt. Nó xử lý sự cố chuyển đổi dự phòng nếu amphorae bị lỗi bất ngờ.3
    - **Housekeeping Manager**: Phần này sẽ dọn dẹp các bản ghi cơ sở dữ liệu lỗi (xóa), quản lý amphora và quản lý vòng quay chứng chỉ amphora.
- **network**: Octavia không thể thực hiện được nó mà không cần thao tác môi trường mạng. Amphorae được kết nối một giao diện mạng trên "load balancer network", và chúng cũng 
có thể cắm trực tiếp vào các tenant network của các pool member, tùy thuộc vào cách dịch vụ cân bằng tải được triển khai bởi người sử dụng.

## Cài đặt

### Devstack

Thực hiện cài đặt bằng devstack rất đơn giản.

**Bước 1**:

Chuẩn bị một máy chủ cài có cấu hình 6 vCPU, 8GB RAM, 60GB HDD, 02 interface (public + private). Cài đặt hệ điều hành Ubuntu 16.04 64bits.

Máy có kết nối internet, và đã cài đặt sẵn các gói `wget, git`
```sh
apt-get update -y && apt-get dist-upgrade -y && apt-get install wget git -y
```

**Bước 2**:

Đăng nhập vào với quyền `root` và tải script sau về:

- Nếu sử dụng OpenStack ocata
```sh
wget https://raw.githubusercontent.com/openstack/octavia/stable/ocata/devstack/contrib/new-octavia-devstack.sh
```

- Nếu sử dụng OpenStack pike:
```sh
wget https://raw.githubusercontent.com/openstack/octavia/stable/pike/devstack/contrib/new-octavia-devstack.sh
```

**Bước 3**:

Cho script quyền thực thi
```sh
chmod +x new-octavia-devstack.sh
```

Chạy lệnh sau để bắt đầu cài đặt
```sh
./new-octavia-devstack.sh
```

Quá trình cài đặt mất khoảng 1h30p. Vì cài đặt từ source nên sẽ bao gồm cả bước biên dịch và cài đặt.

**Note**: Trong quá trình cài đặt devstack Octavia trê bản OpenStack ocata sẽ bị lỗi và ko thể tiếp tục được nữa. Nguyên nhân chưa rõ.

**Bước 4**:

Việc cài đặt vẫn thiếu project horizon, nên bạn cần cài đặt thêm gói horizon nếu muốn thao tác và xem qua web.

Từ user `stack`, ta chạy lệnh sau:
```sh
sudo apt-get install openstack-dashboard -y
```

Cài đặt xong, ta chay đổi cấu hình của file `/etc/openstack-dashboard/local_settings.py` như sau:
```sh
OPENSTACK_API_VERSIONS = {
    "identity": 3,
    "image": 2,
    "volume": 2,
}

OPENSTACK_KEYSTONE_DEFAULT_DOMAIN = "Default"

OPENSTACK_HOST = "172.16.68.59"

OPENSTACK_KEYSTONE_URL = "http://%s/identity" % OPENSTACK_HOST

OPENSTACK_KEYSTONE_DEFAULT_ROLE = "user"

SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
OPENSTACK_KEYSTONE_MULTIDOMAIN_SUPPORT = True

TIME_ZONE = "Asia/Ho_Chi_Minh"
```

Thực hiện khởi động lại `apache2` để bắt đầu sử dụng `horizon`
```sh
sudo service apache2 reload
```

### Package

Cài đặt OpenStack theo hướng dẫn [sau]()

**Cách 1**:

Cài đặt Neutron LBaaS v2 bằng lệnh:
```sh
apt-get install -y neutron-lbaasv2-agent
```

Chỉnh sửa lại các tập tin cấu hình của Neutron liên quan tới LBaaS. 

Trước tiên chỉnh sửa file `/etc/default/neutron-server`, thêm nội dung sau vào cuối file
```sh
DAEMON_ARGS="$DAEMON_ARGS --config-file=/etc/neutron/neutron_lbaas.conf --config-file=/etc/neutron/services_lbaas.conf"
```

Tiếp theo, mở file `/etc/neutron/neutron.conf` và thêm vào plugin lbaas vào dòng có `service_plugins`
```sh
service_plugins = router,neutron_lbaas.services.loadbalancer.plugin.LoadBalancerPluginv2
```

Tiếp tục chỉnh sửa file `/etc/neutron/neutron_lbaas.conf` với các nội dung tương ứng sau
```sh
[service_providers]
service_provider = service_provider = LOADBALANCERV2:Octavia:neutron_lbaas.drivers.octavia.driver.OctaviaDriver:default


[service_auth]
auth_url = http://controller:35357/v3
admin_user = octavia
admin_tenant_name = admin
admin_password = tan124
admin_user_domain = default
admin_project_domain = default
region = RegionOne
auth_version = 3
```

Chỉnh sửa file `/etc/neutron/services_lbaas.conf` với các nội dung sau:
```sh
[octavia]
base_url = http://controller:9876/
```

Cập nhật lại DB của Neutron bằng lệnh:
```sh
neutron-db-manage --config-file /etc/neutron/neutron.conf --config-file /etc/neutron/plugins/ml2/ml2_conf.ini upgrade head
```

Cần thực hiện cài đặt octavia. Đầu tiên ta khởi tạo database trên node controller
```sh
mysql> CREATE DATABASE octavia;
mysql> GRANT ALL PRIVILEGES ON octavia.* TO 'octavia'@'localhost' IDENTIFIED BY 'tan124';
mysql> GRANT ALL PRIVILEGES ON octavia.* TO 'octavia'@'%' IDENTIFIED BY 'tan124';
mysql> flush privileges;
```

Tiếp theo ta khởi tạo endpoint cho octavia
```sh
openstack user create --domain default --password tan124 octavia
openstack role add --project service --user octavia admin
openstack service create --name octavia --description "OpenStack Load Balancer" load-balancer
openstack endpoint create load-balancer public http://controller:9876/ --region RegionOne 
openstack endpoint create load-balancer admin http://controller:9876/ --region RegionOne 
openstack endpoint create load-balancer internal http://controller:9876/ --region RegionOne
```

Cài đặt từ pip, vì octavia chưa được đóng gói thành package. 

```sh
apt-get install -y python-pip

pip install -y octavia 
```

Tạo một flavor để cho amphora sử dụng khi khởi tạo bằng lệnh.
```sh
root@controller:/usr/local# nova flavor-create --is-public False m1.amphora auto 1024 2 1
+--------------------------------------+------------+-----------+------+-----------+------+-------+-------------+-----------+
| ID                                   | Name       | Memory_MB | Disk | Ephemeral | Swap | VCPUs | RXTX_Factor | Is_Public |
+--------------------------------------+------------+-----------+------+-----------+------+-------+-------------+-----------+
| 55afa5a1-1b67-43b0-a3cc-f4420e4526de | m1.amphora | 1024      | 2    | 0         |      | 1     | 1.0         | False     |
+--------------------------------------+------------+-----------+------+-----------+------+-------+-------------+-----------+
```

Ta sử dụng IP của flavor vừa tạo để đưa vào config trong octavia. tại file `/usr/local/etc/octavia/octavia.conf`
```sh
[controller_worker]
amp_flavor_id = 55afa5a1-1b67-43b0-a3cc-f4420e4526de
```

Để có thể ssh vào amphora ta cần sử dụng keypair. ta tạo một cặp key bằng lệnh
```sh
mkdir -p /usr/local/etc/octavia/.ssh && cd /usr/local/etc/octavia/.ssh
ssh-keygen -b 2048 -t rsa -N "" -f octavia
```

Sau đó import cặp key vào trong nova 
```sh
nova keypair-add --pub-key octavia.pub octavia
```

Thực hiện chỉnh sửa cấu hình file `/usr/local/etc/octavia/octavia.conf` với keypair vừa tạo trên
```sh
[controller_worker]
amp_ssh_key_name = octavia
```

Giữa amphora API và Octavia controller tương tác 2 chiều với certificate xác thực. Ta tạo cặp SSL certificate bằng script `create_certificates.sh` như sau 
```sh
#!/bin/bash

# USAGE: <certificate directory> <openssl.cnf (example in etc/certificate)
#Those are certificates for testing will be generated
#
#* ca_01.pem is a certificate authority file
#* server.pem combines a key and a cert from this certificate authority
#* client.key the client key
#* client.pem the client certificate
#
#You will need to copy them to places the agent_api server/client can find and
#specify it in the config.
#
#Example for client use:
#
#curl -k -v --key client.key --cacert ca_01.pem --cert client.pem https://0.0.0.0:9443/
#
#
#Notes:
#For production use the ca issuing the client certificate and the ca issuing the server cetrificate
#need to be different so a hacker can't just use the server certificate from a compromised amphora
#to control all the others.
#
#Sources:
#* https://communities.bmc.com/community/bmcdn/bmc_atrium_and_foundation_technologies/
#discovery/blog/2014/09/03/the-pulse-create-your-own-personal-ca-with-openssl
#  This describes how to create a CA and sign requests
#* https://www.digitalocean.com/community/tutorials/
#openssl-essentials-working-with-ssl-certificates-private-keys-and-csrs -
#how to issue csr and much more

## Create CA

# Create directories
CERT_DIR=$1
OPEN_SSL_CONF=$2 # etc/certificates/openssl.cnf
VALIDITY_DAYS=${3:-18250} # defaults to 50 years

echo $CERT_DIR

mkdir -p $CERT_DIR
cd $CERT_DIR
mkdir newcerts private
chmod 700 private

# prepare files
touch index.txt
echo 01 > serial


echo "Create the CA's private and public keypair (2k long)"
openssl genrsa -passout pass:foobar -des3 -out private/cakey.pem 2048

echo "You will be asked to enter some information about the certificate."
openssl req -x509 -passin pass:foobar -new -nodes -key private/cakey.pem \
        -config $OPEN_SSL_CONF \
        -subj "/C=US/ST=Denial/L=Springfield/O=Dis/CN=www.example.com" \
        -days $VALIDITY_DAYS \
        -out ca_01.pem

echo "Here is the certificate"
openssl x509 -in ca_01.pem -text -noout

## Create Server/Client CSR
echo "Generate a server key and a CSR"
openssl req \
       -newkey rsa:2048 -nodes -keyout client.key \
       -subj "/C=US/ST=Denial/L=Springfield/O=Dis/CN=www.example.com" \
       -out client.csr

echo "Sign request"
openssl ca -passin pass:foobar -config $OPEN_SSL_CONF -in client.csr \
           -days $VALIDITY_DAYS -out client-.pem -batch

echo "Generate single pem client.pem"
cat client-.pem client.key > client.pem

echo "Note: For production use the ca issuing the client certificate and the ca issuing the server"
echo "certificate need to be different so a hacker can't just use the server certificate from a"
echo "compromised amphora to control all the others."
echo "\nTo use the certificates copy them to the directory specified in the octavia.conf"
```

Cho script quyền thực thi
```sh
chmod +x create_certificates.sh
```

Chỉnh sửa lại file `/etc/ssl/openssl.cnf` một số nội dung mặc định thành như sau
```sh
[ CA_default ]
dir                = ./
certificate        = $dir/ca_01.pem
```

Tạo thư mục chứa các cert
```sh
mkdir -p /usr/local/etc/octavia/certs
```

Sử dụng lệnh sau để tạo các cert:
```sh
source /root/create_certificates.sh /usr/local/etc/octavia/certs /etc/ssl/openssl.cnf
```

Sử dụng các cert vừa tạo để cho vào file `/usr/local/etc/octavia/octavia.conf`
```sh
[certificates]
ca_certificate = /usr/local/etc/octavia/certs/ca_01.pem
ca_private_key = /usr/local/etc/octavia/certs/private/cakey.pem
ca_private_key_passphrase = foobar
[haproxy_amphora]
client_cert = /usr/local/etc/octavia/certs/client.pem
server_ca = /usr/local/etc/octavia/certs/ca_01.pem
```

Tạo một Loadbalancer management network để cho controller giao tiếp với amphorae và ngược lại. 
Tất cả các amphorae mà Octavia khởi tạo sẽ đều có interface và địa chỉ IP của network này.
```sh
root@controller:/usr/local/etc/octavia/certs# neutron net-create lb-mgmt-net
neutron CLI is deprecated and will be removed in the future. Use openstack CLI instead.
Created a new network:
+---------------------------+--------------------------------------+
| Field                     | Value                                |
+---------------------------+--------------------------------------+
| admin_state_up            | True                                 |
| availability_zone_hints   |                                      |
| availability_zones        |                                      |
| created_at                | 2017-12-05T08:51:38Z                 |
| description               |                                      |
| id                        | 9599f74a-eadd-49e3-a045-6e7c0246273a |
| ipv4_address_scope        |                                      |
| ipv6_address_scope        |                                      |
| is_default                | False                                |
| mtu                       | 1450                                 |
| name                      | lb-mgmt-net                          |
| port_security_enabled     | True                                 |
| project_id                | b83b5c2875774d4aa46e8a029f7a4b0e     |
| provider:network_type     | vxlan                                |
| provider:physical_network |                                      |
| provider:segmentation_id  | 24                                   |
| revision_number           | 2                                    |
| router:external           | False                                |
| shared                    | False                                |
| status                    | ACTIVE                               |
| subnets                   |                                      |
| tags                      |                                      |
| tenant_id                 | b83b5c2875774d4aa46e8a029f7a4b0e     |
| updated_at                | 2017-12-05T08:51:38Z                 |
+---------------------------+--------------------------------------+
```
==> lưu id của network lại `9599f74a-eadd-49e3-a045-6e7c0246273a`

Tạo subnet cho network vừa tạo
```sh
openstack subnet create lb-mgmt-subnet --subnet-range 192.168.10.0/24 --dhcp --dns-nameserver 8.8.8.8 --allocation-pool start=192.168.10.10,end=192.168.10.100 --description "Subnet for amphorae" --network lb-mgmt-net
```

Tạo một port manage trên network dành cho amphorae
```sh
neutron port-create --name octavia-health-manager-listen-port --binding:host_id=controller lb-mgmt-net
```

==> lưu lại 02 thông tin về `ip`, `id` và `mac_address` của lệnh tạo port trên
```sh
| fixed_ips             | {"subnet_id": "8ec27137-010b-43be-89d5-a4ede69fba8d", "ip_address": "192.168.10.13"} |
| id                    | 6d5babc6-90d0-4e6d-b796-2e198acefdbc                                                 |
| mac_address           | fa:16:3e:05:c7:dc                                                                    |
```

Ta gắn port vừa tạo ở trên vào br-int trong OpenStack
```sh
sudo ovs-vsctl -- --may-exist add-port br-int o-hm0 -- set Interface o-hm0 type=internal -- set Interface o-hm0 external-ids:iface-status=active -- set Interface o-hm0 external-ids:attached-mac=fa:16:3e:05:c7:dc -- set Interface o-hm0 external-ids:iface-id=6d5babc6-90d0-4e6d-b796-2e198acefdbc
sudo ip link set dev o-hm0 address fa:16:3e:05:c7:dc
sudo dhclient -v o-hm0
```

Tiếp tục chỉnh sửa cấu hình trong file `/usr/local/etc/octavia/octavia.conf` như sau:
```sh
[controller_worker]
amp_network = 9599f74a-eadd-49e3-a045-6e7c0246273a
[health_manager]
controller_ip_port_list = 192.168.10.13:5555
bind_ip = 192.168.10.13
bind_port = 5555
```

Khởi tạo neutron security group mà áp dụng khi khởi tạo amphorae trong LB network. Cần cho phép tất cả các package vào ra trên amphora API. (Thông thường là port 9443 hoặc 22)
```sh
neutron security-group-create lb-mgmt-sec-grp
neutron security-group-rule-create --protocol icmp lb-mgmt-sec-grp
neutron security-group-rule-create --protocol tcp --port-range-min 22 --port-range-max 22 lb-mgmt-sec-grp
neutron security-group-rule-create --protocol tcp --port-range-min 9443 --port-range-max 9443 lb-mgmt-sec-grp
neutron security-group-list
```

==> lưu lại giá trị `id` của security group `lb-mgmt-sec-grp`
```sh
| 58a96121-e6e7-451d-8ceb-315d12fe8ba9 | lb-mgmt-sec-grp | b83b5c2875774d4aa46e8a029f7a4b0e | egress, IPv4                                                         |
|                                      |                 |                                  | egress, IPv6                                                         |
|                                      |                 |                                  | ingress, IPv4, 22/tcp                                                |
|                                      |                 |                                  | ingress, IPv4, 9443/tcp                                              |
|                                      |                 |                                  | ingress, IPv4, icmp                                                  |
```

Thêm vào trong tập tin cấu hình `/usr/local/etc/octavia/octavia.conf` dòng sau
```sh
[controller_worker]
amp_secgroup_list = 58a96121-e6e7-451d-8ceb-315d12fe8ba9
```

Khởi tạo một image Amphorae bằng script tại [đây](https://github.com/TrongTan124/octavia/blob/stable/pike/diskimage-create/diskimage-create.sh) để tạo một cách tự động. Bạn nên tải toàn bộ git octavia về để build. do script viết có dùng nhiều function hoặc lib liên quan tới nhau. Nếu không, bạn phải cài lẻ thêm một vài package trước khi chạy script (sau này nếu thiếu thì cài bổ sung, tôi sử dụng Ubuntu 16.04 x64bits)
```sh
apt-get install -y qemu curl git wget kpartx
```

Sau khi tạo xong image, ta import vào glance

Sau đó chạy lệnh cài đặt 
```sh
$OCTAVIA_DIR/diskimage-create/diskimage-create.sh -s 2
```

Nếu bạn đã có 1 máy cài octavia bằng devstack thì ta export image amphora bằng lệnh
```sh
openstack image save amphora-x64-haproxy --file /tmp/amphora-x64-haproxy.qcow2
```

Hoặc vào thư mục `/opt/stack/octavia/diskimage-create` để tải về.

Sau đó chuyển sang máy chủ controller và import bằng lệnh:
```sh
openstack image create "amphora-x64-haproxy" --file amphora-x64-haproxy.qcow2 --disk-format qcow2 --container-format bare --public
```

Gắn tag cho image amphora vừa import vào glance bằng lệnh
```sh
glance image-tag-update ec33a687-b57b-46ae-bf4e-c2801aeb6479 amphora
```

Chỉnh sửa tập tin cấu hình `/usr/local/etc/octavia/octavia.conf`
```sh
[controller_worker]
amp_image_tag = amphora
```

**Cách 2**:

Tải git về và cài từ source
```sh
wget https://pypi.python.org/packages/31/83/845e8e2930735811d19ff189bc61ae0330385b216039461725c202f4c663/octavia-1.0.0.0rc2-py2.py3-none-any.whl#md5=ad04b06d6af88ed1148ce3a081c1c2bb  
pip install octavia-1.0.0.0rc2-py2.py3-none-any.whl  
```

## Sử dụng

### Devstack

Đăng nhập vào máy chủ và chuyển sang user `stack` hoặc đăng nhập vào horizon với user/password là `admin/secretadmin`

### Package



## Tham khảo

- [https://docs.openstack.org/octavia/pike/reference/introduction.html](https://docs.openstack.org/octavia/pike/reference/introduction.html)
- [http://blog.csdn.net/zhaihaifei/article/details/77482684](http://blog.csdn.net/zhaihaifei/article/details/77482684)