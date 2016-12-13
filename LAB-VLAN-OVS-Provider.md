## Mô hình

- Trong phần này tôi sẽ sử dụng VMware Workstation để dựng hệ thống LAB OpenStack với OpenvSwitch dùng 3 interface:
	- eth0: API, Manage, tunnel
	- eth1: flat network, access internet
	- eth2: vlan network.

- Sơ đồ kết nối:

![lab-vlan-13](/Images/lab-vlan-13.png)

## Chuẩn bị:

- Hệ thống LAB cần đáp ứng các yêu cầu sau:
	- Sử dụng vmware workstation version 12 trở lên
	- Sử dụng hệ điều hành Windows server 2012 R2
	- Node Controller thiết lập 3GB RAM
	- Node Compute 1 thiết lập 2GB RAM
	- Node Compute 2 thiết lập 2GB RAM

## Cài đặt

### Thiết lập Interface Loopback

- Vì LAB trường hợp kết nối VLAN nên interface trên switch phải thiết lập trunk và kết nối tới interface trên HOST

## tham khảo
