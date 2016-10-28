##1. Linux bridge là gì?
<ul> Một bridge là cách thức kết hợp 2 hoặc nhiều các phần mạng (network segment) riêng biệt vào trong trong một logical network (ví dụ một IP-subnet)</ul>
<ul> Một bridge thường được đặt giữa 2 nhóm riêng biệt của máy tính, nơi chúng trao đổi với nhau nhưng không trao đổi với nhóm khác. </ul>
<ul> Công việc của bridge là xem xét đích của các data packet tại một thời điểm và lựa chọn cho packet đi tới side khác của Ethernet. 
Dẫn tới network sẽ nhanh hơn, đơn giản hơn với ít miền đụng độ </ul>
<ul> Luật bridge quyết định việc gửi hay xóa dữ liệu không dựa vào loại protocol (IP, IPX, NetBEUI), nhưng xem xét duy nhất địa chỉ MAC của mỗi NIC </ul>

<ul>Note: Quan trọng để hiểu bridge không phải là router hay firewall. Nói ngắn gọn, một bridge xử lý như một switch (Layer 2 switch), 
làm trong suốt các thành phần mạng (không chính xác tuyệt đối nhưng gần đúng).</ul>

<ul> Thêm nữa, bạn có thể khắc phục hardware không tương thích với một bridge, không cần sự cho phép address-range của IP-net hay subnet. </ul>
<ul>ví dụ: nó có thể bridge giữa kết nối vật lý khác nhau như 10 Base T và 100 Base TX </ul>

Các đặc tính thuần túy của bridge
<ul> STP: Spanning Tree Protocol là một phương thức thuận lợi cho việc giữ các thiết bị kết nối trong khi có nhiều kết nối.</ul>
<ul> Multiple Bridge Instances: cho phép bạn chạy được nhiều hơn một bridge trên máy và điều khiển một cái tách biệt nhau. </ul>
<ul> Fire-walling: Có một phần của luật bridging cho phép bạn sử dụng IP chain trên interface vào một bridge </ul>

##2. Các quy tắc trên Bridging
<ul> Có một số quy tắc bạn không được phép phá vỡ</ul>
<ul> 
<li> Một port chỉ có thể là một thành phần của một bridge  </li>
<li> Một bridge không biết gì về route </li>
<li> Một bridge không biết gì về các giao thức cao hơn ARP. Đó là lý do nó có thể bridge mọi giao thức có thế chạy trên Ethernet của bạn </li>
<li> Không có vấn đề về việc có bao nhiêu port trong logical bridge, nó được bao phủ bởi một logical interface duy nhất </li>
<li> Ngay khi một port (ví dụ một NIC) được gán cho một bridge, bạn sẽ không thể trực tiếp điều khiển nó </li>
</ul>

##3. Cài đặt bridge

```
# apt-get install bridge-utils
```

##4. Cấu hình bridge
<ul> Chắc chắn rằng tất cả các network card đều làm việc ổn và có thể truy nhập. </ul>
<ul> Gõ <b>ifconfig</b> để xem sơ đồ phần cứng của network interface.</ul>
<ul> Sau khi đã check những bước trên, gõ <b>modprobe -v bridge</b> </ul>
<ul> Bạn có thể kiểm tra đã thành công chưa bằng cách in ra <b>cat /proc/modules | grep bridge</b> </ul>
<ul> Nếu bridge-utilities đã được cài đúng và kernel, bridge-module đều OK, thực hiện gõ <b>brctl</b> để nhìn bảng tóm tắt lệnh </ul>
<ul>  </ul>

```
root@ubuntu:~# brctl
Usage: brctl [commands]
commands:
        addbr           <bridge>                add bridge
        delbr           <bridge>                delete bridge
        addif           <bridge> <device>       add interface to bridge
        delif           <bridge> <device>       delete interface from bridge
        hairpin         <bridge> <port> {on|off}        turn hairpin on/off
        setageing       <bridge> <time>         set ageing time
        setbridgeprio   <bridge> <prio>         set bridge priority
        setfd           <bridge> <time>         set bridge forward delay
        sethello        <bridge> <time>         set hello time
        setmaxage       <bridge> <time>         set max message age
        setpathcost     <bridge> <port> <cost>  set path cost
        setportprio     <bridge> <port> <prio>  set port priority
        show            [ <bridge> ]            show a list of bridges
        showmacs        <bridge>                show a list of mac addrs
        showstp         <bridge>                show bridge stp info
        stp             <bridge> {on|off}       turn stp on/off
```

**Tạo và xóa một Instace** với lệnh **addbr, delbr**
```
root@ubuntu:~# brctl addbr br0
```

**Chỉnh sửa dns**
vi /etc/resolv.conf
```
# add
nameserver 8.8.8.8 8.8.4.4
```

##5. Cấu hình STP cho bridge

##6. Cấu hình VLAN cho bridge


##7. Cấu hình bonding cho bridge
Nếu một Host có nhiều network interface, mà gần gom gộp thành một đường bonded để tận dụng băng thông hoặc cấu hình để chạy active-backup.
<img src="http://s0.cyberciti.org/uploads/faq/2016/07/bridge-bond-welcome.jpg">
<ul> Fig.01: Sample setup – KVM bridge with Bonding on Ubuntu LTS Server </ul>

####a. Tìm hiểu về bonding
<ul> Bonding cho phép bạn gom gộp nhiều port thành một nhóm đơn, kết hợp hiệu quả băng thông vào trong một kết nối </ul>
<ul> Bạn có thể sử dụng bonding ở nơi network cần đường dự phòng, chống chịu lỗi hay cân bằng tải. Nó là cách tốt nhất để có một network segment sẵn sàng cao (HA).
Một trường hợp rất hữu dụng sử dụng bonding là sử dụng trong kết nối hỗ trợ 802.1q VLAN </ul>
<ul> 
<ul> Các loại mode của bonding </ul>
<li> <b>mode=0 (balance-rr)</b> </li>
Thiết lập điều luật round-robin cho khả năng chịu lỗi và cân bằng tải. Sự truyền tin được gửi và nhận tuần tự trên mỗi slave interface bắt đầu với một slave sẵn sàng.

<li> <b>mode=1 (active-backup)</b> </li>
Cách hành xử active-backup: Chỉ có một slave trong bond được active. Một slave khác trở thành active chỉ khi active slave lỗi.
Địa chỉ MAC của bond chỉ hiển thị duy nhất một port để tránh confusing với switch. Mode này cung cấp khả năng chịu lỗi. tùy chọn đầu tiên sẽ ảnh hưởng tới hành xử trong mode này. 

<li> <b>mode=2 (balance-xor)</b> </li>
Cách hành xử XOR: Truyền tin dựa trên việc [(source MAC address XOR'd with destination MAC address) modulo slave count]. Điều này chọn slave giống với destination MAC address.
Mode này cung cấp cân bằng tải và khả năng chịu lỗi

<li> <b>mode=3 (broadcast)</b> </li>
Cách hành xử broadcast: Truyền tin tới mọi slave interface. Mode này cung cấp khả năng chịu lỗi.

<li>  <b>mode=4 (802.3ad)</b> </li>
IEEE 802.3ad chức năng tổng hợp link. Tạo ra các nhóm kết hợp tốc độ và duplex giống nhau. Sử dụng tất cả slave trong nhóm active theo một 802.3ad cụ thể.
<ul> Yêu cầu: 
<li> ethtool hỗ trợ trong base driver việc lấy tốc độ và duplex của mỗi slave. </li>
<li> Một switch hỗ trợ IEEE 802.3ad Dynamic link aggregation. Các switch yêu cầu cấu hình để enable 802.3ad mode.</li>
 </ul>

<li><b>mode=5 (balance-tlb)</b> (transmit load balancing) </li>
Thích ứng với truyền cân bằng tải: Channel bonding không yêu cầu switch hỗ trợ. Lưu lượng đi ra được phân tán dựa vào chiều load của mỗi slave. Lưu lượng đi vào được nhận bởi chiều slave.
Nếu slave nhận lỗi, một slave khác sẽ đè lên MAC address của slave nhận lỗi.
<ul> <i>Yêu cầu</i>: ethtool hỗ trợ trong base driver trong việc lấy speed và duplex của mỗi slave. </ul>

<li> <b>mode=6 (balance-alb)</b> (adaptive load balancing) </li>
Thích hợp cân bằng tải: Bao gồm balance-tbl được thêm receive load balancing (rlb) cho lưu lượng IPV4, và không yêu cầu switch hỗ trợ riêng biệt. RLB được hoàn tất bởi thương lượng ARP. 
Bonding driver chắn ARP replies gửi bởi local system theo cách của chúng ra ngoài và ghi đè source hardware address với hardware address duy nhất của mỗi slave trong bond như các peer 
khác nhau sử dụng hardware address khác nhau cho server. Bạn có thể sử dụng nhiều bond interface nhưng bạn phải load bonding module nhiều.

<li> </li>
<ul> Một số command: 
<li> <b>Check the bonding: </b> *root@ubuntu:~# ifenslave -a* </li>

<li> <b>Hoặc check:</b> *root@ubuntu:~# cat /proc/net/bonding/bond0*  </li>

<li> <b>Thay đổi active interface sang eth2:</b> *root@ubuntu:~# ifenslave -c bond0 eth2* </li>

<li> <b>Kiểm tra thông số speed:</b> *root@ubuntu:~# ethtool bond0* </li>
</ul>
 </ul>
<ul> Kiểm tra băng thông trong trường hợp gộp chung các network interface vào trong bond: </ul>
Sử dụng iperf để đo băng thông.

####c. Mô hình dưới là dựng thử nghiệm bonding active-backup
*Do mô hình active-active (gom gộp băng thông) cần cấu hình 02 đầu kết nối: cấu hình trên server và cấu hình trên switch vật lý thật*

<ul> Cài đặt thêm ifenslace cho Ubuntu </ul>
```
root@ubuntu:~# apt-get install ifenslave
```
<ul> Backup lại file /etc/network/interfaces </ul>
```
root@ubuntu:~# cp /etc/network/interfaces /etc/network/interfaces.bk
```
<ul> Sửa file */etc/network/interfaces* </ul>
```
root@ubuntu:~# vim /etc/network/interfaces
```
<ul> Đầu tiên tạo interface *bond0* mà không cấu hình địa chỉ IP và thêm eth1, eth2, eth3 như sau: </ul>
```
auto bond0
iface bond0 inet manual
mtu 1500
bond-miimon 100
bond-slaves none
bond-mode 1
bond-downdelay 200
bond-updelay 200
#bond-lacp-rate 1
#bond-lacp-rate fast
#post-up ifenslave bond0 eth1 eth2 eth3
#pre-down ifenslave -d bond0 eth1 eth2 eth3
#bond-xmit_hash_policy 1
```
<ul>  </ul>
<ul> Tiếp theo sửa/cập nhật eth0, eth1, eth2 không có địa chỉ IP, có bond master là bond0 </ul>
```
auto eth1
iface eth1 inet manual
bond-master bond0

auto eth2
iface eth2 inet manual
bond-master bond0

auto eth3
iface eth3 inet manual
bond-master bond0
```
<ul> Cuối cùng, tạo bridge br0 và gán một địa chỉ IP </ul>
```
auto br0
iface br0 inet static
address 172.16.69.70
netmask 255.255.255.0
broadcast 172.16.69.255
gateway 172.16.69.1
bridge_ports bond0
bridge_stp off
bridge_fd 9
bridge_hello 2
bridge_maxage 12
```
<ul> Lưu và đóng file, khởi động lại network interface </ul>
```
# ifdown -a && ifup -a
```

<ul> Kiểm tra lại: </ul>
```
# brctl show
```
Sample output
```
root@ubuntu:~# brctl show
bridge name	bridge id		STP enabled	interfaces
br0		8000.000c29a4c502	no		bond0
```
<ul> Xem trạng thái của bond0 và các thông tin khác </ul>
```
# cat /proc/net/bonding/bond0
```

####b. Hướng dẫn sử dụng iperf để đo băng thông
<ul> iperf là một công cụ thuận tiện cho việc tính toán băng thông của mạng </ul>
**Cài đặt trên Ubuntu dùng lệnh: **
```
root@ubuntu:~# apt-get install iperf
```
<ul> Sử dụng help để xem các tùy chọn của lệnh: </ul>
```
root@ubuntu:~# iperf --help
Usage: iperf [-s|-c host] [options]
       iperf [-h|--help] [-v|--version]

Client/Server:
  -f, --format    [kmKM]   format to report: Kbits, Mbits, KBytes, MBytes
  -i, --interval  #        seconds between periodic bandwidth reports
  -l, --len       #[KM]    length of buffer to read or write (default 8 KB)
  -m, --print_mss          print TCP maximum segment size (MTU - TCP/IP header)
  -o, --output    <filename> output the report or error message to this specified file
  -p, --port      #        server port to listen on/connect to
  -u, --udp                use UDP rather than TCP
  -w, --window    #[KM]    TCP window size (socket buffer size)
  -B, --bind      <host>   bind to <host>, an interface or multicast address
  -C, --compatibility      for use with older versions does not sent extra msgs
  -M, --mss       #        set TCP maximum segment size (MTU - 40 bytes)
  -N, --nodelay            set TCP no delay, disabling Nagle's Algorithm
  -V, --IPv6Version        Set the domain to IPv6

Server specific:
  -s, --server             run in server mode
  -U, --single_udp         run in single threaded UDP mode
  -D, --daemon             run the server as a daemon

Client specific:
  -b, --bandwidth #[KM]    for UDP, bandwidth to send at in bits/sec
                           (default 1 Mbit/sec, implies -u)
  -c, --client    <host>   run in client mode, connecting to <host>
  -d, --dualtest           Do a bidirectional test simultaneously
  -n, --num       #[KM]    number of bytes to transmit (instead of -t)
  -r, --tradeoff           Do a bidirectional test individually
  -t, --time      #        time in seconds to transmit for (default 10 secs)
  -F, --fileinput <name>   input the data to be transmitted from a file
  -I, --stdin              input the data to be transmitted from stdin
  -L, --listenport #       port to receive bidirectional tests back on
  -P, --parallel  #        number of parallel client threads to run
  -T, --ttl       #        time-to-live, for multicast (default 1)
  -Z, --linux-congestion <algo>  set TCP congestion control algorithm (Linux only)

Miscellaneous:
  -x, --reportexclude [CDMSV]   exclude C(connection) D(data) M(multicast) S(settings) V(server) reports
  -y, --reportstyle C      report as a Comma-Separated Values
  -h, --help               print this message and quit
  -v, --version            print version information and quit

[KM] Indicates options that support a K or M suffix for kilo- or mega-

The TCP window size option can be set by the environment variable
TCP_WINDOW_SIZE. Most other options can be set by an environment variable
IPERF_<long option name>, such as IPERF_BANDWIDTH.

Report bugs to <iperf-users@lists.sourceforge.net>
```
<ul> <b>Mô hình chung:</b> </ul>
<img src="https://camo.githubusercontent.com/69090167971053bf2170fec8175fbf627a799d82/687474703a2f2f692e696d6775722e636f6d2f697a4a487a6e6f2e706e67">

<ul> Một số lệnh đơn dùng để kiểm tra: 
	<ul>  Đối với TCP
		<li>  </li>
		<li> </li>
		<li> </li>
	</ul>
	<ul>  Đối với UDP
		<li> </li>
		<li> </li>
		<li> </li>
	</ul>
</ul>

<ul>  </ul>
<ul>  </ul>
<ul>  </ul>

##8. Kết hợp bridge với các máy ảo (VM) sử dụng virt
<img src="https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/images/Network_Interfaces-bridge-with-bond.png">
<ul> Hình: Một network bridge gồm 2 interface Ethernet được bonded </ul>
<ul>  </ul>

<ul> Câu lệnh tạo máy ảo bằng virt: </ul>
```
virt-install \
--name test2 \
--ram 1024 \
--disk path=/var/kvm/images/test2.img,size=20,bus=virtio \
--vcpus 1 \
--os-type linux \
--os-variant ubuntutrusty \
--network bridge=br0 \
--graphics none \
--console pty,target_type=serial \
--location 'http://jp.archive.ubuntu.com/ubuntu/dists/trusty/main/installer-amd64/' \
--extra-args 'console=ttyS0,115200n8 serial'
#--cdrom=/var/lib/libvirt/boot/CentOS-7-x86_64-Minimal-1511.iso \
# --virt-type=kvm \
# --hvm \
```

<ul> Mối liên hệ giữa tab interfaces và linux bridge </ul>
<b>Mạng vật lý vs mạng ảo</b>
<ul> Lưu lượng dữ liệu mạng được truyền bởi kết nối port Ethernet vật lý trên thiết bị vật lý. Tương tự cho máy ảo, các lưu lượng cũng cần được truyền bằng 
các port Ethernet ảo. Cuối cùng, lưu lượng từ các port ảo được gửi tới mạng vật lý để kết nối ra ngoài. Điều này xảy ra như thế nào? Nhìn hình phía dưới để xem các thành phần 
vật lý được liên kết ra sao trong mạng vật lý. </ul>
<ul>
	<li> Ethernet port trên server - thường được gọi là pNIC (physical NIC) </li>
	<li> Cáp RJ45 </li>
	<li> Ethernet port trên switch vật lý </li>
	<li> Port uplink trên switch vật lý - kết nối ra mạng ngoài </li>
</ul>
<img src="http://www.innervoice.in/blogs/wp-content/uploads/2013/12/Physical-Network.png">
<ul> Mục đích của ảo hóa là giả lập các thành phần vật lý trong phần mềm, nó phải hỗ trợ một cấu trúc tương tự: 
"Port ảo của một máy ảo được kết nối tới switch ảo"</ul>

<b>Port switch</b>
<ul> Như đã nói ở trên, Linux bridge cung cấp một switch vào trong nhân Linux. Như mọi switch, nó yêu cầu các port hay interface để mang dữ liệu vào và ra trên switch. </ul>
<img src="http://www.innervoice.in/blogs/wp-content/uploads/2013/12/VirtualNetwotk.png">



## Tham khảo
<ul> http://www.tldp.org/HOWTO/BRIDGE-STP-HOWTO/what-is-a-bridge.html</ul>
<ul> http://www.cyberciti.biz/faq/ubuntu-linux-bridging-and-bonding-setup/</ul>
<ul> http://www.linux-admins.net/2010/09/network-card-bonding-on-centos.html</ul>
<ul> https://github.com/ducnc/iperf#user-content-1-c%C3%A0i-%C4%91%E1%BA%B7t </ul>
<ul> https://github.com/lethanhlinh247/Thuc-tap-thang-03-2016/tree/master/LTLinh/LTLinh-Virtual%20Switching/Linux%20Bridge </ul>
<ul> https://github.com/lethanhlinh247/Thuc-tap-thang-03-2016/blob/master/LTLinh/LTLinh-Virtual%20Switching/LTLinh-NetworkBonding.md </ul>
<ul> http://www.innervoice.in/blogs/2013/12/08/tap-interfaces-linux-bridge/ </ul>
<ul> http://www.innervoice.in/blogs/2013/12/02/linux-bridge-virtual-networking/ </ul>
<ul> http://www.server-world.info/en/note?os=Ubuntu_14.04&p=kvm&f=2 </ul>
<ul> http://www.cyberciti.biz/faq/how-to-install-kvm-on-ubuntu-linux-14-04/ </ul>