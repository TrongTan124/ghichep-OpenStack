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

<a name="phan6"></a>
# Chương 6: Managing Security Groups

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