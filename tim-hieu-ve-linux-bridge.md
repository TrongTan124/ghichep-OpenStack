##1. Linux bridge là gì?
<ul> Một bridge là cách thức phân chia thành 2 hoặc nhiều hơn các phần mạng (network segment) riêng biệt trong phạm vi một logical network (ví dụ một IP-subnet)</ul>
<ul> Một bridge thường được đặt giữa 2 nhóm riêng biệt của máy tính, nơi chúng trao đổi với nhau nhưng không trao đổi với nhóm khác. </ul>
<ul> Công việc của bridge là xem xét đích của các data packet tại một thời điểm và lựa chọn có cho packet đi tới side khác của Ethernet. 
Dẫn tới network sẽ nhanh hơn, đơn giản hơn với ít miền đụng độ </ul>
<ul> Luật bridge quyết định việc gửi hay xóa dữ liệu không dựa vào loại protocol (IP, IPX, NetBEUI), nhưng xem xét duy nhất địa chỉ MAC của mỗi NIC </ul>
**Note**: Quan trọng để hiểu bridge không phải là router hay firewall. Nói ngắn gọn, một bridge xử lý như một switch (Layer 2 switch), 
làm trong suốt các thành phần mạng (không chính xác tuyệt đối nhưng gần đúng).
<ul> Thêm nữa, bạn có thể khắc phục hardware không tương thích với một bridge, không cần sự cho phép address-range của IP-net hay subnet. </ul>
ví dụ: nó có thể bridge giữa kết nối vật lý khác nhau như 10 Base T và 100 Base TX

**Các đặc tính thuần túy của bridge**
<ul> STP: Spanning Tree Protocol là một phương thức thuận lợi cho việc giữ các thiết bị kết nối trong khi có nhiều kết nối.</ul>
<ul> Multiple Bridge Instances: cho phép bạn chạy được nhiều hơn một bridge trên máy và điều khiển một cái tách biệt nhau. </ul>
<ul> Fire-walling: Có một phần của luật bridging cho phép bạn sử dụng IP chain trên interface vào một bridge </ul>

##2. Các quy tắc trên Bridging
Có một số quy tắc bạn không được phép phá vỡ
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
<ul> Gõ **ifconfig** để xem sơ đồ phần cứng của network interface.</ul>
<ul> Sau khi đã check những bước trên, gõ **modprobe -v bridge** </ul>
<ul> Bạn có thể kiểm tra đã thành công chưa bằng cách in ra **cat /proc/modules | grep bridge** </ul>
<ul> Nếu bridge-utilities đã được cài đúng và kernel, bridge-module đều OK, thực hiện gõ **brctl** để nhìn bảng tóm tắt lệnh </ul>
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
<li> **mode=1 (active-backup)** </li>
Cách hành xử active-backup: Chỉ có một slave trong bond được active. Một slave khác trở thành active chỉ khi active slave lỗi.
Địa chỉ MAC của bond chỉ hiển thị duy nhất một port để tránh confusing với switch. Mode này cung cấp khả năng chịu lỗi. tùy chọn đầu tiên sẽ ảnh hưởng tới hành xử trong mode này. 

<li> **mode=2 (balance-xor)** </li>
Cách hành xử XOR: Truyền tin dựa trên việc [(source MAC address XOR'd with destination MAC address) modulo slave count]. Điều này chọn slave giống với destination MAC address.
Mode này cung cấp cân bằng tải và khả năng chịu lỗi

<li> **mode=3 (broadcast)** </li>
Cách hành xử broadcast: Truyền tin tới mọi slave interface. Mode này cung cấp khả năng chịu lỗi.

<li>  **mode=4 (802.3ad)** </li>
IEEE 802.3ad chức năng tổng hợp link. Tạo ra các nhóm kết hợp tốc độ và duplex giống nhau. Sử dụng tất cả slave trong nhóm active theo một 802.3ad cụ thể.
<ul> Yêu cầu: 
<li> ethtool hỗ trợ trong base driver việc lấy tốc độ và duplex của mỗi slave. </li>
<li> Một switch hỗ trợ IEEE 802.3ad Dynamic link aggregation. Các switch yêu cầu cấu hình để enable 802.3ad mode.</li>
 </ul>

<li> **mode=5 (balance-tlb)** </li>


<li> </li>

<li> </li>
 </ul>
<ul>  </ul>
<ul>  </ul>

#### Mô hình dưới là dựng thử nghiệm bonding active-backup
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

##8. Kết hợp bridge với các máy ảo (VM) sử dụng virt
<img src="https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/images/Network_Interfaces-bridge-with-bond.png">
<ul> Hình: Một network bridge gồm 2 interface Ethernet được bonded </ul>
<ul>  </ul>

## Tham khảo
<ul> http://www.tldp.org/HOWTO/BRIDGE-STP-HOWTO/what-is-a-bridge.html</ul>
<ul> http://www.cyberciti.biz/faq/ubuntu-linux-bridging-and-bonding-setup/</ul>
<ul> http://www.linux-admins.net/2010/09/network-card-bonding-on-centos.html</ul>
