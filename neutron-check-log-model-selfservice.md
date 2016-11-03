# 1 Check log của Neutron

Lần đầu sau khi cài đặt xong OpenStack, có thể kiểm tra các dòng log xuất hiện lần đầu trong file log của các thành phần

Câu lệnh cài đặt Neutron theo mô hình selfservice tại node controller
```sh
apt-get -y install neutron-server neutron-plugin-ml2 \
    neutron-plugin-openvswitch-agent neutron-l3-agent \
    neutron-dhcp-agent neutron-metadata-agent  \
    python-neutronclient ipset
```

Sau khi cài đặt xong thì log sẽ được lưu tại 02 nơi là
```sh
root@controller1:# cd /var/log/neutron
root@controller1:# cd /var/log/openvswitch
```

Tại thư mục */var/log/neutron* sẽ có các file log sau:
```sh
-rw-r--r--  1 neutron neutron  15336 Nov  3 12:12 dhcp-agent.log
-rw-r--r--  1 neutron neutron   8943 Nov  3 12:13 l3-agent.log
-rw-r--r--  1 neutron neutron  12201 Nov  3 12:44 neutron-metadata-agent.log
-rw-r--r--  1 neutron neutron 536758 Nov  3 14:18 neutron-server.log
-rw-r--r--  1 neutron neutron  30046 Nov  3 12:44 openvswitch-agent.log
-rw-r--r--  1 neutron neutron    728 Nov  3 12:42 ovs-cleanup.log
```

Tại thư mục */var/log/openvswitch* sẽ có các file log sau:
```sh
-rw-r--r--  1 root root      689 Nov  3 12:40 ovs-ctl.log
-rw-r--r--  1 root root      787 Nov  3 12:40 ovsdb-server.log
-rw-r--r--  1 root root   101698 Nov  3 12:42 ovs-vswitchd.log
```

Bây giờ ta sẽ lần lượt kiểm tra log của các file trong thư mục */var/log/neutron*
- Đối với file **dhcp-agent.log**, ta sẽ kiểm tra trường hợp restart dhcp agent để xem log nào sẽ được xuất ra
```sh
root@controller1:/var/log/neutron# service neutron-dhcp-agent restart
stop: Unknown instance: 
neutron-dhcp-agent start/running, process 23122
```

	Log được xuất ra sau lệnh restart trên như sau:
```sh
2016-11-03 12:12:40.168 27203 ERROR neutron.agent.dhcp.agent     message = self.waiters.get(msg_id, timeout=timeout)
2016-11-03 12:12:40.168 27203 ERROR neutron.agent.dhcp.agent   File "/usr/lib/python2.7/dist-packages/oslo_messaging/_drivers/amqpdriver.py", line 244, in get
2016-11-03 12:12:40.168 27203 ERROR neutron.agent.dhcp.agent     'to message ID %s' % msg_id)
2016-11-03 12:12:40.168 27203 ERROR neutron.agent.dhcp.agent MessagingTimeout: Timed out waiting for a reply to message ID 55f9fa7e4c3149749d58b6274612428a
2016-11-03 12:12:40.168 27203 ERROR neutron.agent.dhcp.agent 
2016-11-03 12:12:41.008 27203 WARNING oslo.service.loopingcall [req-47f290ce-5cec-44ee-90ad-bbec45c7db0c - - - - -] Function 'neutron.agent.dhcp.agent.DhcpAgentWithStateReport._report_state' run outlasted interval by 82.53 sec
2016-11-03 12:12:41.701 27203 INFO neutron.agent.dhcp.agent [req-fe8ce7ce-aa1e-4147-a3a8-727b36e9cf51 - - - - -] Agent has just been revived. Scheduling full sync
2016-11-03 12:12:42.734 27203 INFO neutron.agent.dhcp.agent [-] Synchronizing state
2016-11-03 12:12:49.471 27203 INFO neutron.agent.dhcp.agent [-] All active networks have been fetched through RPC.
2016-11-03 12:12:49.472 27203 INFO neutron.agent.dhcp.agent [-] Synchronizing state complete
2016-11-03 14:22:44.462 23122 INFO neutron.common.config [-] Logging enabled!
2016-11-03 14:22:44.463 23122 INFO neutron.common.config [-] /usr/bin/neutron-dhcp-agent version 8.2.0
2016-11-03 14:22:44.547 23122 INFO neutron.agent.dhcp.agent [-] Synchronizing state
2016-11-03 14:22:44.626 23122 INFO neutron.agent.dhcp.agent [req-009dc640-0f90-4cda-aba8-f8116ec0c3e3 - - - - -] Agent has just been revived. Scheduling full sync
2016-11-03 14:22:45.870 23122 INFO neutron.agent.dhcp.agent [-] All active networks have been fetched through RPC.
2016-11-03 14:22:45.871 23122 INFO neutron.agent.dhcp.agent [-] Synchronizing state complete
2016-11-03 14:22:45.897 23122 INFO neutron.agent.dhcp.agent [req-009dc640-0f90-4cda-aba8-f8116ec0c3e3 - - - - -] Synchronizing state
2016-11-03 14:22:45.937 23122 INFO neutron.agent.dhcp.agent [-] DHCP agent started
2016-11-03 14:22:46.136 23122 INFO neutron.agent.dhcp.agent [req-009dc640-0f90-4cda-aba8-f8116ec0c3e3 - - - - -] All active networks have been fetched through RPC.
2016-11-03 14:22:46.137 23122 INFO neutron.agent.dhcp.agent [req-009dc640-0f90-4cda-aba8-f8116ec0c3e3 - - - - -] Synchronizing state complete
2016-11-03 14:22:51.141 23122 INFO neutron.agent.dhcp.agent [-] Synchronizing state
2016-11-03 14:22:51.513 23122 INFO neutron.agent.dhcp.agent [-] All active networks have been fetched through RPC.
2016-11-03 14:22:51.515 23122 INFO neutron.agent.dhcp.agent [-] Synchronizing state complete
```

Ta kiểm tra lệnh trên thấy hệ thống xuất ra là chưa thấy dhcp agent chạy, hệ thống sẽ chạy dhcp lần đầu, bây giờ ta thực hiện restart lần 2 để xem log:
```sh
root@controller1:/var/log/neutron# service neutron-dhcp-agent restart
neutron-dhcp-agent stop/waiting
neutron-dhcp-agent start/running, process 24274

Log:
2016-11-03 14:28:25.865 24274 INFO neutron.common.config [-] Logging enabled!
2016-11-03 14:28:25.997 24274 INFO neutron.common.config [-] /usr/bin/neutron-dhcp-agent version 8.2.0
2016-11-03 14:28:26.061 24274 INFO neutron.agent.dhcp.agent [-] Synchronizing state
2016-11-03 14:28:26.372 24274 INFO neutron.agent.dhcp.agent [-] All active networks have been fetched through RPC.
2016-11-03 14:28:26.374 24274 INFO neutron.agent.dhcp.agent [-] Synchronizing state complete
2016-11-03 14:28:26.408 24274 INFO neutron.agent.dhcp.agent [req-939d3edf-4e78-46d3-becd-0a1ec0a9f51f - - - - -] Synchronizing state
2016-11-03 14:28:26.463 24274 INFO neutron.agent.dhcp.agent [-] DHCP agent started
2016-11-03 14:28:26.661 24274 INFO neutron.agent.dhcp.agent [req-939d3edf-4e78-46d3-becd-0a1ec0a9f51f - - - - -] All active networks have been fetched through RPC.
2016-11-03 14:28:26.662 24274 INFO neutron.agent.dhcp.agent [req-939d3edf-4e78-46d3-becd-0a1ec0a9f51f - - - - -] Synchronizing state complete
```

- Ta kiểm tra log của file **l3-agent.log** trong trường hợp restart l3 sẽ xuất ra như sao:
```sh
root@controller1:/var/log/neutron# service neutron-l3-agent restart
stop: Unknown instance: 
neutron-l3-agent start/running, process 23943
```

	Log được xuất ra sau lệnh restart trên như sau:
```sh
2016-11-03 12:13:07.391 27256 ERROR neutron.agent.l3.agent   File "/usr/lib/python2.7/dist-packages/oslo_messaging/_drivers/amqpdriver.py", line 459, in _send
2016-11-03 12:13:07.391 27256 ERROR neutron.agent.l3.agent     result = self._waiter.wait(msg_id, timeout)
2016-11-03 12:13:07.391 27256 ERROR neutron.agent.l3.agent   File "/usr/lib/python2.7/dist-packages/oslo_messaging/_drivers/amqpdriver.py", line 342, in wait
2016-11-03 12:13:07.391 27256 ERROR neutron.agent.l3.agent     message = self.waiters.get(msg_id, timeout=timeout)
2016-11-03 12:13:07.391 27256 ERROR neutron.agent.l3.agent   File "/usr/lib/python2.7/dist-packages/oslo_messaging/_drivers/amqpdriver.py", line 244, in get
2016-11-03 12:13:07.391 27256 ERROR neutron.agent.l3.agent     'to message ID %s' % msg_id)
2016-11-03 12:13:07.391 27256 ERROR neutron.agent.l3.agent MessagingTimeout: Timed out waiting for a reply to message ID 899c792a3b56449fa1e9d276d42a1324
2016-11-03 12:13:07.391 27256 ERROR neutron.agent.l3.agent 
2016-11-03 12:13:07.481 27256 WARNING oslo.service.loopingcall [-] Function 'neutron.agent.l3.agent.L3NATAgentWithStateReport._report_state' run outlasted interval by 106.48 sec
2016-11-03 12:13:07.544 27256 INFO neutron.agent.l3.agent [-] Agent has just been revived. Doing a full sync.
2016-11-03 14:26:51.857 23943 INFO neutron.common.config [-] Logging enabled!
2016-11-03 14:26:51.858 23943 INFO neutron.common.config [-] /usr/bin/neutron-l3-agent version 8.2.0
2016-11-03 14:26:52.085 23943 INFO eventlet.wsgi.server [-] (23943) wsgi starting up on http:/var/lib/neutron/keepalived-state-change
2016-11-03 14:26:52.106 23943 INFO neutron.agent.l3.agent [-] Agent has just been revived. Doing a full sync.
2016-11-03 14:26:52.124 23943 INFO neutron.agent.l3.agent [-] L3 agent started
```

	Cũng tương tự như lệnh restart dhcp, lệnh này cũng báo là l3 chưa được chạy, và chạy lần đầu, giờ ta thực hiện restart lại l3 agent.
```sh
root@controller1:/var/log/neutron# service neutron-l3-agent restart
neutron-l3-agent stop/waiting
neutron-l3-agent start/running, process 25054

Log:
2016-11-03 14:32:18.742 25054 INFO neutron.common.config [-] Logging enabled!
2016-11-03 14:32:18.744 25054 INFO neutron.common.config [-] /usr/bin/neutron-l3-agent version 8.2.0
2016-11-03 14:32:19.024 25054 INFO eventlet.wsgi.server [-] (25054) wsgi starting up on http:/var/lib/neutron/keepalived-state-change
2016-11-03 14:32:19.057 25054 INFO neutron.agent.l3.agent [-] L3 agent started
```

- Ta kiểm tra log của file **neutron-metadata-agent.log** khi thực hiện lệnh restart metadata agent.
```sh
root@controller1:/var/log/neutron# service neutron-metadata-agent restart
neutron-metadata-agent stop/waiting
neutron-metadata-agent start/running, process 25374
```

	Log được xuất ra như sau:
```sh
2016-11-03 12:44:19.559 1318 ERROR neutron.agent.metadata.agent     retry=retry)
2016-11-03 12:44:19.559 1318 ERROR neutron.agent.metadata.agent   File "/usr/lib/python2.7/dist-packages/oslo_messaging/_drivers/amqpdriver.py", line 459, in _send
2016-11-03 12:44:19.559 1318 ERROR neutron.agent.metadata.agent     result = self._waiter.wait(msg_id, timeout)
2016-11-03 12:44:19.559 1318 ERROR neutron.agent.metadata.agent   File "/usr/lib/python2.7/dist-packages/oslo_messaging/_drivers/amqpdriver.py", line 342, in wait
2016-11-03 12:44:19.559 1318 ERROR neutron.agent.metadata.agent     message = self.waiters.get(msg_id, timeout=timeout)
2016-11-03 12:44:19.559 1318 ERROR neutron.agent.metadata.agent   File "/usr/lib/python2.7/dist-packages/oslo_messaging/_drivers/amqpdriver.py", line 244, in get
2016-11-03 12:44:19.559 1318 ERROR neutron.agent.metadata.agent     'to message ID %s' % msg_id)
2016-11-03 12:44:19.559 1318 ERROR neutron.agent.metadata.agent MessagingTimeout: Timed out waiting for a reply to message ID d3a5ad8e560843ebbd91a03032e1c0ff
2016-11-03 12:44:19.559 1318 ERROR neutron.agent.metadata.agent 
2016-11-03 12:44:19.653 1318 WARNING oslo.service.loopingcall [-] Function 'neutron.agent.metadata.agent.UnixDomainMetadataProxy._report_state' run outlasted interval by 100.05 sec
2016-11-03 14:33:47.243 2233 INFO eventlet.wsgi.server [-] (2233) wsgi exited, is_accepting=True
2016-11-03 14:33:47.823 1318 INFO oslo_service.service [req-c4c07163-224b-4058-ad1a-b0b96af8256c - - - - -] Caught SIGTERM, stopping children
2016-11-03 14:33:47.826 1318 INFO oslo_service.service [req-c4c07163-224b-4058-ad1a-b0b96af8256c - - - - -] Waiting on 1 children to exit
2016-11-03 14:33:47.828 1318 INFO oslo_service.service [req-c4c07163-224b-4058-ad1a-b0b96af8256c - - - - -] Child 2233 exited with status 0
2016-11-03 14:33:50.340 25374 INFO neutron.common.config [-] Logging enabled!
2016-11-03 14:33:50.341 25374 INFO neutron.common.config [-] /usr/bin/neutron-metadata-agent version 8.2.0
2016-11-03 14:33:50.353 25374 INFO oslo_service.service [req-3f3f2d63-3e96-4f57-a911-10aead129d5b - - - - -] Starting 1 workers
2016-11-03 14:33:50.364 25390 INFO eventlet.wsgi.server [-] (25390) wsgi starting up on http:/var/lib/neutron/metadata_proxy
```

	Ta cũng thực hiện restat lại metadata agent lần 2 để kiểm tra log:
```sh
root@controller1:/var/log/neutron# service neutron-metadata-agent restart
neutron-metadata-agent stop/waiting
neutron-metadata-agent start/running, process 25662

Log:
2016-11-03 14:35:09.017 25390 INFO eventlet.wsgi.server [-] (25390) wsgi exited, is_accepting=True
2016-11-03 14:35:09.447 25374 INFO oslo_service.service [req-3f3f2d63-3e96-4f57-a911-10aead129d5b - - - - -] Caught SIGTERM, stopping children
2016-11-03 14:35:09.452 25374 INFO oslo_service.service [req-3f3f2d63-3e96-4f57-a911-10aead129d5b - - - - -] Waiting on 1 children to exit
2016-11-03 14:35:09.455 25374 INFO oslo_service.service [req-3f3f2d63-3e96-4f57-a911-10aead129d5b - - - - -] Child 25390 exited with status 0
2016-11-03 14:35:11.949 25662 INFO neutron.common.config [-] Logging enabled!
2016-11-03 14:35:11.950 25662 INFO neutron.common.config [-] /usr/bin/neutron-metadata-agent version 8.2.0
2016-11-03 14:35:12.019 25662 INFO oslo_service.service [req-5004a601-d563-43cf-97b4-bab141f1d51e - - - - -] Starting 1 workers
2016-11-03 14:35:12.038 25680 INFO eventlet.wsgi.server [-] (25680) wsgi starting up on http:/var/lib/neutron/metadata_proxy
```

# Tham khảo