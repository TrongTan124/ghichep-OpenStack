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


<a name="phan2"></a>
# Chương 2: Installing OpenStack

![read-flow-agent](/Images/read-flow-agent.png)

<a name="phan3"></a>
# Chương 3: Installing Neutron

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