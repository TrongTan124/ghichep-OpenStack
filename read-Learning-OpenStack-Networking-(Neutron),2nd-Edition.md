# Mục lục
* [1. Chương 1: Preparing the Network for OpenStack](#phan1)
* [2. Chương 2: Installing OpenStack](#phan2)
* [3. Chương 3: Installing Neutron](#phan3)
* [4. Chương 4: Building a Virtual Switching Infrastructure](#phan4)
* [5. Chương 5: Creating Networks with Neutron](#phan5)
* [6. Chương 6: Managing Security Groups](#phan6)
* [7. Chương 7: Creating Standalone Router with Neutron](#phan7)
* [8. Chương 8: Router Redundancy Using VRRP](#phan8)
* [9. Chương 9: Distributed Virtual Routers](#phan9)
* [10. Chương 10: Load Balancing Traffic to Instances](#phan10)
* [11. Chương 11: Firewall as a Service](#phan11)
* [12. Chương 12: Virtual Private Network as a Service](#phan12)
* [Appendix A](#phan13)
* [Appendix B](#phan14)

<a name="phan1"></a>
# Chương 1: Preparing the Network for OpenStack

#### OpenStack Networking là gì?

- OpenStack Networking là một pluggable, scalable và API-driven system để quản lý mạng và địa chỉ IP trong một OpenStack-based cloud. Như các thành phần core khác trong OpenStack, OpenStack Networking được sử dụng bởi
người quản trị và user để tăng value và maximize đối với tài nguyên có sẵn tại data center.

- Neutron - code name OpenStack Networking, là một standalone services giống như Nova (compute service), Glance (image service), Keystone (identity service), Cinder (block storage) và Horizon (dashboard). 
OpenStack Networking services có thể được tách ra nhiều host để cung cấp khả năng đàn hồi và dự phòng. Hoặc có thể được cài đặt trên một node đơn.

- OpenStack Networking cung cấp một application programmable interface (API), để người dùng hay các yêu cầu chuyển tới thực thi cấu hình network và xử lý khác. 
Người dùng có thể định nghĩa các kết nối mạng riêng trong cloud.

#### Các đặc tính của OpenStack Networking.

- OpenStack Networking bao gồm rất nhiều công nghệ trong datacenter, như switching, routing, load balancing, firewalling, và VPN.

##### Switching

##### Routing

##### Load balacing

##### Firewalling

#### Preparing the physical infrastructure

Khi thiết kế một network, quan trọng nhất là xác định cách thức cloud sẽ làm việc. Có yêu cầu về việc mở rộng, dự phòng không? 

![read-v2-1](/Images/read-v2-1.png)

#### Các loại network traffic

Theo kiến trúc của OpenStack Networking, có ít nhất 4 loại traffic có thể được nhìn thấy:
- Management
- API
- External
- Guest

#### Single Interface

- Trong trường hợp triển khai Cloud với 1 interface. Cả 4 loại traffic có thể đi chung vào một interface của HOST. 

![read-v2-2](/Images/read-v2-2.png)

#### Multiple Interfaces

- 


<a name="phan2"></a>
# Chương 2: Installing OpenStack

<a name="phan3"></a>
# Chương 3: Installing Neutron

#### Basic networking elements in Neutron.

- Sử dụng Neutron API, người dùng có thể tạo network với các thành phần sau:
	- Network
	- Subnet
	- Port
	
#### Extending functionality with plugins

Có 2 loại plugin trong kiến trúc Neutron
- Core plugin
	- ml2
- Service plugin
	- router
	- load balancer
	- firewall
	- virtual private network
	
#### ML2 architecture

Mô hình ở mức high level cách thức tương tác giữa các thành phần trong Neutron

![read-v2-3](/Images/read-v2-3.png)

#### Configuring the Neutron metadata agent

- OpenStack cung cấp metadata service để người dùng nhận các thông tin về instance. Các thông tin này được sử dụng để cấu hình hoặc quản lý instance. 
Metadata gồm nhiều thông tin như hostname, fixed, floating IP, public keys,...

- Instance truy cập vào metadata thông qua HTTP tại địa chỉ http://169.254.169.254 trong suốt quá trình khởi động

![read-v2-4](/Images/read-v2-4.png)

1. Instance gửi yêu cầu metadata tới 169.254.169.254 thông qua HTTP khi khởi động
2. Yêu cầu metadata gửi tới router hoặc DHCP namespace
3. metadata proxy service trong namespace gửi yêu cầu tới Neutron metadata thông qua Unix socket
4. Neutron metadata gửi yêu cầu tới Nova metadata API service
5. Nova metadata API service phản hồi lại yêu cầu tới Neutron metadata
6. Neutron metadata gửi trả lại phản hồi tới metadata proxy trong namespace
7. metadata proxy service gửi trả lại phản hồi tới instance thông qua HTTP
8. Instance nhận thông tin metadata và tiếp tục khởi động

<a name="phan4"></a>
# Chương 4: Building a Virtual Switching Infrastructure

<a name="phan5"></a>
# Chương 5: Creating Networks with Neutron

Trong các chương trước, chúng ta đã sắp xếp theo chiều từ trên xuống của một hạ tầng mạng ảo sẽ hỗ trợ OpenStack Neutron networking trong các phần tiếp. 
Trong chương này, chúng ta sẽ xây dựng một tài nguyên mạng. Tôi sẽ hướng dẫn bạn thông qua các task sau:
- Tạo network và subnet
- Gắn instance vào network
- Giải thích về DHCP và metadata

Network, subnet và port là tài nguyên cốt lõi của Neutron API. Mỗi liên hệ giữa tài nguyên này với instance, DHCP, metadata service được trình bày trong các phần phía dưới.

## Network management

OpenStack được quản lý theo nhiều cách khác nhau, thông qua GUI, API, và CLI. Neutron cung cấp cho người dùng khả năng thực thi các lệnh từ shell, đây là giao diện với 
Neutron API. Để đăng nhập vào Neutron shell, bạn gõ `neutron` trong shell trên controller node.
```sh
root@controller1:~# neutron
(neutron)
```

Các lệnh Neutron có thể được thực hiện một cách ngắn gọn từ Linux command line sử dụng Neutron client. Client cung cấp sẵn một số lệnh tạo, sửa, xóa network, subnet, port. 
Các câu lệnh nguyên mẫu được kết hợp với quản lý network:
```sh
net-create
net-delete
net-list
net-external-list
net-update
subnet-create
subnet-delete
subnet-list
subnet-show
subnet-update
port-create
port-delete
port-list
port-show
port-update
```

Bạn chọn Linux bridge hay Open vSwitch làm hạ tầng mạng, việc tạo, sửa, xóa network và subnet cũng tương tự. Tuy nhiên, việc kết nối từ instance tới các tài nguyên của mạng 
có một sự khác nhau tương đối lớn giữa chúng.

## Provider và tenant networks

Có 2 loại network có thể cung cấp kết nối giữa instance và các tài nguyên mạng khác, bao gồm virtual router:
- Provider network
- Tenant network

Mỗi network được tạo trong neutron, có thể bởi người quản trị hoặc tenant, đều cung cấp các thuộc tính mô tả về nó. Các thuộc tính được mô tả trong một network bao gồm: 
network type (flat, vlan, gre, vxlan), interface vật lý, segmentation ID. Sự khác nhau giữa provider và tenant network là cách ai hoặc thuộc tính gì hay cách nào mà chúng được quản 
lý trong OpenStack.

Provider network chỉ có thể được tạo và quản lý bởi người quản trị OpenStack khi họ yêu cầu hiểu biết cấu hình hạ tầng mạng vật lý. Khi provider được tạo, người quản trị phải chỉ định 
rõ các thuộc tính đối với network. Người quản trị cần phải có hiểu biết về hạ tầng mạng vật lý để cấu hình switch port phù hợp. Provider network thường được cấu hình ở flat hoặc vlan, và 
sử dụng một thiết bị routing bên ngoài cho phép route lưu lượng phù hợp vào hoặc ra khỏi cloud.

Tenant network, không như provider network, được tạo bởi người dùng và được phân tách với các mạng khác trong cloud. Không cần phải cấu hình hạ tầng vật lý, các tenant kết nối các network 
qua Neutron router khi kết nối ra ngoài được yêu cầu.

## Managing networks in the CLI

### Creating a flat network in the CLI

### Creating a VLAN network in the CLI

### Creating a local network in the CLI

## Listing network in the CLI

## Show network properties in the CLI

## Updating networks in the CLI

## Deleting networks in the CLI

## Creating networks in the dashboard

### Creating a network via the Admin tab as an administrator

### Creating a network via the Project tab as a user

## Subnet in Neutron

Khi một network được tạo, bước tiếp theo là tạo một subnet trong network. Một subnet trong Neutron là một layer 3 object và có thể là một IPv4 hoặc IPv6 network sử dụng ký hiệu CIDR. 
CIDR là cách phân chia địa chỉ IP và gói tin route, nó dựa vào VLSM. VLSM cho phép một network được chia thành các subnet có kích thước khác nhau, cung cấp kích thước mạng phù hợp

### Creating subnets in the CLI

Tạo một subnet sử dụng Neutron client, dùng lệnh `subnet-create`
```sh
usage:	subnet-create [--tenant-id TENANT_ID] [--name NAME]
		[--gateway GATEWAY_IP] [--no-gateway]
		[--allocation-pool start=IP_ADDR,end=IP_ADDR]
		[--host-route destination=CIDR,nexthop=IP_ADDR]
		[--dns-nameserver DNS_NAMESERVER]
		[--disable-dhcp] [--enable-dhcp]
		[--ip-version {4,6}]
		[--ipv6-ra-mode {dhcp6-stateful,dhcp6-stateless,slaac}]
		[--ipv6-address-mode {dhcp6-stateful,dhcp6-stateless,slaac}]
		NETWORK CIDR
```

**note**: Các thuộc tính trong ngoặc vuông là tùy chọn, không yêu cầu khi khởi tạo subnet

- Thuộc tính `tenant-id` đặc tả ID của tenant mà subnet sẽ được kết hợp cùng. Điều này để tenant được gắn cùng với mạng cha.

- Thuộc tính `name` chỉ ra tên của subnet. Khi bạn tạo nhiều subnet với cùng một tên, nó sẽ gợi ý rằng tên của subnet nên là duy nhất để dễ xác định.

- Thuộc tính `gateway` khai báo địa chỉ gateway của subnet. Khi subnet được gắn vào vùng cài đặt Neutron router, interface của router sẽ được cấu hình với một địa chỉ cụ thể. Địa chỉ 
thường được sử dụng là defautl gateway của subnet. Nếu subnet được gắn ra khu vực ngoài của Neutron router, địa chỉ được sử dụng như default gateway cho chính router đó. Để xem được cụ thể 
các hành vi này, tham khảo chương 7. Nếu gateway không được chỉ định, Neutron mặc định địa chỉ đầu tiên trong phạm vi CIDR.

- Thuộc tính `no-gateway` chỉ ra cho Neutron không được tự động gán một IP để sử dụng như gateway của subnet. Nó được dùng nhanh với một metadata route thông qua DHCP khi 
`enable_isolated_metadata` được thiết lập `true` trong file cấu hình DHCP.

- Thuộc tính `allocation-pool` khai báo một khoảng địa chỉ IP bên trong subnet có thể được gán cho instance hoặc như floating IPs.

- Thuộc tính `host-route` khai báo một hoặc nhiều static route được đưa vào DHCP. Nhiều route được liệt kê theo cặp *destination* và *nexthop*, có thể được chia tách bằng khoảng trắng.
Số route mặc định lớn nhất trên một subnet là 20 và có thể được sửa đổi trong `/etc/neutron/neutron.conf`. Số lượng lớn nhất của route trong router là 30 và có thể được sửa đổi trong file 
cấu hình Neutron.

**Cấu hình host route trong neutron**
	- Tạo một network, copy lại ID để sử dụng cho việc tạo subnet và khởi động instance:
	```sh
	# neutron net-create Test
	```
	
	- Kết quả trả về của lệnh trên
	![read-v2-5](/Images/read-v2-5.png)
	
	- Tạo một subnet với host route:
	```sh
	# neutron subnet-create \
     --ip-version 4 \
     --allocation-pool start=192.168.5.3,end=192.168.5.100 \
     --allocation-pool start=192.168.5.103,end=192.168.5.254 \
     --host-route destination=1.1.1.0/24,nexthop=192.168.5.254 \
     --tenant-id f36c5ad3e860402d84275eff4e3418c4 \
     34d3f31f-64e8-4d38-9417-f7f9d0e037d9 192.168.5.0/24
	```
	
	- Kết quả trả về của lệnh trên
	![read-v2-6](/Images/read-v2-6.png)
	
	- Tạo một server, cần các thông tin sau: tên instance, image ID, flavor ID, network ID
	```sh
	openstack server create --flavor m1.tiny --image cirros \
  --nic net-id=34d3f31f-64e8-4d38-9417-f7f9d0e037d9 --security-group default VM3
	```
	
	- Kết quả của lệnh trên
	![read-v2-7](/Images/read-v2-7.png)
	
	- Kiểm tra route của instance vừa tạo, ta thấy có một route static được DHCP gửi xuống.
	![read-v2-8](/Images/read-v2-8.png)

- Thuộc tính `dns-nameserver` thiết lập nameserver cho subnet. Số lượng mặc định lớn nhất của nameserver là 5/subnet và có thể được sửa đổi trong `/etc/neutron/neutron.conf`

- Thuộc tính `disable-dhcp` loại bỏ dịch vụ DHCP cho subnet.

- `enable-dhcp` cho phép dịch vụ DHCP chạy với subnet. Mặc định DHCP là enable

- Thuộc tính `ip-version` khai báo phiên bản của Internet protocol được sử dụng trong subnet. IPv4 được thiết lập mặc định khi version không được cấu hình.

- Thuộc tính `ipv6-ra-mode` và `ipv6-address-mode` sử dụng cho IPv6.

- Biến `NETWORK` khai báo network mà subnet được kết hợp cùng. Nhiều subnet có thể được kết hợp cùng với một network miễn là subnet không ghi đè nhau. Biến `NETWORK` có thể 
là UUID hoặc tên của network.

- Biến `CIDR` khai báo CIDR của subnet được tạo.

### Creating a subnet in the CLI

Giải thích lệnh bằng ví dụ, tạo một subnet trong *MyFlatNetwork* network với các thuộc tính sau:
- Internet Protocol: IPv4
- Subnet: 192.168.100.0/24
- Subnet mask: 255.255.255.0
- External gateway: 192.168.100.1
- DNS servers: 8.8.8.8,8.8.4.4

```sh
# neutron subnet-create MyFlatNetwork 192.168.100.0/24 --name MyFlatSubnet --ip-version=4 --dns-nameservers 8.8.8.8 8.8.4.4
```

### Listing subnets in the CLI

Để liệt kê các subnet trong Neutron, sử dụng lệnh `subnet-list`
```sh
# neutron subnet-list
```

Lệnh trên in ra ID, name, CIDR và địa chỉ IP cấp phát của tất cả các subnet khi được thực thi bởi người quản trị. Với người dùng, lệnh trên trả lại subnet trong project hoặc subnet 
gắn với network được chia sẻ.

<a name="phan6"></a>
# Chương 6: Managing Security Groups

Neutron có 2 cách thức cung cấp bảo mật cho các instance: security groups và virtual firewall. Chức năng security groups dựa vào iptables rules để lọc lưu lượng trên các compute node 
lưu trữ instance. Virtual firewall cung cấp bởi tính năng nâng cao Neutron service, được biết tới như Firewall as a Service (FaaS), cũng dựa trên iptables để lọc lưu lượng trong phạm vi của 
network trong Neutron router.

Trong chương này, chúng ta sẽ tập trung vào security group và một vài tính năng bảo mật cơ bản của Neutron:
- Giới thiệu ngắn gọn về iptables
- Tạo và quản lý security groups
- Giải thích các security group sử dụng iptables
- Loại bỏ port security

## Security groups trong OpenStack

Một security group là một tập hợp các network access rules, giới hạn các loại lưu lượng mà instance có thể gửi và nhận.

<a name="phan7"></a>
# Chương 7: Creating Standalone Router with Neutron

<a name="phan8"></a>
# Chương 8: Router Redundancy Using VRRP

<a name="phan9"></a>
# Chương 9: Distributed Virtual Routers

<a name="phan10"></a>
# Chương 10: Load Balancing Traffic to Instances

<a name="phan11"></a>
# Chương 11: Firewall as a Service

<a name="phan12"></a>
# Chương 12: Virtual Private Network as a Service

<a name="phan13"></a>
# Appendix A

<a name="phan14"></a>
# Appendix B