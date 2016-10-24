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
		<p>	cat << EOF >openvswitch.sh
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
		</p>
	</li>
	<li> root@ubuntu:~/openvswitch# chmod 755 openvswitch.sh </li>
	<li>root@ubuntu:~/openvswitch# ./openvswitch.sh </li>
</ul>
<ul> </ul>
<ul> </ul>


## Tham khảo
<ul>http://dannykim.me/danny/openflow/57620?ckattempt=2 </ul>
<ul> </ul>
<ul> </ul>