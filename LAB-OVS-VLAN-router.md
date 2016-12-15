## Chuẩn bị

- Cài đặt OpenStack OVS distro ubuntu 14.04 trước.

- Tạo network với vlan 101, tạo VM gắn vào network.

- Cài đặt một host với linux bridge (không có openstack)

## Cài đặt

- Yêu cầu cài đặt thêm gói vlan trên host cài linux bridge
```sh
apt-get install vlan
```

## Cấu hình

- Sử dụng một Host cài Linux bridge, cấu hình sub interface eth2.101

- Tạo linux bridge br-vlan101

- Gắn sub interface eth2.101 vào bridge br-vlan101

- Disable routing just in case:
```sh
echo 0 > /proc/sys/net/ipv4/ip_forward 
```

- setup vlans:
```sh
modprobe 8021q
```

- Cấu hình tham khảo
```sh
auto eth2
iface eth2 inet manual

auto eth2.101
iface eth2.101 inet manual
vlan-raw-device eth2

auto br-vlan101
iface br-vlan101 inet manual
bridge_ports eth2.101
bridge_fd 9
bridge_hello 2
bridge_maxage 12
bridge_stp off
up /sbin/ifconfig $IFACE up || /bin/true
```

- Sau khi cấu hình xong, thực hiện restart network
```sh
# ifdown -a && ifup -a
```

## Tham khảo

- [http://net.doit.wisc.edu/~dwcarder/captivator/linux_trunking_bridging.txt](http://net.doit.wisc.edu/~dwcarder/captivator/linux_trunking_bridging.txt)
- [http://blog.frosty-geek.net/2011/02/ubuntu-tagged-vlan-interfaces-and.html](http://blog.frosty-geek.net/2011/02/ubuntu-tagged-vlan-interfaces-and.html)
- [http://blog.davidvassallo.me/2012/05/05/kvm-brctl-in-linux-bringing-vlans-to-the-guests/](http://blog.davidvassallo.me/2012/05/05/kvm-brctl-in-linux-bringing-vlans-to-the-guests/)
