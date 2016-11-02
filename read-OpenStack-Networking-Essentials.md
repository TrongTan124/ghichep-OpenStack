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

Neutron là một virtual networking service cho phép người dùng định nghĩa kết nối mạng, địa chỉ IP cho các instance và tài nguyên cloud khác sử dụng **application programmable interface 
(API)**. 

Hình dưới giải thích ở mức higt level về cách mà Neutron API server tương tác với các plugin và agent chịu trách nhiệm cấu thành mạng ảo và mạng vật lý trên cloud

![read-core-neutron](/Images/read-core-neutron.png)

Trong hình giải thích tương tác giữa Neutron API service, Neutron plugin, dirver, service như L2 và L3 agent. Khi một tác động mạng được thực thi bởi người dùng thông qua API, 
Neutron server đưa ra message và message queue để agent sử dụng. L2 agent xây dựng và vận hành hạ tầng mạng ảo, trong khi L3 agent chịu trách nhiệm xây dựng và vận hành Neutron router cùng 
các chức năng liên quan.

Đặc tả Neutron API có thể tìm thấy tại OpenStack wiki ở [đây](https://wiki.openstack.org/wiki/Neutron/APIv2-specification). Trong phần tiếp, chúng ta sẽ tìm hiểu về các core element của 
API và mô hình dữ liệu sử dụng bởi các element này.

## Networks

Network là đối tượng trung tâm của Neutron v2.0 API data model và chỉ ra một **Layer 2** segment riêng biệt. Trong hạ tầng truyền thống, máy tính được kết nối tới switch port 
và nhóm cùng nhau thành **Virtual Local Area Networks (VLANs)** định danh bởi IDs duy nhất. Các máy tính trong một mạng hay VLAN có thể kế nối với nhau và không thể kết nối ra mạng khác 
trong các VLAN khác khi thiếu router. Hình sau giải thích cách mà network được tách biệt với nhau trong hạ tầng mạng truyền thống.

![read-vlan.png](/Images/read-vlan.png)

### Network attributes

Bảng sau chỉ ra các thuộc tính cơ bản của network object; chi tiết hơn có thể tìm tại wiki trong mục trước.

![read-network-attribute](/Images/read-network-attribute.png)

Network được liên kết với tenants hoặc project, có thể sử dụng bởi người dùng là thành viên của cùng tenant hay project. Network có thể được chia sẻ với các project khác hay subnet của project 
sử dụng tính năng **Role Based Access Control (RBAC)** của Neutron.

**note**: Neutron RBAC xuất hiện đầu tiên trong bản Liberty của OpenStack, chi tiết về các đặc tính của RBAC xem tại [đây](https://developer.rackspace.com/blog/A-First-Look-at-RBAC-in-the-Liberty-Release-of-Neutron/)

### Provider attributes

Một trong những mở rộng sớm nhất của Neutron API được biết tới là **provider extension**. Provider network extension ánh xạ virtual network và physical network bằng cách thêm các thuộc tính mạng 
như network type, segmentation ID và physical interface. Hình sau chỉ ra các thuộc tình provider khác nhau:

![read-provider-attribute](/Images/read-provider-attribute.png)

Tất cả các network đều có thuộc tính provider. Tuy nhiên, vì thuộc tính provider chỉ rõ cấu hình và ánh xạ mạng cụ thể, chỉ người dùng có admin role có thể chỉ định chúng khi tạo mạng. 
Người dùng không có admin role có thể tạo network nhưng Neutron server, không phải người dùng, sẽ quyết định loại mạng được tạo và mọi interface hay segmentation ID phù hợp. Thuộc tính 
provider sẽ được xem lại chi tiết trong [phần 6](#phan6) và [phần 8](#phan8)

## Subnets

Trong mô hình Neutron data, một subnet là một khối địa chỉ IPv4 hoặc IPv6 có thể gán cho máy ảo và tài nguyên mạng khác. Mỗi subnet phải có một subnet mask đại diện bởi một 
**Classless Inter-Domain Routing (CIDR)** và phải liên kết với một network, như hình dưới.

![read-subnet](/Images/read-subnet.png)

Trong hình trên, 3 VLAN riêng biệt tương ứng với các subnet. Instance và các thiết bị khác không thể gắn vào network khi thiếu subnet cụ thể. Instance kết nối tới một network có thể kết nối 
với nhau nhưng không thể kết nối tới mạng khác hoặc subnet không sử dụng router. Xem thêm thông tin về router tại [phần 7](#phan7). Hình sau chỉ ra Neutron subnet thuộc Layer 3 trong mô hình OSI:

![read-subnet-osi](/Images/read-subnet-osi.png)

## Port

Trong mô hình Neutron data, một port đại diện cho một switch port trên một logical switch, triển khai trên toàn bộ cloud và chưa thông tin về thiết bị kết nối. **Virtual machine interfaces (VÍ)** 
và các đối tượng network khác như router và DHCP server interface được ánh xạ tới Neutron port. Các port định nghĩa cả địa chỉ MAC và địa chỉ IP được gán cho thiết bị liên kết với chúng. 
Mỗi port phải được kết nối với một Neutron network

Hình sau chỉ ra cách một port gắn với Layer 2 trong mô hình OSI:

![read-port-osi](/Images/read-port-osi.png)

Khi Neutron được cài lần đầu, không có port nào tồn tại trong database. Khi network và subnet được tạo, port có thể được tạo với mỗi DHCP server tương ứng với mô hình logical switch sau:

![read-port-logic](/Images/read-port-logic.png)

Khi một Instance được tạo, một port được tạo với mỗi network interface gắn với instance:

![read-port-vm](/Images/read-port-vm.png)

Một port chỉ có thể được kết nối với một network. Do đó, nếu một instance được kết nối tới nhiều networks, nó sẽ được liên kết với nhiều port. Khi instance và tài nguyên cloud được khởi tạo, 
logical switch có thể mở rộng tới hàng trăm, hàng nghìn port, xem hình dưới:

![read-port-creat](/Images/read-port-creat.png)

Không có giới hạn số port có thể được tạo trong Neutron. Tuy nhiên, tồn tại một quota giới hạn số port cho một tenant có thể tạo. Khi số port Neutron mở rộng, hiệu xuất của Neutron API server 
và thực thi của mạng trong cloud có thể giảm. Ý tưởng hay nhất là giữa quota tại điểm mà đảm bảo hiệu năng cloud, nhưng quota mặc định và subsequent nên tăng hợp lý.

## The Neutron workflow
----
Theo workflow Neutron chuẩn, network phải được tạo đầu tiên, theo đó là subnet và port. Các phần sau mô tả workflow liên quan trong khi khởi động và xóa các instance.

### Booting an instance

- 

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
