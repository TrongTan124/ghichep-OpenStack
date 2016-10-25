##1. Khái niệm
<ul></ul>
<ul></ul>
<ul></ul>
<ul></ul>
<ul></ul>

##2. Đặc điểm

##3. Cài đặt
<ul> Thực hiện các lệnh sau
	<li>root@ubuntu:~# apt-get update </li>
	<li>root@ubuntu:~# apt-get install -y git python-simplejson python-qt4 python-twisted-conch automake autoconf gcc uml-utilities libtool build-essential git pkg-config linux-headers-`uname -r` </li>
	<li>root@ubuntu:~# wget http://openvswitch.org/releases/openvswitch-2.5.0.tar.gz </li>
	<li>root@ubuntu:~# tar -xzvf openvswitch-2.5.0.tar.gz </li>
	<li>root@ubuntu:~# mv openvswitch-2.5.0 openvswitch </li>
	<li>root@ubuntu:~# cd openvswitch </li>
	<li>root@ubuntu:~/openvswitch# ./boot.sh </li>
	<li>root@ubuntu:~/openvswitch# ./configure --with-linux=/lib/modules/`uname -r`/build </li>
	<li>root@ubuntu:~/openvswitch# make && make install </li>
	<li>root@ubuntu:~/openvswitch# cd datapath/linux </li>
	<li>root@ubuntu:~/openvswitch/datapath/linux# modprobe openvswitch </li>
	<li>root@ubuntu:~/openvswitch/datapath/linux# lsmod | grep openvswitch
		<p>	openvswitch            86016  0
			libcrc32c              16384  1 openvswitch</p>
	</li>
	<li>root@ubuntu:~/openvswitch/datapath/linux# touch /usr/local/etc/ovs-vswitchd.conf </li>
	<li>root@ubuntu:~/openvswitch/datapath/linux# mkdir -p /usr/local/etc/openvswitch </li>
	<li>root@ubuntu:~/openvswitch/datapath/linux# cd ../.. </li>
	<li>root@ubuntu:~/openvswitch# ovsdb-tool create /usr/local/etc/openvswitch/conf.db  vswitchd/vswitch.ovsschema </li>
	<li>
		<pre>	
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
		</pre>
	</li>
	<li> root@ubuntu:~/openvswitch# chmod 755 openvswitch.sh </li>
	<li>root@ubuntu:~/openvswitch# ./openvswitch.sh </li>
</ul>
<ul> </ul>
<ul> </ul>

##4. Cấu hình OVS
###a. OVS + KVM-QEMU
<ul> Thực hiện cấu hình switch:
	<li>Add thêm một switch: <i>root@ubuntu:~/openvswitch# ovs-vsctl add-br br-int</i> </li>
	<li>Sau khi thực hiện lệnh gán port cho switch, thì sẽ ko thể control được port vừa gán. Vì vậy cần cần thận chọn đúng port.</li>
	<li>Gán port của host (vật lý) vào switch vừa tạo: <i>root@ubuntu:~# ovs-vsctl add-port br-int eth0</i> </li>
	<li>Clear IP cho port của host sẽ gán vào switch vừa tạo: <i>ifconfig eth0 0</i></li>
	<li>Gán IP để truy nhập vào host qua switch: <i>ifconfig br-int 172.16.69.110 netmask 255.255.255.0</i></li>
	<li>Thực hiện add route default: <i>route add default gw 172.16.69.1</i></li>
	<li>Kiểm tra kết nối ra internet: <i>ping 8.8.8.8</i></li>
	<li>Thêm DNS cho host: vi /etc/resolv.conf
		<p>Comment các dòng cũ và Thêm dòng sau: nameserver 8.8.8.8 8.8.4.4</p>
	</li>
	<li>Show switch vừa tạo: <i>root@ubuntu:~# ovs-vsctl show</i> 
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
	</li>
</ul>
<ul> Cài đặt KVM
	<li>Chạy lệnh sau để thực hiện cài đặt KVM: <i>root@ubuntu:~# apt-get install kvm -y</i></li>
	<li>Thực hiện tạo 02 scritp để add và xóa port trong switch:
		<ul>
			<li>Script add port vào switch: <i>vi /etc/ovs-ifup</i>
				<pre>
					#!/bin/sh
					switch='br-int'
					/sbin/ifconfig $1 0.0.0.0 up
					ovs-vsctl add-port ${switch} $1
				</pre>
			</li>
			<li>Script xóa port trên switch: <i>vi /etc/ovs-ifdown</i>
				<pre>
					#!/bin/sh
					switch='br-int'
					/sbin/ifconfig $1 0.0.0.0 down
					ovs-vsctl del-port ${switch} $1
				</pre>
			</li>
			<li>Phân quyền để script có thể thực thi: <i>chmod +x /etc/ovs-ifup /etc/ovs-ifdown</i></li>
		</ul>
	</li>
</ul>
<ul> Thực hiện tạo KVM VM với cirror image và gán vào ovs bridge "br-int"
	<li>Lệnh tạo máy ảo 1: <i>kvm -m 512 -net nic,macaddr=00:00:00:00:cc:10 -net tap,script=/etc/ovs-ifup,downscript=/etc/ovs-ifdown -nographic /home/tannt/cirros-0.3.4-x86_64-disk.img</i></li>
	<li>Lệnh tạo máy ảo 2: <i>kvm -m 512 -net nic,macaddr=00:11:22:CC:CC:10 -net tap,script=/etc/ovs-ifup,downscript=/etc/ovs-ifdown -nographic /home/tannt/cirros-0.3.4-x86_64-disk.img</i></li>
	<li>Lệnh tạo máy ảo 3: <i>kvm -m 512 -net nic,macaddr=22:22:22:00:cc:10 -net tap,script=/etc/ovs-ifup,downscript=/etc/ovs-ifdown -nographic /home/tannt/cirros-0.3.4-x86_64-disk.img</i></li>
</ul>
<ul> </ul>
<ul> </ul>
<ul> </ul>

## Tham khảo
<ul>http://dannykim.me/danny/openflow/57620?ckattempt=2 </ul>
<ul>https://www.youtube.com/watch?v=Gud2GoI-W_w&index=3&list=PLUUhQ61ndlZVdwm3_gAyoTcmIVlm_yc2h </ul>
<ul> </ul>