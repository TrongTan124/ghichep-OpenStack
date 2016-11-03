Đây là ghi chép của tôi trong quá trình tìm hiểu về OpenStack
====

*Có thể những ghi chép này được cóp nhặt vụn vặt từ nhiều nguồn, tôi sẽ để lại link gốc những nguồn mà tôi tham khảo.*


Trong quá trình tìm hiểu về Cloud Computing, dùng OpenStack để xây dựng một hệ thống Cloud Computing, tìm hiểu cụ thể về thành phần Neutron (Networking) trong OpenStack, tôi có một số 
định hướng cho người bắt đầu tìm hiểu như sau:

1. Đầu tiên cần biết được thế nào là một Cloud Computing.
----
Để trả lời câu hỏi trên có thể tham khảo định nghĩa từ NIST. Bao gồm 5 đặc tính, 4 mô hình triển khai, 3 mô hình dịch vụ.

2. Nguồn gốc của OpenStack
----
Cloud Computing là một khái niệm, một ý niệm, một mô hình; để cụ thể hóa các điều trên, ở đây là dựng lên một hệ thống Cloud Computing trong thực tế, cần sử dụng các phần mềm. 
Có rất nhiều phần mềm hỗ trợ việc triển khai này, trong đó có OpenStack.

3. Tại sao sử dụng OpenStack để cụ thể hóa một Cloud Computing mà không phải phần mềm khác
----
Vì rất nhiều ưu điểm:
- OpenStack được hỗ trợ bởi rất nhiều các công ty lớn: Google, IBM, NASA,...
- OpenStack được viết bằng python (99,99%)
- OpenStack là mã nguồn mở, có lộ trình phát triển rõ ràng
....

4. OpenStack bao gồm những thành phần nào
----
OpenStack gồm rất nhiều thành phần, trong đó có thành phần core (phải có), có thành phần được phát triển với các mục đích riêng của các công ty triển khai
- Core:
	- Nova
	- Keystone
	- Glance
	- Neutron
	
- Add:
	- Horizon
	- Cinder
	- Ceilometer
	- Heat
	....
	
5. Các kiến thức cần thiết để có thể xây dựng và vận hành hệ thống Cloud Computing dùng OpenStack là gì
----
Để có thể xây dựng và vận hành một hệ thống Cloud Computing dùng OpenStack, cần một lượng kiến thức cực kỳ đồ sộ, có thể nói là cực kỳ rộng.
- Kiến thức về Linux
	- Kiến trúc của một HĐH Linux
	- Các command khi làm việc qua terminal
	- Có thể làm việc với Script
	- Cần có một base về Linux như LPI

- Kiến thức về Network
	- Hiểu biết về mô hình OSI
	- Cần base về network như CCNA
	
- Kiến thức về lập trình (fundamental)
	- Có kiến thức về lập trình OOP
	- Có thể đọc hiểu code python
	
- Kiến thức về Database
	- Cấu trúc bảng của MySQL
	- Mô hình dữ liệu
	- Có thể query dữ liệu
	- Cấu hình MySQL
	- Nên có base về DBA
	
- Kiến thức về storage
	- Cấu trúc của các phân vùng nhớ HDD
	- Cách thức tổ chức, lưu trữ, chia sẻ phân vùng nhớ
	
- Kiến thức về bảo mật
	- Cách thức mã hóa file
	- hiểu về SSL, TSL, HTTPS, IPSec,...
	- Hiểu về RSA, SHA,...
	
Còn rất nhiều mảng kiến thức khác phải bổ sung khi làm việc lâu với OpenStack

6. Kiến thức cần thiết để làm việc với Neutron
----
Tôi chia sẻ các kiến thức mà tôi phải tích lũy, trau dồi, bù đắp, đào xới trong khi tìm hiểu về Neutron (một thành phần có nhiệm vụ quản lý Network của Cloud Computing)
- Kiến thức về hypervisor (chủ yếu tôi học KVM-QEMU)
- Kiến thức về ảo hóa trong Linux: namespace, bridge
- Kiến thức về virtual switch: Open vSwitch, Linux Bridge
	- Các module
	- Các workflow
	- SDN, OpenFlow
	- GRE, VXLAN, VLAN
- Kiến thức về DHCP, Router, NAT

Nói thì ngắn gọn nhưng để lĩnh hội được mớ kiến thức trên cũng không phải chỉ vài ngày ngắn ngủi là xong.

