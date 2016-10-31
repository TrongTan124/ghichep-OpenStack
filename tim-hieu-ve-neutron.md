# 1. Giới thiệu
Service Networking của OpenStack cung cấp một API cho phép người dùng tạo và xác định các kết nối, địa chỉ trong cloud.
Project code-name cho service networking là neutron. OpenStack Networking điều khiển khởi tạo và quản lý hạ tầng mạng ảo, bao gồm:
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

# 2. Chức năng


# 3. Các thành phần





# Tham khảo
- [OpenStack basic](http://www.slideshare.net/KwonSunBae/openstack-basic-rev05)
- [OpenStack neutron and sdn](http://www.slideshare.net/inakipascual/openstack-neutron-and-sdn)
- []()