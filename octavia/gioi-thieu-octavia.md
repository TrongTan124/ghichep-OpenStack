## Tổng quan

### Giới thiệu về Octavia

Octavia là một mã nguồn mở, giải pháp cân bằng tải được thiết kế để làm việc với OpenStack. Octavia được đưa ra khỏi dự án Neutron LBaaS. 
Sự hình thành của nó ảnh hưởng đến sự chuyển đổi của dự án Neutron LBaaS, khi Neutron LBaaS chuyển từ phiên bản 1 sang phiên bản 2. Bắt đầu với việc phát hành 
OpenStack bản Liberty, Octavia đã trở thành ứng dụng tham chiếu cho phiên bản Neutron LBaaS.

Octavia thực hiện việc cung cấp dịch vụ cân bằng tải bằng cách quản lý các VM, container, hoặc BM - gọi chung là amphorae - tùy yêu cầu. Tính năng mở rộng theo yêu cầu và 
theo chiều ngang này giúp phân biệt Octavia với các giải pháp cân bằng tải khác, làm cho Octavia phù hợp với "Cloud".

### Trường hợp Octavia phù hợp với hệ sinh thái OpenStack

Cân bằng tải cần thiết để kích hoạt khả năng mở rộng và khả năng phân phối đơn giản, tự động. Đổi lại, quy mô phân phối và cung cấp ứng dụng phải được coi là các 
tính năng quan trọng của bất kỳ cloud nào.

Do đó, chúng tôi coi Octavia là thiết yếu như Nova, Neutron, Glance hay bất kỳ một dự án "cốt lõi" nào khác cho phép các tính năng thiết yếu của một OpenStack cloud.

Để hoàn thành vai trò của mình, Octavia sử dụng các dự án OpenStack khác:

- *Nova*: Quản lý vòng đời của các amphora và quay vòng tính toán các nguồn lực theo yêu cầu
- *Neutron*: Cung cấp kết nối mạng giữa amphora, môi trường tenant và mạng bên ngoài.
- *Barbican*: Quản lý chứng chỉ và thông tin TLS, khi TLS được cấu hình trên amphorae
- *Keystone*: Để xác thực đối tới octavia API và để Octavia xác thực với các dự án OpenStack khác.
- *Glance*: Để lưu trữ image amphora
- *Oslo*: Để truyền thông giữa các bộ phận điều khoeenr Octavia, làm cho Octavia hoạt động trong framework OpenStack tiêu chuẩn và hệ thống đánh giá, cấu trúc mã dự án
- *Taskflow*: Về mặt kỹ thuật là một phần của Oslo, tuy nhiên, Octavia sử dụng rộng dãi hệ thống taskflow khi điều chỉnh cấu hình và quản lý dịch vụ back-end.

Octavia được thiết kế để tương tác với các thành phần được liệt kê ở trên. Trong mỗi trường hợp, chúng tôi đã quan tâm để xác định tương tác này thông qua giao diện điều khiển. 
Bằng cách đó, các thành phần bên ngoài có thể được thay thế bằng các bộ phận thay thế có chứ năng tương đương mà không cần phải cơ cấu lại các thành phần chính của Octavia. 
Ví dụ, nếu bạn sử dụng một giải pháp SDN khác Neutron trong môi trường của bạn, bạn nên viết một trình điều khiển network Octavia trong môi trường SDN của bạn. 
Một sự thay thế cho trình điều khiển của Neutron tiêu chuẩn trong Octavia.

Điều quan trọng là bạn phải biết rằng, Octavia không nhất thiết được thiết kế như là một sự thay thế hoàn toàn cho dự án Neutron LBaaS. Có nghĩa là, Octavia được thiết kế 
để "cắm vào" với Neutron LBaaS theo cách tương tự như với bất kỳ giải pháp của nhà cung cấp nào: thông qua giao diện điều khiển Neutron LBaaS v2. Bạn có thể nghĩ Octavia là "nhà 
cung cấp mã nguồn mở" cho Neutron LBaaS chứ không phải là một sự thay thế cho Neutron LBaaS. Vì lý do này, chúng tôi khuyên các khách hàng nên cấu hình dịch vụ cân bằng tải 
với Octavia thông qua CLI và API của Neutron LBaaS.

## Tham khảo

- [https://docs.openstack.org/octavia/pike/reference/introduction.html](https://docs.openstack.org/octavia/pike/reference/introduction.html)