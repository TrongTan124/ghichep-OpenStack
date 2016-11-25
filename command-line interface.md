# Các command thường sử dụng trong OpenStack

Trên Neutron node (nếu cài cùng controller thì nằm trên Controller node)
----

#### Neutron - Agent status

- Kiểm tra trạng thái của các agent trong neutron
```sh
# neutron agent-list
```

	- Cột ID: là ID được sử dụng để xác định agent
	- agent_type: xác định loại agent
	- host: xác định vị trí host mà agent đang chạy
	- alive: trạng thái hoạt động của agent, xxx là NOK, :-) là OK
	- admin_state_up: 

#### Neutron - OVS - Tunnel Structure
	
- Kiểm tra cấu hình của OVS
```sh
# ovs-vsctl show
```

	- Liệt kê danh sách các virtual switch và các port trên virtual switch.
	- Thường có 3 ovs sau: br-ex, br-tun, br-int
	- br-tun sẽ tạo các tunnel kết nối với compute node qua br-int
	
#### Neutron - Internal Network Creation
	
- Neutron - internal network creation, tạo L2 network mà ko có subnet được gán
```sh
# neutron net-create Internal-Network
```
	
	- Kiểm tra network
```sh
# neutron net-list
```

#### Neutron - Internal Subnet Creation

- Tạo và gán một subnet mới vào network
```sh
# neutron subnet-create Internal-Network --name Internal-Subnet 10.12.13.0/24
```

	- view subnet vừa tạo
```sh
# neutron subnet-list -c id -c cidr -c name
```

	- view namespace
```sh
# ip netns show
```

#### Neutron - external Network Creation

- tạo một external network và gán IP subnet và pool vào nó
```sh
# neutron net-create External-Net --router:external=True

# neutron subnet-create External-Net 172.16.65.0/24 --allocaltion-pool start=172.16.65.100,end=172.16.65.150
```
	- Nếu muốn kết nối ra ngoài thì tên của network phải giống với tên được khai báo sẵn trong file cấu hình ml2 tại thẻ [ovs]
```sh
[ovs]
bridge_mappings = external:br-ex
```
	- Như cấu hình trên thì tên lúc tạo network phải là external, vì nó được chỉ định nối ra ngoài.
	
#### Neutron - Router Creation

- Tạo một router và kết nối tới Uplink (external network) vừa tạo
```sh
# neutron router-create MyRouter

# neutron router-gateway-set MyRouter External-Net

# neutron router-interface-add MyRouter Internal-Subnet
```

	- Các lệnh trên thực hiện tạo một router MyRouter, cho router này liên kết với external network, đồng thời add internal subnet vào router.

- Show router vừa tạo
```sh
# neutron router-show MyRouter
```

- view port trên router
```sh
# neutron router-port-list MyRouter -c fixed_ips
```

- view router
```sh
# ip netns show
```

- Vào trong namespace của router vừa tạo để show interface, sẽ thấy có 2 interface với 2 IP. 1 IP của dải External-Net (172.16.65.100), 1 IP của Internal-Subnet (10.12.13.1)
- Do set gateway là IP của dải External-Net nên nó sẽ có route default về đó.

# Tham khảo
