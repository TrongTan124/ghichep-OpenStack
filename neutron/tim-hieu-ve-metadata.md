# 1. Giới thiệu

Hôm nay chúng ta sẽ tìm hiểu về cách instance lấy các thông tin metadata khi được khởi động như thế nào.

Thông thường, bạn sẽ ít khi để ý đến workflow này, nhưng nó lại rất quan trọng. Bước lấy thông tin metadata chỉ xảy ra trong khoảng thời gian rất ngắn lúc instance khởi động. 
Ngay sau khi gửi bản tin dhcp discover để lấy IP thì sẽ tới bước này. 

# 2. Metadata service với DHCP namespace

Openstack có một tùy chọn cho phép chạy metadata proxy service bên trong DHCP namespace, khi đó metadata service làm việc một cách tách biệt với mỗi tenant network. không cần phải 
gắn tenant network tới neutron router. Để kích hoạt tính năng này, chúng ta cần cấu hình trong `/etc/neutron/dhcp_agent.ini` tham số sau
```sh
enable_isolated_metadata = True
```

Bây giờ ta sẽ tạo một network để xem cách hoạt động của metadata service khi mà subnet không có router port. Tạo một network không có gateway
```sh
neutron net-create test-metadata-in-dhcp
neutron subnet-create --no-gateway --name test-metadata-in-dhcp test-metadata-in-dhcp 192.168.111.0/24
```

Bạn sẽ thấy có một dhcp namespace mới được tạo, trong đó có IP `169.254.169.254` được cấu hình
```sh
root@controller1:~# ip netns
qdhcp-09da3932-f6e3-4806-abca-2b42f9c23845

root@controller1:~# ip netns exec qdhcp-09da3932-f6e3-4806-abca-2b42f9c23845 ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default 
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
16: tape25858ae-01: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1458 qdisc noqueue state UNKNOWN group default 
    inet 192.168.111.1/24 brd 192.168.111.255 scope global tape25858ae-01
       valid_lft forever preferred_lft forever
    inet 169.254.169.254/16 brd 169.254.255.255 scope global tape25858ae-01
       valid_lft forever preferred_lft forever
```

Có một metadata-proxy chạy trong namespace này
```sh
root@controller1:~# ps -ef | grep meta | grep 09da3932-f6e3-4806-abca-2b42f9c23845 | fold -s -w 100
neutron   13277      1  0 14:35 ?        00:00:00 /usr/bin/python 
/usr/bin/neutron-ns-metadata-proxy 
--pid_file=/var/lib/neutron/external/pids/09da3932-f6e3-4806-abca-2b42f9c23845.pid 
--metadata_proxy_socket=/var/lib/neutron/metadata_proxy 
--network_id=09da3932-f6e3-4806-abca-2b42f9c23845 --state_path=/var/lib/neutron --metadata_port=80 
--metadata_proxy_user=112 --metadata_proxy_group=119 --verbose 
--log-file=neutron-ns-metadata-proxy-09da3932-f6e3-4806-abca-2b42f9c23845.log 
--log-dir=/var/log/neutron
```

Metadata-proxy dc listen ở port 80
```sh
root@controller1:~# ip netns exec qdhcp-09da3932-f6e3-4806-abca-2b42f9c23845 netstat -auntlp | grep 13277
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      13277/python
```

Kiểm tra static route của dhcp server, chúng ta sẽ thấy có một static route 169.254.169.254 đặt tại đây. VM instance nhận static route thông qua dhcp client, do đó metadata request 
được định tuyến tới DHCP namespace, metadata-proxy chạy trong đó sẽ xử lý các request này.
```sh
root@controller1:~# ps -ef | grep dnsmasq | grep 09da3932-f6e3-4806-abca-2b42f9c23845 |fold -s -w 100
nobody    13241      1  0 14:35 ?        00:00:00 dnsmasq --no-hosts --no-resolv --strict-order 
--except-interface=lo --pid-file=/var/lib/neutron/dhcp/09da3932-f6e3-4806-abca-2b42f9c23845/pid 
--dhcp-hostsfile=/var/lib/neutron/dhcp/09da3932-f6e3-4806-abca-2b42f9c23845/host 
--addn-hosts=/var/lib/neutron/dhcp/09da3932-f6e3-4806-abca-2b42f9c23845/addn_hosts 
--dhcp-optsfile=/var/lib/neutron/dhcp/09da3932-f6e3-4806-abca-2b42f9c23845/opts 
--dhcp-leasefile=/var/lib/neutron/dhcp/09da3932-f6e3-4806-abca-2b42f9c23845/leases 
--dhcp-match=set:ipxe,175 --bind-interfaces --interface=tape25858ae-01 
--dhcp-range=set:tag0,192.168.111.0,static,86400s --dhcp-option-force=option:mtu,1458 
--dhcp-lease-max=256 --conf-file=/etc/neutron/dnsmasq-neutron.conf --domain=openstacklocal

root@controller1:~# cat /var/lib/neutron/dhcp/09da3932-f6e3-4806-abca-2b42f9c23845/opts
tag:tag0,option:classless-static-route,169.254.169.254/32,192.168.111.1
tag:tag0,249,169.254.169.254/32,192.168.111.1
tag:tag0,option:router
```

# 3. Metadata service với router namespace

Trong phần trên, chúng ta thử nghiệm với network không sử dụng gateway trong subnet. Vậy chuyện gì sẽ xảy ra nếu ta khai báo một network có gateway. 
Tạo một network với gateway mặc định
```sh
neutron net-create test-metadata-in-dhcp-with-gw
neutron subnet-create --name test-metadata-in-dhcp-with-gw test-metadata-in-dhcp-with-gw 172.31.0.0/24
```

Kiểm tra DHCP của namespace vừa tạo trên
```sh
root@controller1:~# ip netns
qdhcp-47b2f56b-0698-4eee-bd10-6a61e35e90d2

root@controller1:~# ip netns exec qdhcp-47b2f56b-0698-4eee-bd10-6a61e35e90d2 ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default 
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
17: tap90661a98-7a: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1458 qdisc noqueue state UNKNOWN group default 
    inet 172.31.0.2/24 brd 172.31.0.255 scope global tap90661a98-7a
       valid_lft forever preferred_lft forever
    inet 169.254.169.254/16 brd 169.254.255.255 scope global tap90661a98-7a
       valid_lft forever preferred_lft forever
```

Tại sao vẫn thấy địa chỉ IP 169.254.169.254 được cấu hình, chuyện gì xảy ra với metadata-proxy service?
```sh
root@controller1:~# ps -ef | grep meta |grep 47b2f56b-0698-4eee-bd10-6a61e35e90d2 | fold -s -w 100
neutron   16719      1  0 14:51 ?        00:00:00 /usr/bin/python 
/usr/bin/neutron-ns-metadata-proxy 
--pid_file=/var/lib/neutron/external/pids/47b2f56b-0698-4eee-bd10-6a61e35e90d2.pid 
--metadata_proxy_socket=/var/lib/neutron/metadata_proxy 
--network_id=47b2f56b-0698-4eee-bd10-6a61e35e90d2 --state_path=/var/lib/neutron --metadata_port=80 
--metadata_proxy_user=112 --metadata_proxy_group=119 --verbose 
--log-file=neutron-ns-metadata-proxy-47b2f56b-0698-4eee-bd10-6a61e35e90d2.log 
--log-dir=/var/log/neutron

root@controller1:~# ip netns exec qdhcp-47b2f56b-0698-4eee-bd10-6a61e35e90d2 netstat -anptlu |grep 16719
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      16719/python
```

Metadata-proxy vẫn đang chạy, ta kiểm tra các tham số khác của DHCP namespace
```sh
root@controller1:~# ps -ef | grep dnsmasq | grep 47b2f56b-0698-4eee-bd10-6a61e35e90d2 |fold -s -w 100
nobody    16686      1  0 14:51 ?        00:00:00 dnsmasq --no-hosts --no-resolv --strict-order 
--except-interface=lo --pid-file=/var/lib/neutron/dhcp/47b2f56b-0698-4eee-bd10-6a61e35e90d2/pid 
--dhcp-hostsfile=/var/lib/neutron/dhcp/47b2f56b-0698-4eee-bd10-6a61e35e90d2/host 
--addn-hosts=/var/lib/neutron/dhcp/47b2f56b-0698-4eee-bd10-6a61e35e90d2/addn_hosts 
--dhcp-optsfile=/var/lib/neutron/dhcp/47b2f56b-0698-4eee-bd10-6a61e35e90d2/opts 
--dhcp-leasefile=/var/lib/neutron/dhcp/47b2f56b-0698-4eee-bd10-6a61e35e90d2/leases 
--dhcp-match=set:ipxe,175 --bind-interfaces --interface=tap90661a98-7a 
--dhcp-range=set:tag0,172.31.0.0,static,86400s --dhcp-option-force=option:mtu,1458 
--dhcp-lease-max=256 --conf-file=/etc/neutron/dnsmasq-neutron.conf --domain=openstacklocal

root@controller1:~# cat /var/lib/neutron/dhcp/47b2f56b-0698-4eee-bd10-6a61e35e90d2/opts
tag:tag0,option:classless-static-route,169.254.169.254/32,172.31.0.2,0.0.0.0/0,172.31.0.1
tag:tag0,249,169.254.169.254/32,172.31.0.2,0.0.0.0/0,172.31.0.1
tag:tag0,option:router,172.31.0.1
```

Bạn có thấy mọi thứ giống hệt trường hợp không khai báo gateway không? Bây giờ ta sẽ gán vào network một router để xem mọi thứ có thay đổi hay không
```sh
neutron router-create testrouter
neutron router-interface-add testrouter test-metadata-in-dhcp-with-gw
```

Xem lại static route ở DHCP namespace sau khi tạo router
```sh
root@controller1:~# cat /var/lib/neutron/dhcp/47b2f56b-0698-4eee-bd10-6a61e35e90d2/opts
tag:tag0,option:classless-static-route,169.254.169.254/32,172.31.0.2,0.0.0.0/0,172.31.0.1
tag:tag0,249,169.254.169.254/32,172.31.0.2,0.0.0.0/0,172.31.0.1
tag:tag0,option:router,172.31.0.1
```

Static route 169.254.169.254 vẫn tồn tại. giờ ta kiểm tra router namespace
```sh
root@controller1:~# ip netns
qrouter-4f9e3fb4-b7f1-4f34-bfea-bfcf0440d05c

root@controller1:~# ip netns exec qrouter-4f9e3fb4-b7f1-4f34-bfea-bfcf0440d05c iptables-save | grep REDIRECT
-A neutron-l3-agent-PREROUTING -d 169.254.169.254/32 -i qr-+ -p tcp -m tcp --dport 80 -j REDIRECT --to-ports 9697
```

Trong router namespace cũng thấy metadata-proxy đang chạy. vậy nghĩa là cả router và dhcp namespace đều có thể xử lý metadata request.

Ta khởi động lại `neutron-dhcp-agent` trên controller
```sh
root@controller1:~# /etc/init.d/neutron-dhcp-agent restart 
neutron-dhcp-agent stop/waiting
neutron-dhcp-agent start/running, process 19446
```

Kiểm tra lại static route trong dhcp
```sh
root@controller1:~# cat /var/lib/neutron/dhcp/47b2f56b-0698-4eee-bd10-6a61e35e90d2/opts
tag:tag0,option:classless-static-route,169.254.169.254/32,172.31.0.1,0.0.0.0/0,172.31.0.1
tag:tag0,249,169.254.169.254/32,172.31.0.1,0.0.0.0/0,172.31.0.1
tag:tag0,option:router,172.31.0.1
```

Ta thấy 169.254.169.254 đã được route sang địa chỉ gateway `169.254.169.254/32,172.31.0.1`. Nhưng địa chỉ IP vẫn được gán trong DHCP namespace và metadata-proxy vẫn đang chạy.
```sh
root@controller1:~/scripts# ip netns exec qdhcp-47b2f56b-0698-4eee-bd10-6a61e35e90d2 ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default 
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
17: tap90661a98-7a: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1458 qdisc noqueue state UNKNOWN group default 
    inet 172.31.0.2/24 brd 172.31.0.255 scope global tap90661a98-7a
       valid_lft forever preferred_lft forever
    inet 169.254.169.254/16 brd 169.254.255.255 scope global tap90661a98-7a
       valid_lft forever preferred_lft forever
	   
root@controller1:~/scripts# ps -ef | grep metadata-proxy | grep 47b2f56b-0698-4eee-bd10-6a61e35e90d2 |fold -s -w 100
neutron   16719      1  0 14:51 ?        00:00:00 /usr/bin/python 
/usr/bin/neutron-ns-metadata-proxy 
--pid_file=/var/lib/neutron/external/pids/47b2f56b-0698-4eee-bd10-6a61e35e90d2.pid 
--metadata_proxy_socket=/var/lib/neutron/metadata_proxy 
--network_id=47b2f56b-0698-4eee-bd10-6a61e35e90d2 --state_path=/var/lib/neutron --metadata_port=80 
--metadata_proxy_user=112 --metadata_proxy_group=119 --verbose 
--log-file=neutron-ns-metadata-proxy-47b2f56b-0698-4eee-bd10-6a61e35e90d2.log 
--log-dir=/var/log/neutron
```

Ta thấy các hành vi trên có vẻ kỳ lạ, tuy nhiên cả router và dhcp namespace đều có metadata-proxy chạy bên trong đó để xử lý metadata request, nhưng 169.254.169.254 được định 
tuyến tới dhcp server trước khi restart `neutron-dhcp-agent`. Còn sau khi restart, 169.254.169.254 được route tới IP gateway.

# Tham khảo
- [http://kimizhang.com/metadata-service-in-dhcp-namespace/](http://kimizhang.com/metadata-service-in-dhcp-namespace/)
