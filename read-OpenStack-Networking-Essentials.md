# Mục lục
* [1. Sơ lược](#phan1)
* [2. Chương 1: OpenStack Networking Components](#phan2)
* [3. Chương 2: Installing OpenStack Using RDO](#phan3)
* [4. Chương 3: Neutron API Basic](#phan4)
* [5. Chương 4: Interfaceing with Neutron](#phan5)
* [6. Chương 5: Switching](#phan6)
* [7. Chương 6: Routing](#phan7)
* [8. Chương 7: Building Network and Router](#phan8)
* [9. Chương 8: Security Group Fundamentals](#phan9)
* [Tham khảo](#phan10)

<a name="phan1"></a>
# 1. Sơ lược
OpenStack là một hệ điều hành cloud nguồn mở, được thiết kế để điều khiển chung tài nguyên của các thành phần compute, storage, networking. 
Hệ thống này tăng trưởng mạnh mẽ để giảm chi phí vận hành và vốn. OpenStack đã bùng nổ trong vài năm trở lại đây nhờ các tính năng, linh hoạt và độ trưởng thành.

Trong sách này, chúng ta sẽ khám phá về thành phần networking của OpenStack, được biết như Neutron. Neutron cung cấp một API cho người dùng xây dựng tài nguyên mạng ảo như 
switch, router, load balancer, firewall. Chúng ta sẽ đi qua việc cài đặt OpenStack sử dụng RDO để xem các thành phần cốt lõi của API, tạo nên network, subnet, port. 
Kết thúc quyển sách này, bạn sẽ khai khác được sức mạnh của OpenStack và Neutron khi tạo và truy cập tài nguyên mạng ảo của bạn.


<a name="phan2"></a>
# 2. Chương 1: OpenStack Networking Components
**Các đặc tính của OpenStack Networking**

Rất nhiều môi trường cloud dựa vào các công nghệ ảo hóa có sẵn bởi hypervisors như: KVM, Xen, Hyper-V,... Mục đích cốt lõi của Neutron là kết nối các máy ảo tới mạng ảo của cloud và 
kết nối mạng ảo tới hạ tầng mạng vật lý.

Neutron dựa vào các thành phần gắn vào nó hoặc kiến trúc mở rộng để cấu hình tài nguyên mạng ảo và vật lý. Rất nhiều thiết bị như switch, router, firewall, load balancer được thực thi 
trong phần mềm được tham chiếu (reference implementation). Một tham chiếu được đề cập khi sử dụng là plugin và agent có sẵn trong Neutron. Plugin phổ biến là **Modular Layer 2 (ML2)** plugin,
được sử dụng xác định tổ chức (framework) mạng vật lý để agent có thể xây dựng mạng ảo. Agent phổ biến gồm **Open vSwitch (OVS)** và **Linux bridge** agent được sử dụng để xây dựng hạ tầng 
switch ảo tương ứng dựa trên mạn được người dùng định nghĩa với Neutron API.

**Switching**

Trong một thành phần tham chiếu, Neutron dựa vòa virtual bridge và switch để kết nối virtual instance, container và các tài nguyên mạng khác tới network. Neutron hỗ trợ các chuẩn 
Linux bridges và virtual switch tạo bởi OVS. OVS là một switch ảo mã nguồn mở hỗ trợ nhiều công nghệ và giao thức như: NetFlow, SPAN, RSPAN, LACP, 802.1q Vlan tagging. Neutron hỗ trợ việc sử dụng 
nhiều công nghệ overlay networking như GRE, VXLAN để kết nối virtual bridge và switch thông qua node tới node khác. tham khảo thêm tại [phần 6](#phan6)

**Routing**

Neutron cung cấp routing và NAT để cho phép instance và thiết bị mạng ảo khác truy cập mạng. Khi người dùng tạo một mạng ảo, mạng này được tách biết với các mạng khác. Người dùng 
có thể tạo router và gắn một hoặc nhiều mạng ảo vào. Khi được gắc, các thiết bị trong mạng có thể kết nối với nhau, một vài trường hợp có thể kết nối ra ngoài internet Neutron cũng 
cung cấp kết nối đi vào thông qua floating IP. Một floating IP là 1 liên kết 1-1 từ instance trong mạng ảo tới địa chỉ IP trên mạng thực. Tham khảo thêm tại [phần 7](#phan7)

**Các đặc tính nâng cao khác**

- **Load balacing**:

- **Firewalling**:

- **Virtual private networks**:

**Kiến trúc OpenStack**

- **Controller nodes**

- **Network nodes**

- **Compute nodes**

- **Storage nodes**

![3node-read-ONE](/Images/3node-read-ONE.png)

**Implementing the network**

- **Plugins and drivers**: 
	- **Core plugins**: chịu trách nhiệm tương thích với logical network bằng API để có thể thực thi bằng L2 agent và IP Address Management (IPAM) chạy trên host. ML2 plugin được sử dụng tại đây.
	
	- **Service plugins**: Cung cấp việc add thêm các dịch vụ mạng như routing, load balancing, firewalling và có sẵn cho các tham chiếu.
	
ML2 plugin dựa vào các loại khác nhau của driver để xác định loại network thực thi và cơ chế để thực thi. **Type drivers** mô tả các loại mạng được hỗ trợ bởi Neutron, gồm: FLAT, VLAN, VXLAN, GRE.
**Mechanism drivers** được sử dụng để thực thi mạng được chỉ ra trong phần mềm và phần cứng.

- **Neutron agents**

Neutron server là controller tập trung cảu mạng, chịu trách nhiệm cung cấp API cho người dùng lưu trữ thông tin về mạng trong database. Tuy nhiên, lệnh thực thi mạng được thực hiện tại
compute và network node bởi các agent. Neutron agent nhận message và chỉ dận từ Neutron server trong message bus và thực thi.

	- **DHCP agent**:
	
	- **metadata agent**:
	
	- **network plugin agent**:
	
![read-flow-agent](/Images/read-flow-agent.png)

1. Neutron nhận yêu cầu kết nối máy ảo tới mạng. API server gọi tới ML2 plugin để xử lý yêu cầu
2. ML2 plugin chuyển tiếp yêu cầu tới OVS mechanism driver để tạo message sử dụng thông tin có sẵn trong request. Message được gửi cho OVS agent tương ứng xử lý thông qua quản lý network.
3. OVS agent nhận message và cấu hình trên local virtual switch
4. Trong lúc đó, DHCP agent cũng nhận message tương ứng của yêu cầu này và cấu hình DHCP server trên network node. Sau khi xong, virtual machine instance sẽ giao tiếp với DHCP server và nhận 
địa chỉ IP thông qua dữ liệu mạng.

<a name="phan3"></a>
# 3. Chương 2: Installing OpenStack Using RDO
- Phần cài đặt, tôi thực hiện theo bài [này](https://github.com/TrongTan124/ghichep-OpenStack/blob/master/huong-dan-cai-dat-openstack-mitaka-ovs.md)

<a name="phan4"></a>
# 4. Chương 3: Neutron API Basic

<a name="phan5"></a>
# 5. Chương 4: Interfaceing with Neutron

<a name="phan6"></a>
# 6. Chương 5: Switching

<a name="phan7"></a>
# 7. Chương 6: Routing

<a name="phan8"></a>
# 8. Chương 7: Building Network and Router

<a name="phan9"></a>
# 9. Chương 8: Security Group Fundamentals

<a name="phan10"></a>
# Tham khảo
- [https://www.packtpub.com/virtualization-and-cloud/openstack-networking-essentials](https://www.packtpub.com/virtualization-and-cloud/openstack-networking-essentials)
