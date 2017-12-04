##1. Khái niệm


##2. Đặc điểm

##3. Cài đặt
- Thực hiện cài đặt từ source
```sh
root@ubuntu:~# apt-get update 
root@ubuntu:~# apt-get install -y git python-simplejson python-qt4 python-twisted-conch automake autoconf gcc uml-utilities libtool build-essential git pkg-config linux-headers-`uname -r` 
root@ubuntu:~# wget http://openvswitch.org/releases/openvswitch-2.5.0.tar.gz 
root@ubuntu:~# tar -xzvf openvswitch-2.5.0.tar.gz 
root@ubuntu:~# mv openvswitch-2.5.0 openvswitch 
root@ubuntu:~# cd openvswitch 
root@ubuntu:~/openvswitch# ./boot.sh 
root@ubuntu:~/openvswitch# ./configure --with-linux=/lib/modules/`uname -r`/build 
root@ubuntu:~/openvswitch# make && make install 
root@ubuntu:~/openvswitch# cd datapath/linux 
root@ubuntu:~/openvswitch/datapath/linux# modprobe openvswitch 
root@ubuntu:~/openvswitch/datapath/linux# lsmod | grep openvswitch
			openvswitch            86016  0
			libcrc32c              16384  1 openvswitch
	
root@ubuntu:~/openvswitch/datapath/linux# touch /usr/local/etc/ovs-vswitchd.conf 
root@ubuntu:~/openvswitch/datapath/linux# mkdir -p /usr/local/etc/openvswitch 
root@ubuntu:~/openvswitch/datapath/linux# cd ../.. 
root@ubuntu:~/openvswitch# ovsdb-tool create /usr/local/etc/openvswitch/conf.db  vswitchd/vswitch.ovsschema 


cat << EOF >openvswitch.sh
ovsdb-server /usr/local/etc/openvswitch/conf.db \
--remote=punix:/usr/local/var/run/openvswitch/db.sock \
--remote=db:Open_vSwitch,Open_vSwitch,manager_options \
--private-key=db:Open_vSwitch,SSL,private_key \
--certificate=db:Open_vSwitch,SSL,certificate \
--bootstrap-ca-cert=db:Open_vSwitch,SSL,ca_cert --pidfile --detach --log-file

ovs-vsctl --no-wait init
ovs-vswitchd --pidfile --detach
ovs-vsctl show
EOF

	
root@ubuntu:~/openvswitch# chmod 755 openvswitch.sh 
root@ubuntu:~/openvswitch# ./openvswitch.sh 
```

##4. Cấu hình OVS
###a. OVS + KVM-QEMU
<ul> Thực hiện cấu hình switch:
Add thêm một switch: <i>root@ubuntu:~/openvswitch# ovs-vsctl add-br br-int</i> 
Sau khi thực hiện lệnh gán port cho switch, thì sẽ ko thể control được port vừa gán. Vì vậy cần cần thận chọn đúng port.
Gán port của host (vật lý) vào switch vừa tạo: <i>root@ubuntu:~# ovs-vsctl add-port br-int eth0</i> 
Clear IP cho port của host sẽ gán vào switch vừa tạo: <i>ifconfig eth0 0</i>
Gán IP để truy nhập vào host qua switch: <i>ifconfig br-int 172.16.69.110 netmask 255.255.255.0 up</i>
Thực hiện add route default: <i>route add default gw 172.16.69.1</i>
Kiểm tra kết nối ra internet: <i>ping 8.8.8.8</i>
Thêm DNS cho host: vi /etc/resolv.conf
		<p>Comment các dòng cũ và Thêm dòng sau: nameserver 8.8.8.8 8.8.4.4</p>
	
Show switch vừa tạo: <i>root@ubuntu:~# ovs-vsctl show</i> 
		<pre>
			ff424c58-2e9a-44c8-b2c4-ab81c556dd50
			Bridge br-int
				Port br-int
					Interface br-int
						type: internal
				Port "eth2"
					Interface "eth2"
			ovs_version: "2.5.0"
		</pre>
	
</ul>
<ul> Cài đặt KVM
Chạy lệnh sau để thực hiện cài đặt KVM: <i>root@ubuntu:~# apt-get install kvm -y</i>
Thực hiện tạo 02 scritp để add và xóa port trong switch:
		<ul>
		Script add port vào switch: <i>vi /etc/ovs-ifup</i>
				<pre>
#!/bin/sh
switch='br-int'
/sbin/ifconfig $1 0.0.0.0 up
ovs-vsctl add-port ${switch} $1
				</pre>
			
		Script xóa port trên switch: <i>vi /etc/ovs-ifdown</i>
				<pre>
#!/bin/sh
switch='br-int'
/sbin/ifconfig $1 0.0.0.0 down
ovs-vsctl del-port ${switch} $1
				</pre>
			
		Phân quyền để script có thể thực thi: <i>chmod +x /etc/ovs-ifup /etc/ovs-ifdown</i>
		</ul>
	
</ul>
<ul> Thực hiện tạo KVM VM với cirror image và gán vào ovs bridge "br-int"
Lệnh tạo máy ảo 1: <i>kvm -m 512 -net nic,macaddr=00:00:00:00:cc:10 -net tap,script=/etc/ovs-ifup,downscript=/etc/ovs-ifdown -nographic /home/tannt/cirros-0.3.4-x86_64-disk.img</i>
Lệnh tạo máy ảo 2: <i>kvm -m 512 -net nic,macaddr=00:11:22:CC:CC:10 -net tap,script=/etc/ovs-ifup,downscript=/etc/ovs-ifdown -nographic /home/tannt/cirros-0.3.4-x86_64-disk.img</i>
Lệnh tạo máy ảo 3: <i>kvm -m 512 -net nic,macaddr=22:22:22:00:cc:10 -net tap,script=/etc/ovs-ifup,downscript=/etc/ovs-ifdown -nographic /home/tannt/cirros-0.3.4-x86_64-disk.img</i>
</ul>

##5. Triển khai GRE cho OVS
<ul> Mô hình triển khai
	<img src="http://openvswitch.github.io/support/config-cookbooks/port-tunneling/2host-4vm-2nic-tunnel.png">
</ul>
<ul> 
Tạo OVS bridge ovsbr0 <i>ovs-vsctl add-br ovsbr0</i>
		<ul>Chú ý chỉ gán port e</ul>
	



</ul>
<ul> </ul>

##6. Triển khai Controller SDN cho OVS
<ul>Cài đặt Floodlight openflow controller và gán vào openswitch 
Cài đặt các gói phụ thuộc: <i>apt-get install build-essential default-jdk ant python-dev eclipse git</i>
Update lên java 1.8: 
		<ul> 
		apt-add-repository ppa:webupd8team/java
		apt-get update
		apt-get install oracle-java8-installer
		java -version
		</ul>
	
Kéo floodlight từ git về: <i>git clone git://github.com/floodlight/floodlight.git</i>
Build source thành file jar: <i>ant</i>
Chạy file java vừa build được: <i>java -jar target/floodlight.jar</i>
</ul>
<ul> Thực hiện gán openvswitch vào controller
ovs-vsctl set-controller br-int tcp:172.16.69.72:6633
ovs-vsctl set-controller ovsbr0 tcp:172.16.69.72:6633
ovs-vsctl set-controller br-gre tcp:172.16.69.72:6633
ovs-vsctl show
</ul>

## Tham khảo
<ul>http://dannykim.me/danny/openflow/57620?ckattempt=2 </ul>
<ul>https://www.youtube.com/watch?v=Gud2GoI-W_w&index=3&list=PLUUhQ61ndlZVdwm3_gAyoTcmIVlm_yc2h </ul>
<ul>http://networkstatic.net/openflow-openvswitch-lab/ </ul>
<ul>http://networkstatic.net/open-vswitch-gre-tunnel-configuration/ </ul>
<ul>https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Virtualization_Administration_Guide/ch19s05.html </ul>
<ul> </ul>
<ul> </ul>