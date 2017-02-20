# Chuẩn bị

- Thực hiện thử nghiệm dựng Openstack mitaka sử dụng LDAP cho thành phần identity của keystone.
- Tôi cài đặt bằng 02 cách: 
	- Cài đặt bằng script, và sử dụng một hệ thống LDAP tách biệt
	- Cài đặt bằng devstack, có cài thêm LDAP trên node nội tại luôn.
- Hệ thống máy chủ thực hiện cài đặt chạy trên vmware, các máy chủ đều có 02 interface

# Cài đặt bằng devstack

## Cấu hình local.conf

- Ta thực hiện thiết lập một số tham số để chuẩn bị cho script cài đặt.

## Kết quả

Chưa rõ tại sao cài chưa thành công. mà lần nào cũng chạy mất 1h30p mới lòi ra lỗi. :(

# Cài đặt bằng script

Trong phần này tôi sẽ thực hiện cài OpenStack bằng script, sau đó cấu hình cho keystone sử dụng LDAP.

## Cấu hình LDAP tree

- Trước tiên cần cài đặt và cấu hình LDAP.
	- dn: dc=vnptdata,dc=vn

## Chỉnh sửa cấu hình keystone

- Cấu hình identity sử dụng LDAP bên ngoài, cần chỉnh sửa cấu hình trong file `/etc/keystone.conf` như sau:
```sh
[identity]
driver = keystone.identity.backends.ldap.Identity

[ldap]
url = ldap://localhost
user = dc=admin,dc=vnptdata,dc=vn
password = tan124
suffix = dc=vnptdata,dc=vn
user_tree_dn = ou=Users,dc=vnptdata,dc=vn
user_objectclass = inetOrgPerson
user_id_attribute = cn
user_mail_attribute = mail
tenant_tree_dn = ou=Projects,dc=vnptdata,dc=vn
tenant_objectclass = groupOfNames
tenant_id_attribute = cn
tenant_desc_attribute = description
use_dumb_member = True
role_tree_dn = ou=Roles,dc=vnptdata, dc=vn
role_objectclass = organizationalRole
role_id_attribute = cn
role_member_attribute = roleOccupant

user_allow_create = False
user_allow_update = False
user_allow_delete = False

tenant_allow_create = False
tenant_allow_update = False
tenant_allow_delete = False

role_allow_create = False
role_allow_update = False
role_allow_delete = False
```

- Ngoài ra cần kích hoạt tính năng `authlogin_nsswitch_use_ldap` cho selinux
```sh
setsebool -P authlogin_nsswitch_use_ldap on
```

- Chỉ sử dụng LDAP cho xác thực, còn gán quyền cho người dùng vẫn sử dụng SQL
```sh
[assignment]
driver = keystone.assignment.backends.sql.Assignment
```

# Tham khảo
- [http://www.ibm.com/developerworks/cloud/library/cl-ldap-keystone/](http://www.ibm.com/developerworks/cloud/library/cl-ldap-keystone/)
