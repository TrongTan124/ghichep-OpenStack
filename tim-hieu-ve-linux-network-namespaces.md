##1. Giới thiệu
<ul> - Network namespaces cho phép bạn tạo ra các môi trường mạng tách biệt trên 1 host đơn. </ul>
<ul> - Mỗi namepaces có giao diện và bảng routing riêng, phân chia với các namespaces khác
Việc thêm và xử lý trên hệ thống của bạn có thể được kết hợp với một network namespace cụ thể. </ul>
<ul> - Network namespace đã sử dụng trong các dự án đa dạng như Openstack, Docker và Minimet. 
Để tìm hiểu sâu về những dự án này, bạn phải làm quen với namespaces và biết được cách chúng làm việc. </ul>

####a. Làm việc với network namespaces
<ul> Khi bắt đầu với Linux, bạn sẽ có một namespace trên hệ thống, mọi tiến trình khởi tạo sẽ kế thừa namespace này như cha của nó. Vì vậy, tất cả các tiền trình thừa kế network namespace sử dụng bởi init (PID 1)  </ul>

<img src="http://i0.wp.com/abregman.com/wp-content/uploads/2016/09/namespace_level1.jpg">

##2. Chuẩn bị môi trường LAB
<ul> - Thực hiện lab trên môi trường máy ảo chạy Ubuntu server 14.04 64bit </ul>
<ul> NOTE: nên thực hiện clone và snapshot máy ảo để thuận tiện trong quá trình thử nghiệm </ul>


## Reference
[Youtube](https://www.youtube.com/watch?v=_WgUwUf1d34)
[link1](http://abregman.com/2016/09/29/linux-network-namespace/)
