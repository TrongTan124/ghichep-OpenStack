## Tổng quan

### Giới thiệu về Barbican

Key Manager service (barbican) cung cấp nơi lưu trữ an toàn, cung cấp và quản trị dữ liệu mật. Điều này bao gồm các key như khóa đối xứng, khóa bất đối xứng, chứng chỉ và dữ liệu binary thô.

Key Manager service bao gồm các thành phần sau: `barbican-api service` chịu trách nhiệm xử lý yêu cầu của người dùng cuối thông qua API

## kiến trúc

Kiến trúc tổng quan của Barbican

![barbican-overall-architecture](../Images/barbican-overall-architecture.gif)

Tương tác giữa các thành phần trong Barbican

![barbican1](../Images/barbican1.gif)



## Cài đặt và cấu hình

### Chuẩn bị

Khởi tạo database trên node controller
```sh
mysql
CREATE DATABASE barbican;
GRANT ALL PRIVILEGES ON barbican.* TO 'barbican'@'localhost' IDENTIFIED BY 'tan124';
GRANT ALL PRIVILEGES ON barbican.* TO 'barbican'@'%' IDENTIFIED BY 'tan124';
```

Import biến môi trường
```sh
source admin-openrc
```

Tạo service xác thực. Tạo user `barbican`
```sh
openstack user create --domain default --password tan124 barbican
```

Thêm user `barbican` vào `admin` role
```sh
openstack role add --project service --user barbican admin
```

Tạo role `creator`
```sh
openstack role create creator
```

Thêm user `barbican` vào role `creator`
```sh
openstack role add --project service --user barbican creator
```

Tạo một service có tên `barbican`
```sh
openstack service create --name barbican --description "Key Manager" key-manager
```

Tạo API endpoint cho Key Manager service
```sh
openstack endpoint create --region RegionOne key-manager public http://controller:9311
openstack endpoint create --region RegionOne key-manager internal http://controller:9311
openstack endpoint create --region RegionOne key-manager admin http://controller:9311
```

### Cài đặt và cấu hình các thành phần

Cài đặt package
```sh
apt-get update && apt-get install barbican-api barbican-keystone-listener barbican-worker
```

Chỉnh sửa file cấu hình `/etc/barbican/barbican.conf` như sau.

trong thẻ `[DEFAULT]` ta chỉnh lại cấu hình database
```sh
[DEFAULT]
...
sql_connection = mysql+pymysql://barbican:tan124@controller/barbican
```

Tiếp tục cấu hình `RabbitMQ` để truy cập message queue
```sh
[DEFAULT]
...
transport_url = rabbit://openstack:tan124@controller
```

Trong thẻ `[keystone_authtoken]`, cấu hình truy cập dịch vụ xác thực, các config khác trong thẻ đã có thì xóa đi.
```sh
[keystone_authtoken]
...
auth_uri = http://controller:5000
auth_url = http://controller:35357
memcached_servers = controller:11211
auth_type = password
project_domain_name = default
user_domain_name = default
project_name = service
username = barbican
password = tan124
```

Cập nhật database cho Key Manager service
```sh
su -s /bin/sh -c "barbican-manage db upgrade" barbican
```

Barbican có một plugin cho phép triển khai lưu trữ secret trong một số back-end khác nhau. Mặc định, Barbican được cấu hình lưu trữ secret dựa vào file-based keystone.

### Hoàn thành cài đặt

Bạn khởi động lại server Key Manager.
```sh
service openstack-barbican-api restart
```

**Note**: Nếu bạn sử dụng firewall thì phải mở port 9311 trên node controller.

## Tham khảo

- [https://docs.openstack.org/barbican/pike/install/](https://docs.openstack.org/barbican/pike/install/)
- [https://docs.openstack.org/barbican/pike/index.html](https://docs.openstack.org/barbican/pike/index.html)
- [https://docs.openstack.org/barbican/pike/contributor/architecture.html](https://docs.openstack.org/barbican/pike/contributor/architecture.html)