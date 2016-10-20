##1. Linux bridge là gì?
<ul> Một bridge là cách thức phân chia thành 2 hoặc nhiều hơn các phần mạng (network segment) riêng biệt trong phạm vi một logical network (ví dụ một IP-subnet)</ul>
<ul> Một bridge thường được đặt giữa 2 nhóm riêng biệt của máy tính, nơi chúng trao đổi với nhau nhưng không trao đổi với nhóm khác. </ul>
<ul> Công việc của bridge là xem xét đích của các data packet tại một thời điểm và lựa chọn có cho packet đi tới side khác của Ethernet. Dẫn tới network sẽ nhanh hơn, đơn giản hơn với ít miền đụng độ </ul>
<ul>  </ul>
<ul> Luật bridge quyết định việc gửi hay xóa dữ liệu không dựa vào loại protocol (IP, IPX, NetBEUI), nhưng xem xét duy nhất địa chỉ MAC của mỗi NIC </ul>
*Note*: Quan trọng để hiểu bridge không phải là router hay firewall. Nói ngắn gọn, một bridge xử lý như một switch (Layer 2 switch), làm trong suốt các thành phần mạng (không chính xác tuyệt đối nhưng gần đúng).


## Tham khảo
[http://www.tldp.org/HOWTO/BRIDGE-STP-HOWTO/what-is-a-bridge.html](http://www.tldp.org/HOWTO/BRIDGE-STP-HOWTO/what-is-a-bridge.html)


