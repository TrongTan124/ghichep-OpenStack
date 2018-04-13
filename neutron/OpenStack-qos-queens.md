# Cài đặt

Đối với network backend sẽ có QoS hỗ trợ tương ứng theo chiều In/Out của traffic.

|Rule back end		|OpenvSwitch	|SR-IOV	|Linux bridge	|
|-------------------|---------------|-------|---------------|
|Bandwidth limit	|Egress/Ingress	|Egress	|Egress/Ingress	|
|Minimum bandwidth	|				|Egress	|				|
|DSCP marking		|Egress			|		|Egress			|

Cấu hình trên node `controller`

- Trong `/etc/neutron/neutron.conf` ta chỉnh lại tham số `service_plugins` và tham số `notification_drivers` như sau:
```sh
[DEFAULT]
service_plugins = router,qos,neutron.services.metering.metering_plugin.MeteringPlugin
.
.
.
[qos]
notification_drivers = message_queue
```

- Trong file `/etc/neutron/plugins/ml2/ml2_conf.ini` ta cũng sửa lại biến `extension_drivers` như sau:
```sh
[ml2]
extension_drivers = port_security,qos
```

- Trong file `/etc/neutron/plugins/ml2/openvswitch_agent.ini` ta sửa lại biến `extensions` như sau:
```sh
[agent]
extensions = qos
```

Trên node `compute`, ta chỉ sửa lại một biến duy nhất trong file `/etc/neutron/plugins/ml2/openvswitch_agent.ini`
```sh
[agent]
extensions = qos
```

Điều chỉnh lại file `/etc/neutron/policy.json` để cho phép các user thường cũng sử dụng được tính năng `qos`. 
```sh
"get_policy": "rule:regular_user",
"create_policy": "rule:regular_user",
"update_policy": "rule:regular_user",
"delete_policy": "rule:regular_user",
"get_rule_type": "rule:regular_user",

"get_policy_bandwidth_limit_rule": "rule:regular_user",
"create_policy_bandwidth_limit_rule": "rule:regular_user",
"delete_policy_bandwidth_limit_rule": "rule:regular_user",
"update_policy_bandwidth_limit_rule": "rule:regular_user",

"get_policy_dscp_marking_rule": "rule:regular_user",
"create_dscp_marking_rule": "rule:regular_user",
"delete_dscp_marking_rule": "rule:regular_user",
"update_dscp_marking_rule": "rule:regular_user",

"get_policy_minimum_bandwidth_rule": "rule:regular_user",
"create_policy_minimum_bandwidth_rule": "rule:regular_user",
"delete_policy_minimum_bandwidth_rule": "rule:regular_user",
"update_policy_minimum_bandwidth_rule": "rule:regular_user",
```

Khởi động lại các tiến trình của `neutron` trên cả node compute và controller
```sh
controller:
/etc/init.d/neutron-dhcp-agent restart
/etc/init.d/neutron-l3-agent restart
/etc/init.d/neutron-metadata-agent restart
/etc/init.d/neutron-openvswitch-agent restart
/etc/init.d/neutron-server restart

compute:
/etc/init.d/neutron-openvswitch-agent restart
```

# Sử dụng

Khởi tạo một QoS policy bằng lệnh
```sh
openstack network qos policy create bw-limiter
```

Giới hạn băng thông theo chiều VM đi ra tốc độ 3Mbps
```sh
openstack network qos rule create --type bandwidth-limit --max-kbps 3000 --max-burst-kbits 3000 --egress bw-limiter
```

Giới hạn băng thông theo chiều vào VM tốc độ 3Mbps
```sh
openstack network qos rule create --type bandwidth-limit --max-kbps 3000 --max-burst-kbits 3000 --ingress bw-limiter
```

*NOTE*: lưu ý giá trị `burst` không nên quá thấp, thường lấy 80% giá trị giới hạn. nếu ko sẽ ảnh hưởng tới băng thông bị giới hạn.

Kiểm tra port muốn áp QoS policy
```sh
openstack port list
```

Áp QoS cho port
```sh
openstack port set --qos-policy bw-limiter [ID_PORT]
```

Bỏ QoS áp cho port
```sh
openstack port unset --no-qos-policy [ID_PORT]
```

Hoặc tạo một port và gán QoS policy mặc định ngay
```sh
openstack port create --qos-policy bw-limiter --network private port1
```

Có thể gán QoS cho cả một network bằng lệnh
```sh
openstack network set --qos-policy bw-limiter private
```

*NOTE*: nên loại bỏ các port gán cho DHCP hoặc router khỏi QoS khi áp dụng cho cả network

Có thể thiết lập QoS default cho mọi network, kể cả tạo mới, bằng lệnh
```sh
openstack network qos policy create --default bw-limiter
```

Bỏ QoS default bằng lệnh
```sh
openstack network qos policy set --no-default bw-limiter
```

# Tham khảo

- [https://docs.openstack.org/neutron/pike/admin/config-qos.html](https://docs.openstack.org/neutron/pike/admin/config-qos.html)