# 1. Giới thiệu
<ul> - Network namespaces cho phép bạn tạo ra các môi trường mạng tách biệt trên 1 host vật lý. </ul>
<ul> - Mỗi namepaces có giao diện, bảng routing và forwarding riêng, phân chia với các namespaces khác.
Việc xử lý có thể riêng với từng network namespace cụ thể. </ul>
<ul> - Network namespace đã sử dụng trong các dự án đa dạng như Openstack, Docker và Minimet. 
Để tìm hiểu sâu về những dự án này, bạn phải làm quen với namespaces và biết được cách chúng làm việc. </ul>

## a. Làm việc với network namespaces
<ul> Khi bắt đầu với Linux, bạn sẽ có một namespace trên hệ thống, mọi tiến trình khởi tạo sẽ kế thừa namespace này như cha của nó. Vì vậy, tất cả các tiền trình thừa kế network namespace sử dụng bởi init (PID 1)  </ul>

<img src="http://i0.wp.com/abregman.com/wp-content/uploads/2016/09/namespace_level1.jpg">

<h2>b. Liệt kê các namespace</h2>
<ul> Cách làm việc với namespace là sử dụng lệnh: *ip netns* </ul>
<ul> Để liệt kê tất cả các network namespace trên hệ thống của bạn, sử dụng lệnh "ip netns" hoặc "ip netns list" </ul>
```
# ip netns
```
<ul> Nếu bạn chưa thêm bất kỳ namespace nào, output sẽ trống. Namespace mặc định thì không được tính vào output của lệnh "ip netns list" </ul>

<h2>c. Thêm namespace</h2>
<ul> Để thêm một namespace, sử dụng lệnh "ip netns add <name>" </ul>
<ul> Kiểm tra các namespace đã tạo, sử dụng lệnh "<i>ip netns list</i>" </ul>

<ul> Giải thích về <b>veth virtual ethernet</b>:là loại thiết bị mạng được tạo theo cặp. Có thể hình dung cặp này như một đường ống.
Mọi thứ được gửi vào đầu này sẽ ra ở đầu kia. Lệnh để tạo:
<li>ip link add eth0-r type veth peer name veth-r</li>
 </ul>

# 2. Chuẩn bị môi trường LAB
<ul> - Thực hiện lab trên môi trường máy ảo chạy Ubuntu server 14.04 64bit, đã cài openvswitch </ul>
<ul> NOTE: nên thực hiện clone và snapshot máy ảo để thuận tiện trong quá trình thử nghiệm </ul>

<ul> Các command thực hiện test:
	<li> Tạo 02 namespace 
		<ul> 
			<li> ip netns add red </li>
			<li> ip netns add green </li>
			<li> ip netns exec red ip link </li>
			<li> ip netns exec green ip link </li>
		</ul>
	</li>
	<li> Tạo ovs 
		<ul> 
			<li> ovs-vsctl add-br ovs1 </li>
			<li> ovs-vsctl show </li>
		</ul>
	</li>
	<img src="http://image.prntscr.com/image/1519a9586ea44ec38195ef06227ffb8c.png">
	<li> Tạo kết nối từ ovs tới namespace tương ứng
		<ul> 
			<li> ip link add eth0-r type veth peer name veth-r </li>
			<li> ip link set eth0-r netns red </li>
			<li> ovs-vsctl add-port ovs1 veth-r </li>
			<img src="http://image.prntscr.com/image/e8e6f6a60518406b803ea0426b99a528.png">
			<li> ip link add eth0-g type veth peer name veth-g </li>
			<li> ip link set eth0-g netns green </li>
			<li> ovs-vsctl add-port ovs1 veth-g </li>
			<img src="http://image.prntscr.com/image/51c9decdcd07465aa54b927c06374f40.png">
			<li> ovs-vsctl show 
				<p>     Bridge "ovs1"
						Port veth-g
							Interface veth-g
						Port veth-r
							Interface veth-r
						Port "ovs1"
							Interface "ovs1"
								type: internal
				</p>
			</li>
		</ul>
	</li>
	<li> Cấu hình các interface
		<ul> 
			<li> ip link </li>
			<li> ip link set veth-r up </li>
			<li> ip link set veth-g up </li>
			<li> ip netns exec red ip link set dev lo up </li>
			<li> ip netns exec red ip link set dev eth0-r up </li>
			<li> ip netns exec green ip link set dev lo up </li>
			<li> ip netns exec green ip link set dev eth0-g up </li>
			<li> ip netns exec red ip address add 10.0.0.1/24 dev eth0-r </li>
			<li> ip net exec red ip a 
				<p><i>     1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default
							link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
							inet 127.0.0.1/8 scope host lo
							   valid_lft forever preferred_lft forever
							inet6 ::1/128 scope host
							   valid_lft forever preferred_lft forever
						6: eth0-r@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
							link/ether 36:94:40:ba:c5:2d brd ff:ff:ff:ff:ff:ff
							inet 10.0.0.1/24 scope global eth0-r
							   valid_lft forever preferred_lft forever
							inet6 fe80::3494:40ff:feba:c52d/64 scope link
							   valid_lft forever preferred_lft forever
				</i></p>
			</li>
			<li> ip netns exec red ip route </li>
			<li> ip route </li>
			<li> ip netns exec green bash </li>
			<li> ip address add 10.0.0.2/24 dev eth0-g </li>
			<li> ip a </li>
			<li> ping 10.0.0.1 </li>
			<li> exit </li>
			<img src="http://image.prntscr.com/image/31f6e6b049a84c72b0d91835cc3b98c1.png">
			<img src="http://image.prntscr.com/image/e2f75ed39e504fa298c2b8a062a89c65.png">
		</ul>
	</li>
	<li> Các thành phần của node network:
		<img src="http://image.prntscr.com/image/a74fc6e828144dc2ae75bb9700b51d23.png">		
	</li>
	<li>  Cấu hình DHCP và tag VLAN cho 02 namespace
		<ul> 
			<li> ovs-vsctl set port veth-r tag=100 </li>
			<li> ovs-vsctl set port veth-g tag=200 </li>
			<img src="http://image.prntscr.com/image/d84f94a6223c4cb083fefcde6385d7ba.png">
			<li> ip netns exec red ip address del 10.0.0.1/24 dev eth0-r </li>
			<li> ip netns exec green ip address del 10.0.0.2/24 dev eth0-g </li>
			<li> ip netns add dhcp-r </li>
			<li> ip netns add dhcp-g </li>
			<img src="http://image.prntscr.com/image/312f2bd49e154a9295e9b00c0e0ac216.png">
			<li> ovs-vsctl add-port ovs1 tap-r </li>
			<li> ovs-vsctl set interface tap-r type=internal </li>
			<li> ovs-vsctl set port tap-r tag=100 </li>
			<li> ovs-vsctl add-port ovs1 tap-g </li>
			<li> ovs-vsctl set interface tap-g type=internal </li>
			<li> ovs-vsctl set port tap-g tag=200 </li>			
			<li> ovs-vsctl show 
				<p><i>
						Bridge "ovs1"
						Port veth-g
							tag: 200
							Interface veth-g
						Port tap-r
							tag: 100
							Interface tap-r
								type: internal
						Port tap-g
							tag: 200
							Interface tap-g
								type: internal
						Port veth-r
							tag: 100
							Interface veth-r
						Port "ovs1"
							Interface "ovs1"
								type: internal
				</i></p>
			</li>
			<img src="http://image.prntscr.com/image/32c1de754c7e4b40a7e1e98af1db405b.png">
			<li> ip link set tap-r netns dhcp-r </li>
			<li> ip link set tap-g netns dhcp-g </li>
			<img src="http://image.prntscr.com/image/3d8e68caee3846a781b130934960e701.png">
			<li> ip netns exec dhcp-r bash </li>
			<li> ip link set dev lo up </li>
			<li> ip link set dev tap-r up </li>
			<li> ip address add 10.50.50.2/24 dev tap-r </li>
			<li> ip netns exec dhcp-g bash </li>
			<li> ip link set dev lo up </li>
			<li> ip link set dev tap-g up </li>
			<li> ip address add 10.50.50.2/24 dev tap-g </li>
			<img src="http://image.prntscr.com/image/6ea07f7e9df6449ca7e3ee549b296f0e.png">
			<li> apt-get install dnsmasq </li>
			<li> ip netns exec dhcp-r dnsmasq --interface=tap-r --dhcp-range=10.50.50.10,10.50.50.100,255.255.255.0 </li>
			<li> ip netns exec dhcp-g dnsmasq --interface=tap-g --dhcp-range=10.50.50.10,10.50.50.100,255.255.255.0 </li>
			<li> ps -ef |grep dnsmasq 
				<p>
					nobody    22953      1  0 22:09 ?        00:00:00 dnsmasq --interface=tap-r --dhcp-range=10.50.50.10,10.50.50.100,255.255.255.0
					nobody    22956      1  0 22:11 ?        00:00:00 dnsmasq --interface=tap-g --dhcp-range=10.50.50.10,10.50.50.100,255.255.255.0
				</p>
			</li>
			<li> ip netns identify 22953 </li>
			<li> ip netns identify 22956 </li>
			<li> ip netns exec red dhclient eth0-r </li>
			<li> ip netns exec green dhclient eth0-g </li>
			<li> ip netns exec red ip a </li>
			<li> ip netns exec green ip a </li>
			<img src="http://image.prntscr.com/image/0c5b58687f864aaf877de5dfb16adfa6.png">
		</ul>
	</li>
</ul>


## Reference
[Youtube](https://www.youtube.com/watch?v=_WgUwUf1d34)
[link1](http://abregman.com/2016/09/29/linux-network-namespace/)
