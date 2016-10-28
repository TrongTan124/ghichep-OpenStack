<h1>Thiết lập cấu hình cần thiết</h1>
<ul>Dựa theo file script <i>ctl-1-ipadd.sh</i></ul>
<h3>Thiết lập cấu hình IP cho Controller</h3>
<ul>Backup lại file cấu hình interface
	<p><i>cp /etc/network/interfaces /etc/network/interfaces.bk</i></p>
</ul>
<ul>Sửa file cấu hình interface
	<li><i>vi /etc/network/interfaces</i></li>
<pre>
```
# LOOPBACK NET
auto lo
iface lo inet loopback

# MGNT NETWORK
auto eth0
iface eth0 inet static
address 10.10.10.80
netmask 255.255.255.0

# EXT NETWORK
auto eth1
iface eth1 inet static
address 172.16.69.80
netmask 255.255.255.0
gateway 172.16.69.1
dns-nameservers 8.8.8.8 8.8.4.4
```
</pre>
</ul>
<ul>Sửa lại file hostname
	<li><i>vi /etc/hostname</i></li>
	<li>Sửa thành dòng sau: <i>controller</i></li>
</ul>
<ul>Sửa lại file hosts
	<li>Backup lại cấu hình: <i>cp /etc/hosts /etc/hosts.bk</i></li>
	<li><i>vi /etc/hosts</i></li>
	<li>Sửa thành dòng sau: <i>controller</i></li>
</ul>



<h3></h3>






<h3></h3>



<h1></h1>









<h1></h1>
	<ul></ul>
	<ul></ul>
	<ul></ul>











<h1>Tham khảo</h1>
<ul></ul>
<ul></ul>
<ul></ul>
<ul></ul>


