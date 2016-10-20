##1. Linux bridge là gì?
<ul> Một bridge là cách thức phân chia thành 2 hoặc nhiều hơn các phần mạng (network segment) riêng biệt trong phạm vi một logical network (ví dụ một IP-subnet)</ul>
<ul> Một bridge thường được đặt giữa 2 nhóm riêng biệt của máy tính, nơi chúng trao đổi với nhau nhưng không trao đổi với nhóm khác. </ul>
<ul> Công việc của bridge là xem xét đích của các data packet tại một thời điểm và lựa chọn có cho packet đi tới side khác của Ethernet. Dẫn tới network sẽ nhanh hơn, đơn giản hơn với ít miền đụng độ </ul>
<ul>  </ul>
<ul> Luật bridge quyết định việc gửi hay xóa dữ liệu không dựa vào loại protocol (IP, IPX, NetBEUI), nhưng xem xét duy nhất địa chỉ MAC của mỗi NIC </ul>
*Note*: Quan trọng để hiểu bridge không phải là router hay firewall. Nói ngắn gọn, một bridge xử lý như một switch (Layer 2 switch), làm trong suốt các thành phần mạng (không chính xác tuyệt đối nhưng gần đúng).
<ul> Thêm nữa, bạn có thể khắc phục hardware không tương thích với một bridge, không cần sự cho phép address-range của IP-net hay subnet. </ul>
ví dụ: nó có thể bridge giữa kết nối vật lý khác nhau như 10 Base T và 100 Base TX

*Các đặc tính thuần túy của bridge*
<ul> STP: Spanning Tree Protocol là một phương thức thuận lợi cho việc giữ các thiết bị kết nối trong khi có nhiều kết nối.</ul>
<ul> Multiple Bridge Instances: cho phép bạn chạy được nhiều hơn một bridge trên máy và điều khiển một cái tách biệt nhau. </ul>
<ul> Fire-walling: Có một phần của luật bridging cho phép bạn sử dụng IP chain trên interface vào một bridge </ul>

##2. Quy tắc trên Bridging
Có một số quy tắc bạn không được phép phá vỡ (nếu không thì bridge của bạn sẽ hỏng)
<ul> 
<li> Một port chỉ có thể là một thành phần của một bridge  </li>
<li> Một bridge không biết gì về route </li>
<li> Một bridge không biết gì về các giao thức cao hơn ARP. Đó là lý do nó có thể bridge mọi giao thức có thế chạy trên Ethernet của bạn </li>
<li> Không có vấn đề về việc có bao nhiêu port trong logical bridge, nó được bao phủ bởi một logical interface duy nhất </li>
<li> Ngay khi một port (ví dụ một NIC) được gán cho một bridge, bạn sẽ không thể trực tiếp điều khiển nó </li>
 </ul>

## Tham khảo
[http://www.tldp.org/HOWTO/BRIDGE-STP-HOWTO/what-is-a-bridge.html](http://www.tldp.org/HOWTO/BRIDGE-STP-HOWTO/what-is-a-bridge.html)


