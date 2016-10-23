##1. Giới thiệu
<ul> - Network namespaces cho phép bạn tạo ra các môi trường mạng tách biệt trên 1 host vật lý. </ul>
<ul> - Mỗi namepaces có giao diện, bảng routing và forwarding riêng, phân chia với các namespaces khác.
Việc xử lý có thể riêng với từng network namespace cụ thể. </ul>
<ul> - Network namespace đã sử dụng trong các dự án đa dạng như Openstack, Docker và Minimet. 
Để tìm hiểu sâu về những dự án này, bạn phải làm quen với namespaces và biết được cách chúng làm việc. </ul>

####a. Làm việc với network namespaces
<ul> Khi bắt đầu với Linux, bạn sẽ có một namespace trên hệ thống, mọi tiến trình khởi tạo sẽ kế thừa namespace này như cha của nó. Vì vậy, tất cả các tiền trình thừa kế network namespace sử dụng bởi init (PID 1)  </ul>

<img src="http://i0.wp.com/abregman.com/wp-content/uploads/2016/09/namespace_level1.jpg">

####b. Liệt kê các namespace
<ul> Cách làm việc với namespace là sử dụng lệnh: *ip netns* </ul>
<ul> Để liệt kê tất cả các network namespace trên hệ thống của bạn, sử dụng lệnh "ip netns" hoặc "ip netns list" </ul>
```
# ip netns
```
<ul> Nếu bạn chưa thêm bất kỳ namespace nào, output sẽ trống. Namespace mặc định thì không được tính vào output của lệnh "ip netns list" </ul>

####c. Thêm namespace
<ul> Để thêm một namespace, sử dụng lệnh "ip netns add <name>" </ul>

Ví dụ:

##2. Chuẩn bị môi trường LAB
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
	<img src="http://prntscr.com/cxsl49">
	<li> Tạo kết nối từ ovs tới namespace tương ứng
		<ul> 
			<li> ip link add eth0-r type veth peer name veth-r </li>
			<li> ip link set eth0-r netns red </li>
			<li> ovs-vsctl add-port ovs1 veth-r </li>
			<img src="http://prntscr.com/cxslqp">
			<li> ip link add eth0-g type veth peer name veth-g </li>
			<li> ip link set eth0-g netns green </li>
			<li> ovs-vsctl add-port ovs1 veth-g </li>
			<img src="http://prntscr.com/cxsm1x">
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
	<li> </li>
	<li> </li>
	<li> </li>
	<li> </li>
</ul>


## Reference
[Youtube](https://www.youtube.com/watch?v=_WgUwUf1d34)
[link1](http://abregman.com/2016/09/29/linux-network-namespace/)
