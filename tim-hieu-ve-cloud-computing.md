##1. Cloud Computing
<ul> Định nghĩa từ Viện tiêu chuẩn và công nghệ quốc gia của Mỹ - The National Institute of Standards and Technology (NIST): 
<li> Cloud Computing là mô hình cho phép truy cập qua mạng để lựa chọn và sử dụng tài nguyên có thể được tính toán 
(ví dụ: mạng, máy chủ, lưu trữ, ứng dụng và dịch vụ) theo nhu cầu một cách thuận tiện và nhanh chóng; 
đồng thời cho phép kết thúc sử dụng dịch vụ, giải phóng tài nguyên dễ dàng, giảm thiểu các giao tiếp với nhà cung cấp” 

<p> <i>Cloud computing is a model for enabling ubiquitous, convenient, on-demand network access to a shared
pool of configurable computing resources (e.g., networks, servers, storage, applications, and services) that
can be rapidly provisioned and released with minimal management effort or service provider interaction.
This cloud model is composed of five essential characteristics, three service models, and four deployment
models </i></p></li>
</ul>

<ul> Một hệ thống cloud computing cần đáp ứng được các điểm cần thiết dưới đây: </ul>
<ul> <b>5 đặc tính</b>
<li> Rapid elasticity: Khả năng cấp phát và thu hồi tài nguyên, tự động mở rộng theo nhu cầu người dùng bất cứ lúc nào.
 <p>(<i>Capabilities can be elastically provisioned and released, in some cases
automatically, to scale rapidly outward and inward commensurate with demand. To the
consumer, the capabilities available for provisioning often appear to be unlimited and can
be appropriated in any quantity at any time</i>)</p></li>

<li> Broad network access: Khả năng truy cập thông qua các chuẩn kết nối 

<p>(<i>Capabilities are available over the network and accessed through standard
mechanisms that promote use by heterogeneous thin or thick client platforms (e.g.,
mobile phones, tablets, laptops, and workstations)</i>)</p></li>

<li> Resource pooling: Gom gộp tài nguyên và phân bổ tự động theo nhu cầu người dùng. 

<p>(<i>The provider’s computing resources are pooled to serve multiple consumers
using a multi-tenant model, with different physical and virtual resources dynamically
assigned and reassigned according to consumer demand. There is a sense of location
independence in that the customer generally has no control or knowledge over the exact
location of the provided resources but may be able to specify location at a higher level of
abstraction (e.g., country, state, or datacenter). Examples of resources include storage,
processing, memory, and network bandwidth</i>)</p></li>

<li> Measured service: Tự động kiểm soát và tối ưu tài nguyên bằng cách đo lường mức độ sử dụng dịch vụ.

<p>(<i>Cloud systems automatically control and optimize resource use by leveraging
a metering capability at some level of abstraction appropriate to the type of service (e.g.,
storage, processing, bandwidth, and active user accounts). Resource usage can be
monitored, controlled, and reported, providing transparency for both the provider and
consumer of the utilized service</i>)</p></li>

<li> On-demand self-service: Khả năng tự phục vụ theo nhu cầu một cách tự động mà không cần tương tác giữa khách hàng và nhà cung cấp.

<p>(<i>A consumer can unilaterally provision computing capabilities, such as
server time and network storage, as needed automatically without requiring human
interaction with each service provider</i>)</p></li>

</ul>
<ul> <b>4 mô hình triển khai</b>
<li> Private cloud: 

<p>(<i>The cloud infrastructure is provisioned for exclusive use by a single organization
comprising multiple consumers (e.g., business units). It may be owned, managed, and
operated by the organization, a third party, or some combination of them, and it may exist
on or off premises</i>)</p></li>

<li> Public cloud: 

<p>(<i>The cloud infrastructure is provisioned for open use by the general public. It may be
owned, managed, and operated by a business, academic, or government organization, or
some combination of them. It exists on the premises of the cloud provider</i>)</p></li>

<li> Hybrid cloud: 

<p>(<i>The cloud infrastructure is a composition of two or more distinct cloud
infrastructures (private, community, or public) that remain unique entities, but are bound
together by standardized or proprietary technology that enables data and application
portability (e.g., cloud bursting for load balancing between clouds)</i>)</p></li>

<li> Community cloud: 

<p>(<i>The cloud infrastructure is provisioned for exclusive use by a specific
community of consumers from organizations that have shared concerns (e.g., mission,
security requirements, policy, and compliance considerations). It may be owned,
managed, and operated by one or more of the organizations in the community, a third
party, or some combination of them, and it may exist on or off premises</i>)</p></li>
</ul>

<ul> <b>3 mô hình dịch vụ</b>
<li> Software as a Service (SaaS): 

<p>(<i>The capability provided to the consumer is to use the provider’s
applications running on a cloud infrastructure. The applications are accessible from
various client devices through either a thin client interface, such as a web browser (e.g.,
web-based email), or a program interface. The consumer does not manage or control the
underlying cloud infrastructure including network, servers, operating systems, storage, or
even individual application capabilities, with the possible exception of limited userspecific application configuration settings.</i>)</p></li>

<li> Platform as a Service (PaaS): 

<p>(<i>The capability provided to the consumer is to deploy onto the cloud
infrastructure consumer-created or acquired applications created using programming
languages, libraries, services, and tools supported by the provider. The consumer does
not manage or control the underlying cloud infrastructure including network, servers,
operating systems, or storage, but has control over the deployed applications and possibly
configuration settings for the application-hosting environment.</i>)</p></li>

<li> Infrastructure as a Service (IaaS): 

<p>(<i>The capability provided to the consumer is to provision
processing, storage, networks, and other fundamental computing resources where the
consumer is able to deploy and run arbitrary software, which can include operating
systems and applications. The consumer does not manage or control the underlying cloud
infrastructure but has control over operating systems, storage, and deployed applications;
and possibly limited control of select networking components (e.g., host firewalls)</i>)</p></li>
</ul>

##2. OpenStack
###a. Giới thiệu
<p> Cloud là một khái niệm trừu tượng, để chuyển các khái niệm đó thành thực tế cần xây dựng hệ thống đáp ứng được các đặc điểm cần thiết của cloud computing được chỉ ra bên trên. </p>
<p> Thực tế có rất nhiều hệ thống đáp ứng nhu cầu dựng một hệ thống cloud computing như: </p>
<ul>Chúng ta thực hiện chọn OpenStack để xây dựng hệ thống cloud computing vì một số đặc điểm sau:
<li> Openstack là 1 công nghệ mới và còn phát triển hơn nữa trong tương lai. </li>
<li> Được các công ty lớn ủng hộ như: IBM, Cisco, Google... </li>
<li> Sử dụng 1 ngôn ngữ duy nhất (99% python). </li>
<li> Triển khai quy mô lớn </li>
<li> Mọi thứ đều mở, open source code. </li>
<li> Có lộ trình phát triển rõ ràng. </li>
</ul>

###b. Tóm lược
<ul> OpenStack được thiết kế theo hướng module, nghĩa là các thành phần phía trong có thể triển khai tách rời và tương tác với nhau theo API. </ul>
<ul> Mở về thiết kế, lộ trình phát triển, cộng đồng, mã nguồn. </ul>
<ul> Mỗi 6 tháng sẽ phát hành một phiên bản mới </ul>
<ul> Sử dụng ngôn ngữ python </ul>

###c. Kiến trúc
<ul> Kiến trúc theo ý niệm: </ul>
<img src="https://camo.githubusercontent.com/9733463e8cf45224c060acf265166d7c8b428609/687474703a2f2f646f63732e6f70656e737461636b2e6f72672f6a756e6f2f696e7374616c6c2d67756964652f696e7374616c6c2f6170742f636f6e74656e742f666967757265732f312f612f636f6d6d6f6e2f666967757265732f6f70656e737461636b5f686176616e615f636f6e6365707475616c5f617263682e706e67">
<ul> Kiến trúc theo logic </ul>
<img src="https://camo.githubusercontent.com/efc54e908f39e6d3c5630175b3aba93aaad14947/687474703a2f2f646f63732e6f70656e737461636b2e6f72672f696365686f7573652f747261696e696e672d6775696465732f636f6e74656e742f666967757265732f352f612f666967757265732f6f70656e737461636b2d617263682d686176616e612d6c6f676963616c2d76312e6a7067">
<ul> Kiến trúc triển khai thử nghiệm </ul>
<img src="http://2.bp.blogspot.com/-VV4BMLgzUr4/VElEolk2BjI/AAAAAAAAAFI/EOlNMtfpCm0/s1600/OpenStack-Compute-Nodes-.png">


<ul> </ul>
<ul> </ul>
<ul> </ul>
<ul> </ul>
<ul> </ul>

## Tham khảo
<ul>https://github.com/hocchudong/Thuc-tap-thang-03-2016/blob/master/TriMQ/TriMQ_baocaoCloudComputing%26Openstack.md </ul>