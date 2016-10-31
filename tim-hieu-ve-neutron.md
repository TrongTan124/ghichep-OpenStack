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



# Tham khảo
- [http://www.slideshare.net/KwonSunBae/openstack-basic-rev05](http://www.slideshare.net/KwonSunBae/openstack-basic-rev05)
- [http://www.slideshare.net/inakipascual/openstack-neutron-and-sdn](http://www.slideshare.net/inakipascual/openstack-neutron-and-sdn)
- [http://www.innervoice.in/blogs/2015/01/13/openstack-neutron-components/](http://www.innervoice.in/blogs/2015/01/13/openstack-neutron-components/)
- []()