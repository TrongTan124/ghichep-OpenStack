## Chuẩn bị

- Cài đặt OpenStack OVS với distro ubuntu 14.04 trước. theo tài liệu [này](https://github.com/congto/OpenStack-Mitaka-Scripts/tree/vlan/Ubuntu-OVS-VLAN/scripts)

- Tạo network với vlan 101, tạo VM gắn vào network.

- Cài đặt một host với linux bridge (không có openstack).

## Cài đặt

- Cài đặt linux bridge
```sh
apt-get -y install qemu-kvm libvirt-bin virtinst bridge-utils
```

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

## Phân tích traffic

### VM tới DHCP

Mô hình kết nối 

![lab-vlan-22](/Images/lab-vlan-22.png)

#### Ta thực hiện ping từ VM tới DHCP và sẽ phân tích traffice trên node Compute trước.

![lab-vlan-23](/Images/lab-vlan-23.png)

1 Xem port tap của VM bằng cách:
```sh
root@compute1:/etc/libvirt/qemu# vi instance-00000002.xml
...
    <interface type='bridge'>
      <mac address='fa:16:3e:fc:8f:1a'/>
      <source bridge='qbrccdf123f-97'/>
      <target dev='tapccdf123f-97'/>
      <model type='virtio'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
    </interface>
...
```

2 Linux bridge dc tạo gắn tap của VM vào
```sh
root@compute1:/etc/libvirt/qemu# brctl show
bridge name	bridge id		STP enabled	interfaces
qbrccdf123f-97		8000.22e693574fdb	no		qvbccdf123f-97
							tapccdf123f-97
```

3 Kết nối qvo-qvb là veth pair
```sh
root@compute1:/etc/libvirt/qemu# ip -d link show
...
12: qvoccdf123f-97@qvbccdf123f-97: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master ovs-system state UP mode DEFAULT group default qlen 1000
    link/ether 4e:97:d2:8d:9c:92 brd ff:ff:ff:ff:ff:ff promiscuity 2 
    veth 
13: qvbccdf123f-97@qvoccdf123f-97: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master qbrccdf123f-97 state UP mode DEFAULT group default qlen 1000
    link/ether 22:e6:93:57:4f:db brd ff:ff:ff:ff:ff:ff promiscuity 2 
    veth
...
```

4 Kiểm tra switch br-int bằng lệnh ovs-vsctl
```sh
root@compute1:/etc/libvirt/qemu# ovs-vsctl show
...
    Bridge br-int
        fail_mode: secure
        Port "qvoccdf123f-97"
            tag: 1
            Interface "qvoccdf123f-97"
        Port br-int
            Interface br-int
                type: internal
...
```

5 Kiểm tra switch br-vlan bằng lệnh ovs-vsctl
```sh
root@compute1:/etc/libvirt/qemu# ovs-vsctl show
...
    Bridge br-vlan
        fail_mode: secure
        Port phy-br-vlan
            Interface phy-br-vlan
                type: patch
                options: {peer=int-br-vlan}
        Port "eth2"
            Interface "eth2"
        Port br-vlan
            Interface br-vlan
                type: internal
    Bridge br-int
        ...
        Port int-br-vlan
            Interface int-br-vlan
                type: patch
                options: {peer=phy-br-vlan}
		...
...
```

6 Dữ liệu được đóng gói và gửi ra ngoài qua interface eth2

#### Kiểm tra bằng lệnh tcpdump trên Node compute

1 Thực hiện lệnh ping từ VM

![lab-vlan-24](/Images/lab-vlan-24.png)

2 Gói tin gửi ra tap interface

![lab-vlan-25](/Images/lab-vlan-25.png)

3 Gói tin qua linux bridge

![lab-vlan-26](/Images/lab-vlan-26.png)

4 Gói tin qua veth pair

![lab-vlan-27](/Images/lab-vlan-27.png)

![lab-vlan-28](/Images/lab-vlan-28.png)

5 Kiểm tra flow table trên switch br-int. gói tin được gán tag vlan 101 sẽ được bóc tách và gán tag 1

![lab-vlan-31](/Images/lab-vlan-31.png)

6 Kiểm tra flow table trên switch br-vlan. Gói tin đi ra được gán tag 1 sẽ bóc tách và gán tag vlan 101

![lab-vlan-32](/Images/lab-vlan-32.png)

7 Gói tin ra eth2

![lab-vlan-29](/Images/lab-vlan-29.png)

8 Gói tin được đóng gói 802.1q với vlan 101. Vlan này dc tạo để gắn VM vào bởi người quản trị.

![lab-vlan-30](/Images/lab-vlan-30.png)

#### Gói tin đi trên Node Controller

Gói tin được đi qua các thành phần trên node controller

![lab-vlan-33](/Images/lab-vlan-33.png)

1 Dữ liệu đi qua port eth2 (trunk)

2 Dữ liệu tới switch br-vlan
```sh
root@controller1:~# ovs-vsctl show
...
    Bridge br-vlan
        fail_mode: secure
        Port br-vlan
            Interface br-vlan
                type: internal
        Port phy-br-vlan
            Interface phy-br-vlan
                type: patch
                options: {peer=int-br-vlan}
        Port "eth2"
            Interface "eth2"
...
```

3. Dữ liệu đi tiếp tới switch br-int
```sh
root@controller1:~# ovs-vsctl show
...
    Bridge br-int
        ...
        Port "tap1f94f40d-7f"
            tag: 1
            Interface "tap1f94f40d-7f"
                type: internal
        Port br-int
            Interface br-int
                type: internal
        ...
        Port int-br-vlan
            Interface int-br-vlan
                type: patch
                options: {peer=phy-br-vlan}
		...
...
```

4. Dữ liệu tới tap interface của DHCP namespace

#### Bắt gói tin bằng tcpdump trên Node Controller

1. Dữ liệu đóng gói 802.1q khi vào eth2

![lab-vlan-35](/Images/lab-vlan-35.png)

2. Dữ liệu qua br-vlan, dump flow table trên switch br-vlan ta thấy gói tin khi đi ra mà có tag là 1 sẽ được bóc tách và gán tag vlan 101

![lab-vlan-36](/Images/lab-vlan-36.png)

3. Dữ liệu qua br-int, dump flow table trên br-int. ta thấy dữ liệu đi vào dc tag vlan 101 sẽ tách và gán vlan 1

![lab-vlan-37](/Images/lab-vlan-37.png)

4. Dữ liệu đi vào tap interface tới DHCP namespace

![lab-vlan-34](/Images/lab-vlan-34.png)

- Tôi thấy lệnh tcpdump chạy trong namespace ko tốt, gói tin ko hiển thị tức thời. nên tôi sử dụng lệnh tshark để bắt gói

![lab-vlan-38](/Images/lab-vlan-38.png)

## Tham khảo

- [http://net.doit.wisc.edu/~dwcarder/captivator/linux_trunking_bridging.txt](http://net.doit.wisc.edu/~dwcarder/captivator/linux_trunking_bridging.txt)
- [http://blog.frosty-geek.net/2011/02/ubuntu-tagged-vlan-interfaces-and.html](http://blog.frosty-geek.net/2011/02/ubuntu-tagged-vlan-interfaces-and.html)
- [http://blog.davidvassallo.me/2012/05/05/kvm-brctl-in-linux-bringing-vlans-to-the-guests/](http://blog.davidvassallo.me/2012/05/05/kvm-brctl-in-linux-bringing-vlans-to-the-guests/)
