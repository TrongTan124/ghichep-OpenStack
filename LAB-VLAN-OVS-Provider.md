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

- Vì LAB trường hợp kết nối VLAN nên interface trên switch phải thiết lập trunk. Ta không có môi trường switch vật lý thật nên sẽ tạo một card Loopback trên máy cài Vmware workstation. 
Cấu hình card Loopback như một bridge trên Linux (switch Layer2).

- Trên windows server 2012 gõ lệnh cửa sổ run

![lab-vlan-1](/Images/lab-vlan-1.png)

- Sẽ hiển thị cửa sổ add hardware như dưới

![lab-vlan-1](/Images/lab-vlan-1.png)

- Thực hiện tiếp các bước sau

![lab-vlan-3](/Images/lab-vlan-3.png)

![lab-vlan-4](/Images/lab-vlan-4.png)

![lab-vlan5](/Images/lab-vlan-5.png)

![lab-vlan-6](/Images/lab-vlan-6.png)

![lab-vlan-7](/Images/lab-vlan-7.png)

### Cấu hình bridge

- Sau khi tạo được Interface Loopback

## tham khảo
