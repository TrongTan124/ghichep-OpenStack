## Chuẩn bị

01 máy chủ deployment:
- CPU: 4 vCPU
- RAM: 2 GB
- HDD: 100 GB
- OS: CentOS 7.5-1804
- NIC: 03 interface
    - eth0: 192.168.20.57/24
    - eth1: 172.16.68.57/24; GW: 172.16.68.1
    - eth2: 192.168.30.57/24
    
03 máy chủ controller:
- CPU: 4 vCPU
- RAM: 4 GB
- HDD: 100 GB
- OS: CentOS 7.5-1804
- NIC: 03 interface
    - eth0: 172.16.68.[51,52,53]/24; GW: 172.16.68.1
    - eth1: 192.168.30.[51,52,53]/24
    
01 máy chủ compute
- CPU: 4 vCPU
- RAM: 4 GB
- HDD: 100 GB
- OS: CentOS 7.5-1804
- NIC: 03 interface
    - eth0: 172.16.68.54/24; GW: 172.16.68.1
    - eth1: 192.168.30.54/24


## Thiết lập máy chủ deployment

- Đặt hostname:

```sh
hostnamectl set-hostname deployserver
```

- Thiết lập IP cho máy chủ

```sh
echo "Setup IP eth0"
nmcli c modify eth0 ipv4.addresses 192.168.20.57/24
nmcli c modify eth0 ipv4.method manual
nmcli con mod eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli c modify eth1 ipv4.addresses 172.16.68.57/24
nmcli c modify eth1 ipv4.gateway 172.16.68.1
nmcli c modify eth1 ipv4.dns 8.8.8.8
nmcli c modify eth1 ipv4.method manual
nmcli con mod eth1 connection.autoconnect yes

echo "Setup IP eth2"
nmcli c modify eth2 ipv4.addresses 192.168.30.57/24
nmcli c modify eth2 ipv4.method manual
nmcli con mod eth2 connection.autoconnect yes

sudo systemctl disable firewalld
sudo systemctl stop firewalld
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
sudo systemctl enable network
sudo systemctl start network
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

- Khởi động lại máy chủ:

```sh
init 6
```

## Thiết lập máy chủ controller

### controller1

- Đặt hostname:

```sh
hostnamectl set-hostname controller1
```

- Thiết lập IP cho máy chủ

```sh
echo "Setup IP eth0"
nmcli c modify eth0 ipv4.addresses 172.16.68.51/24
nmcli c modify eth0 ipv4.gateway 172.16.68.1
nmcli c modify eth0 ipv4.dns 8.8.8.8
nmcli c modify eth0 ipv4.method manual
nmcli con mod eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli c modify eth1 ipv4.addresses 192.168.30.51/24
nmcli c modify eth1 ipv4.method manual
nmcli con mod eth1 connection.autoconnect yes

sudo systemctl disable firewalld
sudo systemctl stop firewalld
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
sudo systemctl enable network
sudo systemctl start network
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

- Khởi động lại máy chủ:

```sh
init 6
```

### controller2

- Đặt hostname:

```sh
hostnamectl set-hostname controller2
```

- Thiết lập IP cho máy chủ

```sh
echo "Setup IP eth0"
nmcli c modify eth0 ipv4.addresses 172.16.68.52/24
nmcli c modify eth0 ipv4.gateway 172.16.68.1
nmcli c modify eth0 ipv4.dns 8.8.8.8
nmcli c modify eth0 ipv4.method manual
nmcli con mod eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli c modify eth1 ipv4.addresses 192.168.30.52/24
nmcli c modify eth1 ipv4.method manual
nmcli con mod eth1 connection.autoconnect yes

sudo systemctl disable firewalld
sudo systemctl stop firewalld
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
sudo systemctl enable network
sudo systemctl start network
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

- Khởi động lại máy chủ:

```sh
init 6
```

### controller3

- Đặt hostname:

```sh
hostnamectl set-hostname controller3
```

- Thiết lập IP cho máy chủ

```sh
echo "Setup IP eth0"
nmcli c modify eth0 ipv4.addresses 172.16.68.53/24
nmcli c modify eth0 ipv4.gateway 172.16.68.1
nmcli c modify eth0 ipv4.dns 8.8.8.8
nmcli c modify eth0 ipv4.method manual
nmcli con mod eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli c modify eth1 ipv4.addresses 192.168.30.53/24
nmcli c modify eth1 ipv4.method manual
nmcli con mod eth1 connection.autoconnect yes

sudo systemctl disable firewalld
sudo systemctl stop firewalld
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
sudo systemctl enable network
sudo systemctl start network
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

- Khởi động lại máy chủ:

```sh
init 6
```

### compute1

- Đặt hostname:

```sh
hostnamectl set-hostname compute1
```

- Thiết lập IP cho máy chủ

```sh
echo "Setup IP eth0"
nmcli c modify eth0 ipv4.addresses 172.16.68.54/24
nmcli c modify eth0 ipv4.gateway 172.16.68.1
nmcli c modify eth0 ipv4.dns 8.8.8.8
nmcli c modify eth0 ipv4.method manual
nmcli con mod eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli c modify eth1 ipv4.addresses 192.168.30.54/24
nmcli c modify eth1 ipv4.method manual
nmcli con mod eth1 connection.autoconnect yes

sudo systemctl disable firewalld
sudo systemctl stop firewalld
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
sudo systemctl enable network
sudo systemctl start network
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

- Khởi động lại máy chủ:

```sh
init 6
```

## Cài đặt trên node deployment

- Cài đặt các gói phụ trợ:

```sh
yum install -y epel-release
yum update -y

yum install -y git wget ansible gcc python-devel python-pip yum-utils byobu net-tools vim
```

- Cài đặt docker:

```sh
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

yum install -y docker-ce
```

- Cấu hình cho docker

```sh
mkdir /etc/systemd/system/docker.service.d

tee /etc/systemd/system/docker.service.d/kolla.conf << 'EOF'
[Service]
MountFlags=shared
EOF
```

- Khởi động và thiết lập docker cùng khởi động với HĐH

```sh
systemctl daemon-reload
systemctl enable docker
systemctl restart docker
```

- Tạo ssh-key để sử dụng trong quá trình cài đặt, sẽ đăng nhập vào các node còn lại bằng key (ko phải nhập password), nhấn `enter` để tạo:

```sh
ssh-keygen -t rsa
```

## Cài đặt trên các node `controller`, `compute`

- Cài đặt các gói phụ trợ

```sh
yum install -y epel-release
yum update -y

yum install -y git wget ansible gcc python-devel python-pip yum-utils byobu net-tools vim
```

- Sao lưu key từ node deployment sang các node `controller` và `compute`. Cần nhập password của node deployment

```sh
cd /root

scp root@172.16.68.57:~/.ssh/id_rsa.pub ./
```

- Lưu key vào máy chủ

```sh
cat id_rsa.pub >> ~/.ssh/authorized_keys

chmod 600 ~/.ssh/authorized_keys
```

- Cài đặt docker lên các máy chủ

```sh
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

yum install -y docker-ce
```

- Cấu hình cho docker

```sh
mkdir /etc/systemd/system/docker.service.d

tee /etc/systemd/system/docker.service.d/kolla.conf << 'EOF'
[Service]
MountFlags=shared
EOF
```

- Khởi động và kích hoạt docker cùng HĐH

```sh
systemctl daemon-reload
systemctl enable docker
systemctl restart docker
```

## Thực hiện deploy OpenStack

- Đứng trên máy chủ `deployment` thực hiện các thao tác cài đặt

- Tải kolla-ansible

```sh
cd /opt

git clone https://github.com/openstack/kolla-ansible.git -b stable/rocky

cd kolla-ansible

pip install -r requirements.txt

python setup.py install

cp -r /usr/share/kolla-ansible/etc_examples/kolla /etc/kolla/

cp /usr/share/kolla-ansible/ansible/inventory/* .
```

- Chạy lệnh tự tạo mật khẩu, kết quả của lệnh sẽ lưu mật khẩu vào `/etc/kolla/passwords.yml`

```sh
kolla-genpwd
```

- Chỉnh sửa file `/etc/kolla/globals.yml` để thiết lập các thành phần trong kolla. Tôi sử dụng kolla-ansible để cài OpenStack bản Rocky. Lưu ý thay IP `172.16.68.50` thành IP máy chủ của bạn:

```sh
sed -i 's/#kolla_base_distro: "centos"/kolla_base_distro: "centos"/g' /etc/kolla/globals.yml
sed -i 's/#kolla_install_type: "binary"/kolla_install_type: "source"/g' /etc/kolla/globals.yml
sed -i 's/#openstack_release: ""/openstack_release: "rocky"/g' /etc/kolla/globals.yml
sed -i 's/kolla_internal_vip_address: "10.10.10.254"/kolla_internal_vip_address: "172.16.68.50"/g' /etc/kolla/globals.yml
sed -i 's/#docker_registry: "172.16.0.10:4000"/docker_registry: ""/g' /etc/kolla/globals.yml
sed -i 's/#docker_namespace: "companyname"/docker_namespace: "kolla"/g' /etc/kolla/globals.yml
sed -i 's/#network_interface: "eth0"/network_interface: "eth0"/g' /etc/kolla/globals.yml
sed -i 's/#neutron_external_interface: "eth1"/neutron_external_interface: "eth1"/g' /etc/kolla/globals.yml
sed -i 's/#keepalived_virtual_router_id: "51"/keepalived_virtual_router_id: "199"/g' /etc/kolla/globals.yml
sed -i 's/#enable_haproxy: "yes"/enable_haproxy: "yes"/g' /etc/kolla/globals.yml
```

- Sửa file `/opt/kolla-ansible/multinode` cho phù hợp với mô hình. Chỉ sửa tới thẻ `monitoring` như phía dưới, còn lại các thẻ cấu hình `children` giữ nguyên.

```sh
[control]
172.16.68.51
172.16.68.52
172.16.68.53
[network]
172.16.68.51
172.16.68.52
172.16.68.53
[inner-compute]
[external-compute]
172.16.68.54
[compute:children]
inner-compute
external-compute
[monitoring]
172.16.68.51
```

## Cài đặt OpenStack

- Trước tiên cần phải thêm public key của các node controller, compute vào máy chủ deployment để có thể ssh mà ko cần xác thực.

- Kiểm tra trước khi cài đặt, lệnh này sẽ thực hiện cấu hình kolla (sinh ra các tập tin cấu hình trong `/etc/kolla/`. Lệnh này cũng đồng thời truy cập vào các host controller và compute:

```sh
[root@deployserver kolla-ansible]# ll /etc/kolla/
total 48
-rw-r--r-- 1 root root 17196 Jan 24 17:21 globals.yml
-rw-r--r-- 1 root root 25370 Jan 24 17:20 passwords.yml
```

```sh
cd /opt/kolla-ansible/

kolla-ansible -i multinode bootstrap-servers
```

- Chạy tiếp lệnh sau để kiểm tra trước khi deploy

```sh
kolla-ansible prechecks -i multinode
```

- Kết quả

```sh
172.16.68.51               : ok=90   changed=0    unreachable=0    failed=0   
172.16.68.52               : ok=81   changed=0    unreachable=0    failed=0   
172.16.68.53               : ok=81   changed=0    unreachable=0    failed=0   
172.16.68.54               : ok=24   changed=0    unreachable=0    failed=0   
localhost                  : ok=10   changed=0    unreachable=0    failed=0
```

- Bắt đầu chạy lệnh cài đặt, ở đây, node `deployment` sẽ thực hiện các task cài đặt của `ansible` được viết trong các `playbook` cho đồng thời tất cả các node `controller` và `compute`

```sh
kolla-ansible deploy -i multinode
```

- Kết quả sau khi chạy lệnh `deploy` xong:

```sh

```

- Kiểm tra lại kết quả sau khi cài đặt

```sh
kolla-ansible post-deploy
```

## Thiết lập sau khi cài đặt

- Thực hiện cài đặt openstack-client để thao tác lệnh:

```sh
pip install python-openstackclient
```

- Import biến môi trường ở file `/etc/kolla/admin-openrc.sh` để tương tác với hệ thống

```sh
source /etc/kolla/admin-openrc.sh
```

- Kiểm tra hoạt động của OpenStack bằng lệnh `openstack token issue` sẽ ra kết quả như dưới:

```sh
[root@srv1kolla ~]# openstack token issue
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field      | Value                                                                                                                                                                                   |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| expires    | 2019-01-24T09:19:28+0000                                                                                                                                                                |
| id         | gAAAAABcSDGgJTKHmMT49rlTr-x1wNOdK9d2l6L1Xb4H7d_6MyI53JO_zhMzIt2xgFEcmSmJkE72wRnEuh1arf6Yk40tL0W8S4Oy7Tww-do9Gn4j57E9oHmxvjCLu9BkQG1eHZNGvMU1rKvI6qG3usn1oCndHwikrHkFyt3Hzno2lBXDRGujnkc |
| project_id | b80d189c6f0844bc938a961a4a76e340                                                                                                                                                        |
| user_id    | 1ff1fe655367422883a34832d26d4683                                                                                                                                                        |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

**NOTE**: nếu bị lỗi như sau:

```sh
[root@srv1kolla ~]# openstack token issue
Traceback (most recent call last):
  File "/usr/bin/openstack", line 7, in <module>
    from openstackclient.shell import main
  File "/usr/lib/python2.7/site-packages/openstackclient/shell.py", line 23, in <module>
    from osc_lib import shell
  File "/usr/lib/python2.7/site-packages/osc_lib/shell.py", line 33, in <module>
    from osc_lib.cli import client_config as cloud_config
  File "/usr/lib/python2.7/site-packages/osc_lib/cli/client_config.py", line 18, in <module>
    from openstack.config import exceptions as sdk_exceptions
  File "/usr/lib/python2.7/site-packages/openstack/__init__.py", line 17, in <module>
    import openstack.connection
  File "/usr/lib/python2.7/site-packages/openstack/connection.py", line 166, in <module>
    from openstack.cloud import openstackcloud as _cloud
  File "/usr/lib/python2.7/site-packages/openstack/cloud/openstackcloud.py", line 35, in <module>
    import dogpile.cache
  File "/usr/lib/python2.7/site-packages/dogpile/cache/__init__.py", line 1, in <module>
    from .region import CacheRegion, register_backend, make_region  # noqa
  File "/usr/lib/python2.7/site-packages/dogpile/cache/region.py", line 15, in <module>
    from decorator import decorate
ImportError: cannot import name decorate
```

thì fix bug bằng cách cài đặt bổ sung gói bằng lệnh:

```sh
[root@srv1kolla ~]# pip install -U decorator
```

- Chỉnh sửa file `/usr/share/kolla-ansible/init-runonce` để thiết lập external network cho VM trong hệ thống, sử dụng dải IP thuộc eth2 như đã khai báo trong cấu hình `/etc/kolla/globals.yml` trước khi cài đặt, dòng 16, 17, 18

```sh
EXT_NET_CIDR='192.168.30.0/24'
EXT_NET_RANGE='start=192.168.30.110,end=192.168.30.119'
EXT_NET_GATEWAY='192.168.30.1'
```

- Cũng bỏ tham số `--no-dhcp` `trong file /usr/share/kolla-ansible/init-runonce` ở dòng 74

```sh
openstack subnet create --no-dhcp \
    --allocation-pool ${EXT_NET_RANGE} --network public1 \
    --subnet-range ${EXT_NET_CIDR} --gateway ${EXT_NET_GATEWAY} public1-subnet
```

- Chạy script để khởi tạo network, flavor, tải image. Quá trình chạy script cần nhập passphase:

```sh
bash /usr/share/kolla-ansible/init-runonce
```

- Tạo VM bằng lệnh sau:

```sh
openstack server create \
    --image cirros \
    --flavor m1.tiny \
    --key-name mykey \
    --network demo-net \
    demo1
```

## Một số lưu ý

- Mật khẩu tài khoản admin để đăng nhập vào OpenStack trong file `/etc/kolla/admin-openrc.sh`

- Link đăng nhập horizon là IP của eth1: `http://172.16.68.57`

- Mật khẩu của các project khác trong OpenStack ở file: `/etc/kolla/passwords.yml`

- Một số container khi chạy lệnh `docker exec -it NAME_CONTAINER /bin/bash` sẽ đăng nhập vào container không phải là root, nên có một số lệnh sẽ ko sử dụng được. Thêm tham số khi chạy lệnh đăng nhập container `docker exec -it -u root NAME_CONTAINER /bin/bash` sẽ OK.

- Kolla triển khai OpenStack theo mô hình self-service nên không thể gán trực tiếp IP public cho VM, bắt buộc phải sử dụng floating. do cấu hình của openvswitch agent trên node compute không có mapping:

```sh
[ovs]
datapath_type = system
ovsdb_connection = tcp:127.0.0.1:6640
local_ip = 172.16.68.54
```

## Tham khảo

- [https://docs.openstack.org/kolla-ansible/latest/](https://docs.openstack.org/kolla-ansible/latest/)
- [https://github.com/congto/hdsd-openstack-kolla/blob/master/docs/openstack-pike-kolla-aio.md](https://github.com/congto/hdsd-openstack-kolla/blob/master/docs/openstack-pike-kolla-aio.md)