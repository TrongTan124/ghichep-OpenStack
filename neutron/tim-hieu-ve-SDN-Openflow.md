﻿<h1>1. SDN là gì</h1>
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
<img src="http://image.prntscr.com/image/92e5190d5c2140b2a3fc9701a2996a27.png">
<ul><p>Kiến trúc SDN</p>
	<ul> Theo Open Networking Foundation, kiến trúc của SDN bao gồm bao lớp tách biệt truy cập thông qua các APIs mở:
		<li><b>Lớp ứng dụng</b>: bao gồm các ứng dụng của người dùng cuối sử dụng các dịch vụ truyền thông qua SDN. Ranh giới giao tiếp giữa lớp ứng dụng và lớp điều khiển được thực hiện bởi northbound API</li>
		<li><b>Lớp điều khiển (SDN control plance)</b>: cung cấp chức năng quản lý tập trung làm nhiệm vụ quản lý việc chuyển tiếp trong mạng thông các các open interfaces. Các chức năng này bao gồm: định tuyến, khai báo tên, chính sách và thực hiện kiểm tra vấn đề bảo mật</li>
		<li><b>Lớp cơ sở hạ tầng (SDN data plane)</b>: bao gồm các phần tử và thiết bị mạng cung cấp chức năng chuyển mạch và forward các gói tin</li>
	</ul>
</ul>
<ul> Theo mô hình này, một kiến trúc SDN được đặc trưng bởi 3 thuộc tính chính
	<li><b>Logically centralized intelligence</b>: Trong kiến trúc SDN, network control được phân chia từ forwarding sử dụng giao diện chuẩn southbound: OpenFlow</li>
	<li><b>Programmability</b>: Mạng SDN vốn được điều khiển bởi chức năng phần mềm, được cung cấp bởi vendor hoặc bộ khiển của của chính nó. </li>
	<li><b>Abstraction</b>: Trọng mạng SDN, các ứng dụng sử dụng dịch vụ SDN được trừu tượng hóa với các công nghệ mạng phía dưới</li>
</ul>
<ul></ul>

<h1>2. OpenFlow là gì?</h1>
<ul>
	Giao thức OpenFlow là một chuẩn giao thức quy định hành vi tương tác của switch từ nhiều vendor

	<p><i>The OpenFlow protocol is a standardized protocol for interacting with the forwarding behaviors of switches from multiple vendors.
		This provides us a way to control the behavior of switches throughout our network dynamically and programmatically.
		OpenFlow is a key protocol in many SDN solutions. Much more detail on OpenFlow and SDN can be found at <a href="https://www.youtube.com/watch?v=l25Ukkmk6Sk">Link here</a>
	</i></p>
</ul>
<h2>Dựng thử nghiệm bằng mininet</h2>
<img src="http://image.prntscr.com/image/5e699df97a12413d987869fa50b6276e.png">
<ul> Command tạo topo
	<li>mn --topo=single,4</li>
	<li>dump</li>
	<li> h4 python -m SimpleHTTPServer 80 & </li>
	<li> h1 wget 10.0.0.4</li>
	<img src="http://image.prntscr.com/image/20da748867dc4b548db06dd3584fea8a.png">
	<img src="http://image.prntscr.com/image/4e3222e76c15441299da2fa14cf7bdaf.png">
	<img src="http://image.prntscr.com/image/865ca0d5539d416c82c64c2aa27137a6.png">
</ul>

<h1>Tham khảo</h1>
<ul>https://vietstack.wordpress.com/2015/03/25/tong-quan-ve-sdn-va-nfv/</ul>
<ul>https://www.academia.edu/13497161/T%C3%ACm_hi%E1%BB%83u_c%C3%A1c_v%E1%BA%A5n_%C4%91%E1%BB%81_an_to%C3%A0n_trong_SDN</ul>
<ul>https://github.com/hocchudong/Thuc-tap-thang-03-2016/blob/master/ThaiPH/SDN/ThaiPH_SDN_OpenFlow.md</ul>
<ul>https://www.youtube.com/watch?v=l25Ukkmk6Sk</ul>
<ul>https://www.youtube.com/watch?v=DiChnu_PAzA</ul>


