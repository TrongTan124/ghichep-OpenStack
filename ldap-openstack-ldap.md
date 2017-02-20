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

Sau khi cài đặt xong devstack với cấu hình local.conf có LDAP, ta sẽ có một hệ thống OpenStack All in One, tức là mọi thứ được cài trên 01 máy tính duy nhất.
Ta kiểm tra lại hệ thống OpenStack vừa dựng bằng các lệnh:

- Kiểm tra cây thư mục LDAP, kết quả là một cây thư mục hoàn chỉnh của OpenStack gồm Projects, Roles, UserGroups, Users
```sh
# slapcat 
```

![ldap-tree](/Images/ldap-tree.png)

- Kiểm tra lại OpenStack bằng các lênh:
```sh
openstack user list
openstack token issue
openstack project list
openstack group list
openstack role list
openstack domain list
```

*Lưu ý*: cần export các biến môi trường để thực hiện chạy các lệnh trên, tạo tập tin biến môi trường với nội dung sau:
```sh
export OS_IDENTITY_API_VERSION=3
export OS_AUTH_URL=http://172.16.68.133:5000/v3
export OS_USERNAME=admin
export OS_PROJECT_NAME=admin
export OS_USER_DOMAIN_NAME=Default
export OS_PASSWORD=openstack
export OS_PROJECT_DOMAIN_NAME=Default
```

sau đó sử dụng lệnh `source openrc` để export các biến này vào phiên shell đang làm việc.

- Hiện tại ta có một hệ thống OpenStack với project keystone chạy trên 02 backend. mặc định là SQL, còn identity sử dụng LDAP.
Có thể xem cấu hình trong keystone để biết rõ hơn.
```sh
root@devstack1:/opt/stack# cat /etc/keystone/keystone.conf |egrep -v "^#|^$"
[DEFAULT]
max_token_size = 16384
logging_exception_prefix = %(asctime)s.%(msecs)03d %(process)d TRACE %(name)s %(instance)s
debug = True
admin_endpoint = http://172.16.68.133/identity_v2_admin
public_endpoint = http://172.16.68.133/identity
transport_url = rabbit://stackrabbit:openstack@172.16.68.133:5672/
member_role_name = _member_
member_role_id = 9fe2ff9ee4384b1894a90878d3e92bab
[assignment]
driver = sql
[cache]
memcache_servers = localhost:11211
backend = oslo_cache.memcache_pool
enabled = True
[credential]
key_repository = /etc/keystone/credential-keys/
[database]
connection = mysql+pymysql://root:openstack@172.16.68.133/keystone?charset=utf8
[fernet_tokens]
key_repository = /etc/keystone/fernet-keys/
[identity]
driver = ldap
[ldap]
user_tree_dn = ou=Users,dc=openstack,dc=org
user_domain_id_attribute = businessCategory
tenant_tree_dn = ou=Projects,dc=openstack,dc=org
tenant_desc_attribute = description
tenant_domain_id_attribute = businessCategory
tenant_attribute_ignore = enabled
user_attribute_ignore = enabled,email,tenants,default_project_id
use_dumb_member = True
suffix = dc=openstack,dc=org
user = cn=Manager,dc=openstack,dc=org
password = 9632
[paste_deploy]
config_file = /etc/keystone/keystone-paste.ini
[resource]
admin_project_name = admin
admin_project_domain_name = Default
driver = sql
[role]
driver = sql
[token]
driver = sql
```

- Ta thực hiện tạo một người dùng trong OpenStack để kiểm tra thông tin người dùng trong keystone được lưu trữ như nào. Thực hiện chạy các lệnh sau:
```sh
openstack project create tannt_project --domain default --description "tannt test project"
openstack user create tannt --email tannt@openstack.com --domain default --description "tannt openstack user account" --password tan@123++
openstack role add Member --project tannt_project --project-domain default --user tannt --user-domain default
```

Người dùng đươc tạo trong một domain mặc định cùng với role mặc định. 03 lệnh trên là: tạo project, tạo người dùng, gán role cho người dùng.

- Một người dùng mới được tạo ra, sẽ có các sự kiện sau:
	- Gán người dùng vào LDAP
	- Gán role người dùng trong bảng `assignment` của MySQL
	- Gán thông tin người dùng vào bảng `nonlocal_user` trong MySQL
	- Gán project mới tạo vào bảng `project` trong MySQL
	- Thêm tên người dùng vào bảng `user` trong MySQL

- Khi người dùng thực hiện đăng nhập vào horizon sẽ sinh ra một token, và token này được ghi vào bảng `token` trong MySQL. 
Check bằng lệnh *SELECT * FROM `token` WHERE user_id = 'f8afdf4455b94bd6ba20bdeef05ba732';*

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
