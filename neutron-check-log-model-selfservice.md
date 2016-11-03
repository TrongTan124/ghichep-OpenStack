# 1 Check log của Neutron

Lần đầu sau khi cài đặt xong OpenStack, có thể kiểm tra các dòng log xuất hiện lần đầu trong file log của các thành phần

Câu lệnh cài đặt Neutron theo mô hình selfservice tại node controller
```sh
apt-get -y install neutron-server neutron-plugin-ml2 \
    neutron-plugin-openvswitch-agent neutron-l3-agent \
    neutron-dhcp-agent neutron-metadata-agent  \
    python-neutronclient ipset
```

Sau khi cài đặt xong thì log sẽ được lưu tại 02 nơi là
```sh
root@controller1:# cd /var/log/neutron
root@controller1:# cd /var/log/openvswitch
```

Tại thư mục */var/log/neutron* sẽ có các file log sau:
```sh
-rw-r--r--  1 neutron neutron  15336 Nov  3 12:12 dhcp-agent.log
-rw-r--r--  1 neutron neutron   8943 Nov  3 12:13 l3-agent.log
-rw-r--r--  1 neutron neutron  12201 Nov  3 12:44 neutron-metadata-agent.log
-rw-r--r--  1 neutron neutron 536758 Nov  3 14:18 neutron-server.log
-rw-r--r--  1 neutron neutron  30046 Nov  3 12:44 openvswitch-agent.log
-rw-r--r--  1 neutron neutron    728 Nov  3 12:42 ovs-cleanup.log
```

Tại thư mục */var/log/openvswitch* sẽ có các file log sau:
```sh
-rw-r--r--  1 root root      689 Nov  3 12:40 ovs-ctl.log
-rw-r--r--  1 root root      787 Nov  3 12:40 ovsdb-server.log
-rw-r--r--  1 root root   101698 Nov  3 12:42 ovs-vswitchd.log
```

Bây giờ ta sẽ lần lượt kiểm tra log của các file
- Đối với file

# Tham khảo