<h1>1. SDN là gì</h1>
<img src="https://vietstack.files.wordpress.com/2015/03/sdn-3layers.gif?w=435&h=284">
<ul>Mô hình 3 lớp của SDN</ul>
<ul>
	<p>SDN (Software-defined Networking): SDN là một cấu trúc mới, được thiết kế cho phép hệ thống mạng trở nên linh động và có hiệu quả chi phí hơn. SDN là một khái niệm mang tính lý thuyết, 
	về mặt bản chất, SDN tách riêng các control plane phân tán (distributed) từ các forwarding plane và đưa (offload) các chức năng của control plane vào trong control plane tập trung 
	(centralized). Control plane và forwarding plane là 2 dạng tiến trình mà các thiết bị mạng đều thực hiện. Ví dụ, tại cấu trúc mạng truyền thống, nếu ta truy cập vào router, thực hiện các tác
	 vụ như cấu hình các giao thức gateway,... thì các hoạt động này đều được thực hiện trên cùng một thiết bị (trên control plane và forwarding plane của router), do đó các nút (node) 
	 trong network hoạt động một cách độc lập dựa trên các cấu hình nội bộ tại chính các nút đó. Điều đó có nghĩa, cho dù cấu trúc mạng có linh động, hiệu quả như thế nào thì kết quả của các 
	 tác vụ hoạt động trong mạng phải phụ thuộc vào cấu hình của từng nút. Nếu như số lượng nút nhiều (1000, 10000 nodes) thì đồng nghĩa các nhà vận hành mạng phải quản lý toàn bộ
	  (1000, 10000) control plane (process quản lý hầu như toàn bộ hoạt động của thiết bị mạng)</p>

	<p>
	Chính vì những khó khăn trên, SDN ra đời nhắm mục đích "chuyển" cấu trúc control plane từ phân tán sang tập trung. Control plane tập trung (SDN controller) cho phép chuyển tiếp các quyết
	 định về flow thông qua SDN domain thay vì phải qua từng hop
	</p>
	<p>
	Các tập đoàn lớn như Cisco, HN, IBM, VMware đều tự phát triển các SDN controller riêng của họ. trong SDN, controller được ví như "bộ não", nó thống kê tất cả thông tin từ các 
	flow switch ở trong mạng, cung cấp cái nhìn tổng thể bên trong của network (trong mạng truyền thống, một switch đơn lẻ sẽ không thể nhận biết toàn cảnh network, sự kết nối giữa 
	các switch với nhau). SDN controller có open northbound (programmatic) và southbound (implementation). SDN controller có thể chạy trên máy ảo được host trên x86 server 
	hoặc có thể chạy trên bare metal (x86 hoặc một thiết bị nào đó).
	</p>
</ul>
<img src="http://www.cisco.com/c/dam/en_us/about/ac123/ac147/images/ipj/ipj_16-1/161_sdn_fig01_lg.jpg">
<ul>Các đặc tính của SDN:
	<li>Traffic Engineering</li>
	<li>Security</li>
	<li>QoS</li>
	<li>Routing</li>
	<li>Switching</li>
	<li>Virtualization</li>
	<li>Monitoring</li>
	<li>Load Balancing</li>
</ul>
<ul></ul>
<ul></ul>
<ul></ul>

<h1>2. OpenFlow là gì?</h1>
	<ul>
	<p>Giao thức OpenFlow là một chuẩn giao thức quy định hành vi tương tác của switch từ nhiều vendor</p>
	
	<p><i>The OpenFlow protocol is a standardized protocol for interacting with the forwarding behaviors of switches from multiple vendors.
		This provides us a way to control the behavior of switches throughout our network dynamically and programmatically.
		OpenFlow is a key protocol in many SDN solutions. Much more detail on OpenFlow and SDN can be found at <a href="https://www.youtube.com/watch?v=l25Ukkmk6Sk">Link here</a>
	</i></p>
	</ul>
	<h2>Dựng thử nghiệm bằng mininet</h2>
		<img src="http://image.prntscr.com/image/5e699df97a12413d987869fa50b6276e.png">
		<ul> Command tạo topo
			<li>root@mininet-vm:~# mn --topo=single,4</li>
			<li>mininet> dump
			<pre>
mininet> dump
<Host h1: h1-eth0:10.0.0.1 pid=2613> 
<Host h2: h2-eth0:10.0.0.2 pid=2616> 
<Host h3: h3-eth0:10.0.0.3 pid=2618> 
<Host h4: h4-eth0:10.0.0.4 pid=2620> 
<OVSSwitch s1: lo:127.0.0.1,s1-eth1:None,s1-eth2:None,s1-eth3:None,s1-eth4:None pid=2625> 
<Controller c0: 127.0.0.1:6633 pid=2606> 
mininet>
			</pre>
			</li>
			<li>mininet> h4 python -m SimpleHTTPServer 80 &</li>
			<li>mininet> h1 wget 10.0.0.4</li>
			<img src="http://image.prntscr.com/image/20da748867dc4b548db06dd3584fea8a.png">
			<img src="http://image.prntscr.com/image/4e3222e76c15441299da2fa14cf7bdaf.png">
			<img src="http://image.prntscr.com/image/865ca0d5539d416c82c64c2aa27137a6.png">
		</ul>
		<ul></ul>
		<ul></ul>

<h1></h1>

<h1>Tham khảo</h1>
<ul>https://vietstack.wordpress.com/2015/03/25/tong-quan-ve-sdn-va-nfv/</ul>
<ul>https://www.academia.edu/13497161/T%C3%ACm_hi%E1%BB%83u_c%C3%A1c_v%E1%BA%A5n_%C4%91%E1%BB%81_an_to%C3%A0n_trong_SDN</ul>



