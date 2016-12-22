# 1. Giới thiệu

Hôm nay chúng ta sẽ tìm hiểu thêm về DHCP server, cơ chế cấp phát IP. Sau đó sẽ thử áp dụng cho trường hợp Openstack khởi tạo VM mà sử dụng DHCP server, không thông qua 
DHCP namespace.

# 2. Cài đặt

Tôi sử dụng Server cài Ubuntu 14.04 64bit. 

Tạo một switch bằng interface loopback

- Gói cài đặt DHCP server có sẵn trong các bản phân phối Linux khác nhau, là một phiên bản mã nguồn mở duy trì bởi ISC. Có 3 phiên bản khác nhau 2, 3, 4; ở đây phiên bản 3 hỗ trợ 
backup server, phiên bản 4 hỗ trợ IPv6. Trong lần này, chúng ta thử nghiệm ISC DHCP v3.
```sh
# apt-get install dhcp3-server
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Note, selecting 'isc-dhcp-server' instead of 'dhcp3-server'
Suggested packages:
  isc-dhcp-server-ldap
The following NEW packages will be installed:
  isc-dhcp-server
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 761 kB of archives.
After this operation, 2,138 kB of additional disk space will be used.
Get:1 http://us.archive.ubuntu.com/ubuntu/ trusty-updates/main isc-dhcp-server amd64 4.2.4-7ubuntu12.8 [761 kB]
Fetched 761 kB in 4s (182 kB/s)          
Preconfiguring packages ...
Selecting previously unselected package isc-dhcp-server.
(Reading database ... 102893 files and directories currently installed.)
Preparing to unpack .../isc-dhcp-server_4.2.4-7ubuntu12.8_amd64.deb ...
Unpacking isc-dhcp-server (4.2.4-7ubuntu12.8) ...
Processing triggers for ureadahead (0.100.0-16) ...
ureadahead will be reprofiled on next reboot
Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
Setting up isc-dhcp-server (4.2.4-7ubuntu12.8) ...
Generating /etc/default/isc-dhcp-server...
isc-dhcp-server start/running, process 2143
isc-dhcp-server6 stop/pre-start, process 2195
Processing triggers for ureadahead (0.100.0-16) ...
```

# 3 Cấu hình

File cấu hình của DHCP server vừa cài tại `/etc/dhcp/dhcpd.conf`. Cấu hình đơn giản nhất là tạo ra một pool
```sh
subnet 192.168.101.0 netmask 255.255.255.0 {
  range 192.168.101.100 192.168.101.200;
}
```

Với cấu hình trên sẽ chỉ dẫn cho DHCP server lắng nghe DHCP client yêu cầu trên subnet 192.168.101.0 với netmask 255.255.255.0. Ngoài ra sẽ gán địa chỉ IP trong dải 192.168.101.100 
tới 192.168.101.200

Thời gian mặc định cho việc cấp phát được cấu hình ở 2 tham số
- *default-lease-time* Số giây mà IP sẽ hết hạn nếu DHCP client không gửi yêu cầu gia hạn
- *max-lease-time* Thời gian tối đa tính bằng giây cho việc gia hạn một địa chỉ IP
```sh
default-lease-time 600;
max-lease-time 7200;
```

Tham số cấu hình thiết lập bởi DHCP server gửi tới client khai báo DNS server.
```sh
subnet 192.168.101.0 netmask 255.255.255.0 {
  range 192.168.101.100 192.168.101.200;
  option domain-name-servers 192.168.101.1, 8.8.8.8;
}
```

Thiết lập thêm cấu hình default gateway cho client khi nhận địa chỉ IP
```sh
subnet 192.168.101.0 netmask 255.255.255.0 {
  range 192.168.101.100 192.168.101.200;
  option domain-name-servers 192.168.101.1, 8.8.8.8;
  option routers 192.168.101.1;
}
```

Trong trường hợp với các máy in, hoặc web-server, cần chỉ định một địa chỉ IP cụ thể, ta khai báo thêm địa chỉ MAC với các máy đó
```sh
host printer {
  hardware ethernet 00:16:d3:b7:8f:86;
  fixed-address 10.1.1.100;
}

host web-server {
  hardware ethernet 00:17:a4:c2:44:22;
  fixed-address 10.1.1.200;
}
```

# 4. Thử nghiệm

Ta cài đặt một máy client có interface kết nối tới bridge loopback. Cài đặt thêm package `apt-get install vlan` và thiết lập mode vlan cho kernel `modprobe 8021q`.

Sau đó chỉnh sửa lại trong `/etc/network/interface` cấu hình như sau:
```sh
auto eth1
iface eth1 inet manual

auto eth1.101
iface eth1.101 inet dhcp
```

Thực hiện restart lại card mạng
```sh
# ifdown -a && ifup -a
```

Kết quả như sau:

![dhcp-1](/Images/dhcp-1.png)

Kiểm tra trên DHCP server xem log cấp phát và gia hạn IP tại `# cat /var/lib/dhcp/dhcpd.leases`
```sh
lease 192.168.101.100 {
  starts 4 2016/12/22 02:56:54;
  ends 4 2016/12/22 02:58:39;
  tstp 4 2016/12/22 02:58:39;
  cltt 4 2016/12/22 02:56:54;
  binding state free;
  hardware ethernet 00:0c:29:f6:65:7c;
}
lease 192.168.101.100 {
  starts 4 2016/12/22 02:58:43;
  ends 4 2016/12/22 03:08:43;
  cltt 4 2016/12/22 02:58:43;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 00:0c:29:f6:65:7c;
  client-hostname "ubuntu";
}
```

# 5. Áp dụng vào các VM trong OpenStack 

# Tham khảo
- [https://linuxconfig.org/what-is-dhcp-and-how-to-configure-dhcp-server-in-linux](https://linuxconfig.org/what-is-dhcp-and-how-to-configure-dhcp-server-in-linux)
- [https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s1-dhcp-configuring-server.html](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s1-dhcp-configuring-server.html)
- [http://www.yolinux.com/TUTORIALS/DHCP-Server.html](http://www.yolinux.com/TUTORIALS/DHCP-Server.html)

