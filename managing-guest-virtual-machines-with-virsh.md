##1. Giới thiệu virsh
<ul> Virsh là một công cụ làm việc qua giao diện dòng lệnh để quản lý các máy ảo và giám sát. 
Lệnh của virsh được xây dựng trên API quản lý libvirt và hoạt động như một cách khác với lệnh qemu-kvm
và ứng dụng đồ họa virt-manager. Lệnh virsh có thể được sử dụng với người dùng không đặc quyền hoặc với truy nhập root.
Lệnh virsh là lý tưởng cho script quản trị ảo hóa. </ul>

##2. Các lệnh thường gặp khi quản lý bằng virsh
<ul> Sửa các thuộc tính của một máy ảo: CPU, RAM, interface, ... </ul>
```
# export xml của máy ảo ra để sửa
# test2 ở đây là tên của máy ảo
root@kvm:~# virsh dumpxml test2 > /tmp/test.xml 

# thực hiện sửa các thuộc tính cần thiết trong file xml vừa export ra
# sau đó import vào máy ảo bằng lệnh
root@kvm:~# virsh define /tmp/test.xml

# thực hiện restart lại máy ảo để xác nhận các điều chỉnh.
```

##3. Quản lý VM qua virt-manager
<ul> Thực hiện cài đặt gói cần thiết </ul>
```
root@kvm:~# apt-get install virt-manager
```

##4. Quản lý VM bằng webvirt manager


## Tham khảo
<ul> https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Virtualization_Administration_Guide/chap-Virtualization_Administration_Guide-Managing_guests_with_virsh.html </ul>
<ul> https://help.ubuntu.com/community/KVM/Managing </ul>
<ul> https://github.com/caongocuy/Install-Webvirtmgr-KVM-simple</ul>
<ul> https://libvirt.org/sources/virshcmdref/html/ </ul>
<ul> </ul>