## Mô hình

- Trong phần này tôi sẽ sử dụng VMware Workstation để dựng hệ thống LAB OpenStack với OpenvSwitch dùng 3 interface:
	- eth0: API, Manage, tunnel
	- eth1: flat network, access internet
	- eth2: vlan network.

- Sơ đồ kết nối:

![lab-vlan-13](/Images/lab-vlan-13.png)

## Chuẩn bị:

- Hệ thống LAB cần đáp ứng các yêu cầu sau:
	- Sử dụng vmware workstation version 12 trở lên
	- Sử dụng hệ điều hành Windows server 2012 R2
	- Node Controller thiết lập 3GB RAM
	- Node Compute 1 thiết lập 2GB RAM
	- Node Compute 2 thiết lập 2GB RAM

## Cài đặt

### Thiết lập Interface Loopback

- Vì LAB trường hợp kết nối VLAN nên interface trên switch phải thiết lập trunk. Ta không có môi trường switch vật lý thật nên sẽ tạo một card Loopback trên máy cài Vmware workstation. 
Cấu hình card Loopback như một bridge trên Linux (switch Layer2).

- Trên windows server 2012 gõ lệnh cửa sổ run

![lab-vlan-1](/Images/lab-vlan-1.png)

- Sẽ hiển thị cửa sổ add hardware như dưới

![lab-vlan-1](/Images/lab-vlan-1.png)

- Thực hiện tiếp các bước sau

![lab-vlan-3](/Images/lab-vlan-3.png)

![lab-vlan-4](/Images/lab-vlan-4.png)

![lab-vlan5](/Images/lab-vlan-5.png)

![lab-vlan-6](/Images/lab-vlan-6.png)

![lab-vlan-7](/Images/lab-vlan-7.png)

### Cấu hình bridge

- Sau khi tạo được Interface Loopback, ta thiết lập một card bridge cho VMware workstation. Gắn card bridge này vào các Node để LAB Vlan. Đầu tiên bỏ interface loopback 
trên Vmnet0

![lab-vlan-8](/Images/lab-vlan-8.png)

- Tạo thêm Vmnet3 ở chế độ bridge, gắn interface Loopback vào.

![lab-vlan-9](/Images/lab-vlan-9.png)

### Thiết lập cấu hình interface node

- Cài đặt các máy ảo Ubuntu với một card mạng mặc định

- Cài đặt xong, thực hiện thêm interface card mạng cho các Node như phía dưới

![lab-vlan-10](/Images/lab-vlan-10.png)

![lab-vlan-11](/Images/lab-vlan-11.png)

![lab-vlan-12](/Images/lab-vlan-12.png)

### Cài đặt OpenStack

- Việc cài đặt tham khảo script tại [đây](https://github.com/congto/OpenStack-Mitaka-Scripts/tree/vlan/Ubuntu-OVS-VLAN)

- Vì Controller tôi để 3GB RAM nên không cần cài đặt Horizon, giao tiếp mọi thứ qua CLI (command line interface).

- Sau khi cài đặt xong, ta thực hiện tạo máy ảo theo Vlan được khai báo lúc tạo network.

### Phân tích traffic

- Mô hình các thành phần sau khi cài đặt xong OpenStack

![lab-vlan-14](/Images/lab-vlan-14.png)

- Sau đây tôi sẽ phân tích traffic từ VM tới DHCP theo các bước trong hình sau:

![lab-vlan-15](/Images/lab-vlan-15.png)

- Tôi sẽ nói lại một vài bước tạo network, subnet, instance.

- Tạo network với Vlan 101:
```sh
root@controller1:~# neutron net-create provider_VLAN --shared \
        --router:external True \
        --provider:physical_network provider --provider:network_type vlan \
        --provider:segmentation_id 101
```

- Sau khi tạo xong network, ta thực hiện tạo một subnet gán vào network vừa tạo
```sh
root@controller1:~# neutron subnet-create provider_VLAN 192.168.101.0/24 \
        --name provider_subnet_VLAN101 \
        --gateway 192.168.101.1 \
        --dns-nameserver 8.8.8.8 \
        --allocation-pool start=192.168.101.20,end=192.168.101.60
```

- Lấy ID của network vừa tạo
```sh
root@controller1:~# openstack network list
```

- Cuối cùng tạo một VM gắn theo ID của network vừa tạo
```sh
root@controller1:~# openstack server create --flavor m1.tiny --image cirros \
  --nic net-id=f89104ab-b3fe-4086-9d8d-f738d09128f8 --security-group default \
  VM1
```

- Lưu ý là phải có security cho phép ssh và ping
```sh
root@controller1:~# openstack security group rule create --proto icmp default
root@controller1:~# openstack security group rule create --proto tcp --dst-port 22 default
```

- Sau khi tạo network xong, sẽ có một namespace DHCP thực hiện cấp IP cho các VM, và lấy metadata từ nova cho các VM. kiểm tra namespace
```sh
root@controller1:~# ip netns
```

- Tôi kiểm tra IP gán cho namespace DHCP, ID của namespace lấy từ lệnh trên
```sh
root@controller1:~# ip netns exec qdhcp-f89104ab-b3fe-4086-9d8d-f738d09128f8 ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
15: tap69a39669-e5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default 
    link/ether fa:16:3e:e2:4d:33 brd ff:ff:ff:ff:ff:ff
    inet 192.168.101.20/24 brd 192.168.101.255 scope global tap69a39669-e5
       valid_lft forever preferred_lft forever
    inet 169.254.169.254/16 brd 169.254.255.255 scope global tap69a39669-e5
       valid_lft forever preferred_lft forever
    inet6 fe80::f816:3eff:fee2:4d33/64 scope link 
       valid_lft forever preferred_lft forever
```

- Lệnh thực hiện kiểm tra VM vừa tạo
```sh
root@controller1:~# openstack server list
```

- Tiến hành ssh vào VM từ namespace. ID của namespace và IP của VM có thể khác nhau tùy theo mỗi bài LAB
```sh
root@controller1:~# ip netns exec qdhcp-f89104ab-b3fe-4086-9d8d-f738d09128f8 ssh cirros@192.168.101.24
```

- Tôi kiểm tra interface trong VM
```sh
$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast qlen 1000
    link/ether fa:16:3e:5f:0b:34 brd ff:ff:ff:ff:ff:ff
    inet 192.168.101.24/24 brd 192.168.101.255 scope global eth0
    inet6 fe80::f816:3eff:fe5f:b34/64 scope link 
       valid_lft forever preferred_lft forever
$
```

- Ở đây, bạn lưu ý địa chỉ MAC của VM là: **fa:16:3e:5f:0b:34**

- Tôi sang node compute kiểm tra tap interface
```sh
root@compute1:~# ip a
...
23: tap431b983c-1f: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master qbr431b983c-1f state UNKNOWN group default qlen 500
    link/ether fe:16:3e:5f:0b:34 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::fc16:3eff:fe5f:b34/64 scope link 
       valid_lft forever preferred_lft forever
...
```

- Bạn để ý sẽ thấy địa chỉ MAC có sự tương đồng.

- Vừa xong, tôi đã thực hiện các bước kiểm tra interface bên trong VM, kiểm tra tap interface. Chúng ta lấy được UUID của kết nối tới VM là *431b983c-1f*

- Ta kiểm tra linux bridge mà tap interface của VM gắn vào
```sh
root@compute1:~# brctl show
bridge name			bridge id			STP enabled			interfaces
...
qbr431b983c-1f		8000.6ea8baf4c97b	no					qvb431b983c-1f
															tap431b983c-1f
...
```

- Ở LB này, có 2 interface, một được gắn vào VM, một được gắn vào OVS br-int.
```sh
root@compute1:~# ip -d link show
...
14: qbr431b983c-1f: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default 
    link/ether 6e:a8:ba:f4:c9:7b brd ff:ff:ff:ff:ff:ff promiscuity 0 
    bridge 
15: qvo431b983c-1f@qvb431b983c-1f: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master ovs-system state UP mode DEFAULT group default qlen 1000
    link/ether f2:62:61:2a:85:b3 brd ff:ff:ff:ff:ff:ff promiscuity 2 
    veth 
16: qvb431b983c-1f@qvo431b983c-1f: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master qbr431b983c-1f state UP mode DEFAULT group default qlen 1000
    link/ether 6e:a8:ba:f4:c9:7b brd ff:ff:ff:ff:ff:ff promiscuity 2 
    veth
...
```

- Kết nối giữa LB và OVS br-int là veth.

- Có một điểm lưu ý là LB sử dụng iptables để thiết lập security. Phần kiểm tra iptables ta sẽ xem xét sau.

- Tiếp đến ta kiểm tra ovs br-int
```sh
root@compute1:~# ovs-vsctl show
...
Bridge br-int
        ...
        Port br-int
            Interface br-int
                type: internal
        Port patch-tun
            Interface patch-tun
                type: patch
                options: {peer=patch-int}
        Port "qvo431b983c-1f"
            tag: 1
            Interface "qvo431b983c-1f"
        Port int-br-vlan
            Interface int-br-vlan
                type: patch
                options: {peer=phy-br-vlan}
        ...
...
```

- Trên br-int có kết nối tới ovs br-tun và ovs br-vlan. Kết nối giữa các ovs sử dụng loại patch.

- Ngay từ đầu ta tạo network với vlan là 101, nhưng khi đi qua ovs, sẽ được chuyển thành tag khác.
```sh
root@compute1:~# ovs-ofctl dump-flows br-int |grep in_port=1
...
cookie=0xbbd3c9c614f2dcd1, duration=2992.554s, table=0, n_packets=93, n_bytes=12746, idle_age=2704, priority=3,in_port=1,dl_vlan=101 actions=mod_vlan_vid:1,NORMAL
...

root@compute1:~# ovs-ofctl dump-flows br-int |grep in_port=4
...
 cookie=0xbbd3c9c614f2dcd1, duration=2881.039s, table=0, n_packets=0, n_bytes=0, idle_age=2881, priority=10,icmp6,in_port=4,icmp_type=136 actions=resubmit(,24)
 cookie=0xbbd3c9c614f2dcd1, duration=2880.837s, table=0, n_packets=16, n_bytes=672, idle_age=2683, priority=10,arp,in_port=4 actions=resubmit(,24)
 cookie=0xbbd3c9c614f2dcd1, duration=2881.230s, table=0, n_packets=121, n_bytes=13538, idle_age=2654, priority=9,in_port=4 actions=resubmit(,25)
 cookie=0xbbd3c9c614f2dcd1, duration=2881.135s, table=24, n_packets=0, n_bytes=0, idle_age=2881, priority=2,icmp6,in_port=4,icmp_type=136,nd_target=fe80::f816:3eff:fe5f:b34 actions=NORMAL
 cookie=0xbbd3c9c614f2dcd1, duration=2880.940s, table=24, n_packets=16, n_bytes=672, idle_age=2683, priority=2,arp,in_port=4,arp_spa=192.168.101.24 actions=resubmit(,25)
 cookie=0xbbd3c9c614f2dcd1, duration=2881.421s, table=25, n_packets=137, n_bytes=14210, idle_age=2654, priority=2,in_port=4,dl_src=fa:16:3e:5f:0b:34 actions=NORMAL
...
```

- Khi gói tin được tag vlan 101 đi vào thì được chuyển thành tag 1. nếu gói tin đi ra sẽ được tag ngược lại.

- Tôi grep port do tôi vừa check được là kết nối từ LB tới OVS được gán port 4 ở br-int.

- Tiếp đến gói tin sẽ từ br-int gửi tới br-vlan để đi ra ngoài. openflow trên br-vlan
```sh
root@compute1:~# ovs-ofctl dump-flows br-vlan
NXST_FLOW reply (xid=0x4):
 cookie=0x88698bea3de910a3, duration=3304.160s, table=0, n_packets=137, n_bytes=14210, idle_age=3016, priority=4,in_port=2,dl_vlan=1 actions=mod_vlan_vid:101,NORMAL
 cookie=0x88698bea3de910a3, duration=3358.566s, table=0, n_packets=23, n_bytes=1814, idle_age=3289, priority=2,in_port=2 actions=drop
 cookie=0x88698bea3de910a3, duration=3360.226s, table=0, n_packets=957, n_bytes=145089, idle_age=15, priority=0 actions=NORMAL
```

- Để ý sẽ thấy các gói tin có tag 1 sẽ được bóc tách và gắn vlan ban đầu 101, sau đó gửi ra ngoài.

## tham khảo
