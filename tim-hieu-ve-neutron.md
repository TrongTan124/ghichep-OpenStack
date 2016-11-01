# 1. Giới thiệu
Service Networking của OpenStack cung cấp một API cho phép người dùng tạo và xác định các kết nối, địa chỉ trong cloud.
Project code-name cho service networking là neutron. OpenStack Networking điều khiển việc khởi tạo và quản lý hạ tầng mạng ảo, bao gồm:
network, switch, subnet và router cho các thiết bị được quản lý bởi OpenStack Compute service (nova). Các dịch vụ cao cấp như firewall 
hoặc VPNs cũng có thể được sử dụng.

OpenStack Networking bao gồm neutron-server, 1 DB cho lưu trữ dài hạn, và một số plug-in agents, cái mà cung cấp dịch vụ giao diện cho 
cơ chế mạng native Linux, thiết bị mở rộng hoặc SDN controller. 

OpenStack Networking là một thành phần độc lập, có thể được triển khai tới các host riêng biệt.

- OpenStack Networking tương tác với các thành phần OpenStack khác:
	- OpenStack Identity service (keystone): xác thực và cấp quyền cho các API request
	- OpenStack Compute service (nova): sử dụng để gắn mỗi virtual NIC trên VM vào một mạng cụ thể
	- OpenStack Dashboard (horizon): sử dụng bởi người quản trị và tenant user để tạo và quản lý dịch vụ mạng thông qua giao diện đồ họa web
	
![Mitaka-topo-flow-ops](/Images/topo_flow_OPS.png)

==> Tổng kết: OpenStack Networking cung cấp một hạ tầng thiết bị mạng ảo cho các virtual machine tương tự như các thiết bị mạng vật lý.

# 2. Các thành phần

## a. Neutron Server
- Thành phần chính của Neutron là Neutron-server. Được viết bằng python và có thể khởi động khi thực thi lệnh
```sh
service neutron-server start
```

- Neutron-server khởi động 2 thành phần chính:
	- Neutron REST service
	- Neutron Plugin - core and service plugin
	
- Neutron RPC service cũng được khởi động để liên kết neutron-server với các agent. RPC server thường load cùng Neutron Plugin
![Neutron-Server-Loading-Steps](/Images/Neutron-Server-Loading-Steps.png)

## b. Neutron Plugins
- Plugin trong Neutron cho phép mở rộng và/hoặc tùy biến các chức năng đã có sẵn trong Neutron. Các Networking vendor có thể viết plugin để đảm bảo trơn tru kết nối giữa OpenStack Neutron 
và phần cứng, phần mềm của vendor cụ thể. Việc tiếp cận này làm phong phú tài nguyên mạng thật và ảo có thể cung cấp cho các virtual machine.

## c. Neutron Agents
- Các agent là tập hợp các thành phần quan trọng khác trong Neutron. Thành phần chính Neutron-server (và các plugin) kết nối với các Neutron agent. 
Neutron agents thực thi chức năng mạng đặc thù. Ví dụ DHCP agent, L3 agent. Một vài agent đặc biệt cho plugin như Linux Bridge Neutron Agent.

# 3. Kiến thức bổ sung
## a. Port trong OpenStack Neutron
- OpenStack hỗ trợ trừu tượng phong phú để xử lý nhu cầu mạng ảo hóa trong cloud. Với một người dùng, đối tượng dễ thấy nhất là các network, subnet, router, firewall,... 
Nhưng nếu xem xét điểm vào và ra của lưu lượng dữ liệu, đối tượng quan trọng nhất là Port. OpenStack Neutron Port thường được tạo tự động như một phần của hoạt động người dùng. 
Tuy nhiên, CLI cũng cho phép người dùng tạo các Port một các độc lập.

- Port trong OpenStack networking được thực hiện bằng cách sử dụng giao diện (hầu hết là ảo) phía dưới hypervisor. Địa chỉ IP dùng cho máy ảo, router cũng thường được lưu trữ với các 
đối tượng Port. Thậm chí Port đại diện cho điểm xuất nhập data trafic và các cấu hình liên quan như interface và địa chỉ IP. Chúng đóng một vai trò quan trọng trong OpenStack networking.

- Các loại port trong OpenStack:

![Topology-OpenStack-Ports-1](/Images/Topology-OpenStack-Ports-1.png)

Trong hình trên, chúng ta có 2 Tenant Network, mỗi network có 1 máy ảo và 1 DHCP server. Hai Network được kết nối với nhau qua một Tenant Router. Thêm nữa, chúng ta sẽ sử dụng một 
External Network và thiết lập nó như gateway trên Tenant Router cho các máy ảo truy nhập Internet. Topology đúng của OpenStack Network nhìn như sau:

![OpenStack-Ports-Network-Topology-248x300](/Images/OpenStack-Ports-Network-Topology-248x300.png)

- Để xem loại port trong cấu hình OpenStack, bao gồm cả các thuộc tính "device_owner" của port bằng lệnh sau:

![OpenStack-Ports-CLI-Output-1024x191](/Images/OpenStack-Ports-CLI-Output-1024x191.png)

- **compute:nova** chỉ ra rằng port được gán cho máy ảo. Các port này được tạo tự động như một phần của máy ảo (thông qua nova). Phần "compute" chỉ ra rằng port được tạo trên compute node.

- **network:dhcp** chỉ ra port được gán cho DHCP server. Từ "network" ngụ ý rằng, port này được tạo trên Netwok node. Port DHCP được tạo khi máy ảo đầu tiên được tạo trên mạng tương ứng.

- **network:router_interface** miêu tả "gateway" IP interface cho một tenant network và máy ảo của nó. Interface này được kết hợp với một OpenStack router (namespace). Các port loại này được tạo khi người 
dùng thực hiện "Add interface" trên Router. Bạn sẽ nhìn thấy nhiều "network:router_interface" port - một trong những network ở ví dụ. Thêm một lần nữa, port loại này được nhìn thấy tại Network Node.

- **network:router_gateway**: với một Router, External Network miêu tả "gateway" đi ra mạng ngoài (internet). Một port cụ thể loại "network:router_gateway" được tạo ở đây. 
Port này được tạo khi người dùng thực hiện "Set Gateway" trên một Router và Port này trên Network Node.

## b. Neutron Plugin trong OpenStack
- Networking trong OpenStack (cho các VM) thì tương tự với networking trong thế giới thật. VM khởi tạo yêu cầu tối thiểu kết nối Layer2 (L2) network. Thêm nữa, khởi tạo có thể 
yêu cầu dịch vụ routing, firewall và load-balancing. Các công nghệ và dịch vụ mạng này có thể thực thi sử dụng kết hợp với phần mềm và phần cứng mạng. 

**ML2 - the most important core plugin** 
- ML2 hay Modular Layer 2 plugin được gói lại với OpenStack. Nó là important core plugin bởi vì nó hỗ trợ nhiều công nghệ L2. Quan trọng hơn, ML2 plugin cho phép nhiểu công nghệ 
của nhiều nhà cung cấp cùng tồn tại.

- ML2 plugin hỗ trợ nhiều công nghệ Layer2 như VLAN, VXLAN, GRE,... Các công nghệ này được nhắc đến như Type drivers.

## c. Neutron Agent trong OpenStack
- Trong khi Neutron server đóng vai trò như controller tập trung, câu lệnh liên quan tới mạng thực tế và cấu hình được thực thi trên Compute và Network node. Và các agent là các thực thể 
thực thi thay đổi mạng thực tế trên các node đó. Các agent nhận tin nhắn và chỉ dẫn từ Neutron server (thông qua plugin hoặc trực tiếp) trên message bus.

- Hình dưới chỉ ra một cái nhìn đơn giản tương tác của plugin và agent với các thành phần trong OpenStack Networking

**Ví dụ: Open vSwitch (OVS) Plugin and Agent**

![OVS-Plugin-Agents](/Images/OVS-Plugin-Agents.jpg)

- Trong hình trên, Neutron nhận một API request (thông qua điều khiển trên Horizon hay CLI). API server gọi đến ML2 plugin để xử lý request. ML2 plugin đã tải OVS mechanism driver và 
chuyển tiếp yêu cầu liên quan tới OVS driver. OVS driver tạo một RPC message sử dụng các thông tin có sẵn trong request. RPC message là **cast** tới một OVS agent cụ thể chạy trên compute 
node. OVS agent này nhận RPC message và thực hiện cấu hình khởi tạo trên local OVS switch.

# 4. Flow trong Neutron

- Trong mục này sẽ trình bày về kết quả từ một cấu hình OpenStack cụ thể
	- Neutron sử dụng GRE tunnel
	- Network controller riêng biệt
	- Một instance chạy trên một compute host

![Neutron_architecture](/Images/Neutron_architecture.png)
Kiến trúc thu gọn các liên kết trong Neutron

**Compute host: instance networking (A,B,C)**
- Một packet đi ra ngoài từ eth0 của virtual instance (VM) được kết nối tới một tap device trên host, `tap7c7ae61e-05`. Tap device này được gắn vào Linux bridge, `qbr7c7ae61e-05`. 
Muốn biết về linux bridge thì tham khảo tại [đây](https://github.com/TrongTan124/ghichep-OpenStack/blob/master/tim-hieu-ve-linux-bridge.md).

	- Lý tưởng nhất, Tap device vnet0 được kết nối trực tiếp tới integration bridge, br-int. Tiếc là điều này không thể vì các OpenStack security group đang được thực thi. 
	OpenStack sử dụng iptables rule trên TAP device như vnet0 để thực thi security group, và OpenStack thì không tương thích với iptables rules được áp dụng trực tiếp trên
	TAP device kết nối với OpenvSwitch port.

	- Vì bridge device này tồn tại chủ yếu để hỗ trợ firewall rules, nên sẽ đề cập tới nó như là "firewall bridge".
	
	- Nếu muốn kiểm tra firewall rules trên compute host, bạn sẽ tìm thấy vài rule được kết hợp với `tap` device này:
```sh
# iptables -S | grep tap7c7ae61e-05
-A quantum-openvswi-FORWARD -m physdev --physdev-out tap7c7ae61e-05 --physdev-is-bridged -j quantum-openvswi-sg-chain 
-A quantum-openvswi-FORWARD -m physdev --physdev-in tap7c7ae61e-05 --physdev-is-bridged -j quantum-openvswi-sg-chain 
-A quantum-openvswi-INPUT -m physdev --physdev-in tap7c7ae61e-05 --physdev-is-bridged -j quantum-openvswi-o7c7ae61e-0 
-A quantum-openvswi-sg-chain -m physdev --physdev-out tap7c7ae61e-05 --physdev-is-bridged -j quantum-openvswi-i7c7ae61e-0 
-A quantum-openvswi-sg-chain -m physdev --physdev-in tap7c7ae61e-05 --physdev-is-bridged -j quantum-openvswi-o7c7ae61e-0
```

- `quantum-openvswi-sg-chain` là nơi `neutron-managed security group` được thực hiện. `quantum-openvswi-o7c7ae61e-0` chain điều khiển lưu lượng đi ra từ VM, mặc định được nhìn thấy như sau:
```sh
-A quantum-openvswi-o7c7ae61e-0 -m mac ! --mac-source FA:16:3E:03:00:E7 -j DROP 
-A quantum-openvswi-o7c7ae61e-0 -p udp -m udp --sport 68 --dport 67 -j RETURN 
-A quantum-openvswi-o7c7ae61e-0 ! -s 10.1.0.2/32 -j DROP 
-A quantum-openvswi-o7c7ae61e-0 -p udp -m udp --sport 67 --dport 68 -j DROP 
-A quantum-openvswi-o7c7ae61e-0 -m state --state INVALID -j DROP 
-A quantum-openvswi-o7c7ae61e-0 -m state --state RELATED,ESTABLISHED -j RETURN 
-A quantum-openvswi-o7c7ae61e-0 -j RETURN 
-A quantum-openvswi-o7c7ae61e-0 -j quantum-openvswi-sg-fallback
```

- `quantum-openvswi-i7c7ae61e-0` chain điều khiển dữ liệu đi vào VM. Sau khi mở port 22 trong security group mặc định:
```sh
# neutron security-group-rule-create --protocol tcp \
  --port-range-min 22 --port-range-max 22 --direction ingress default
```

- Các rule được nhìn thấy như sau:
```sh
-A quantum-openvswi-i7c7ae61e-0 -m state --state INVALID -j DROP 
-A quantum-openvswi-i7c7ae61e-0 -m state --state RELATED,ESTABLISHED -j RETURN 
-A quantum-openvswi-i7c7ae61e-0 -p icmp -j RETURN 
-A quantum-openvswi-i7c7ae61e-0 -p tcp -m tcp --dport 22 -j RETURN 
-A quantum-openvswi-i7c7ae61e-0 -p tcp -m tcp --dport 80 -j RETURN 
-A quantum-openvswi-i7c7ae61e-0 -s 10.1.0.3/32 -p udp -m udp --sport 67 --dport 68 -j RETURN 
-A quantum-openvswi-i7c7ae61e-0 -j quantum-openvswi-sg-fallback
```

- Interface thứ 2 được gắn vào bridge, `qvb7c7ae61e-05`, gắn từ firewall bridge tới integration bridge, `br-int`.

**Compute host: integration bridge (D,E)**
- Integration bridge, `br-int`, thực hiện gán VLAN và bỏ gán VLAN cho traffic đến và đi từ VM. Tại thời điểm này, `br-int` được nhìn như sau:
```sh
# ovs-vsctl show
Bridge br-int
    Port &quot;qvo7c7ae61e-05&quot;
        tag: 1
        Interface &quot;qvo7c7ae61e-05&quot;
    Port patch-tun
        Interface patch-tun
            type: patch
            options: {peer=patch-int}
    Port br-int
        Interface br-int
            type: internal
```

- Interface `qvo7c7ae61e-05` là điểm cuối của `qvb7c7ae61e-05`, vận chuyển lưu lượng tới và đi từ firewall bridge. `tag: 1` bạn nhìn thấy ở trên được tích hợp vào access port gắn với VLAN 1.
Lưu lượng đi ra ko được tag từ VM sẽ được gán VLAN ID 1, và lưu lượng đi vào từ VLAN ID 1 sẽ bỏ VLAN tag rồi gửi vào port.

- Mỗi network bạn tạo (với lệnh `neutron net-create`) sẽ được gán với VLAN ID khác nhau.

- Tên interface `patch-tun` kết nối giữa integration bridge và tunnel bridge, `br-tun`.

**Compute host: tunnel bridge (F,G)**
- Tunnel bridge chuyển lưu lượng đã gán VLAN từ integration bridge tới GRE tunnel. Chuyển đổi giữa VLAN ID và tunnel ID được thực hiện bới OpenFlow rules cài trong `br-tun`. 
Trước khi tạo mọi VM, flow rule trên bridge như sau:
```sh
# ovs-ofctl dump-flows br-tun
NXST_FLOW reply (xid=0x4):
 cookie=0x0, duration=871.283s, table=0, n_packets=4, n_bytes=300, idle_age=862, priority=1 actions=drop
```

# Tham khảo
- [http://www.slideshare.net/KwonSunBae/openstack-basic-rev05](http://www.slideshare.net/KwonSunBae/openstack-basic-rev05)
- [http://www.slideshare.net/inakipascual/openstack-neutron-and-sdn](http://www.slideshare.net/inakipascual/openstack-neutron-and-sdn)
- [http://www.innervoice.in/blogs/2015/01/13/openstack-neutron-components/](http://www.innervoice.in/blogs/2015/01/13/openstack-neutron-components/)
- [http://www.innervoice.in/blogs/2015/07/05/ports-in-openstack-neutron/](http://www.innervoice.in/blogs/2015/07/05/ports-in-openstack-neutron/)
- [http://www.innervoice.in/blogs/2015/03/31/openstack-neutron-plugins-and-agents/](http://www.innervoice.in/blogs/2015/03/31/openstack-neutron-plugins-and-agents/)
- [https://www.rdoproject.org/networking/networking-in-too-much-detail/](https://www.rdoproject.org/networking/networking-in-too-much-detail/)
