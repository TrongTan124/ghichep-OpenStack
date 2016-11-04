# 1 Check log của Neutron

Lần đầu sau khi cài đặt xong OpenStack, có thể kiểm tra các dòng log xuất hiện lần đầu trong file log của các thành phần

Câu lệnh cài đặt Neutron theo mô hình selfservice tại node Controller
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

----

- Đối với file **/var/log/neutron/dhcp-agent.log**, ta sẽ kiểm tra trường hợp restart dhcp agent để xem log nào sẽ được xuất ra
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

----

- Ta kiểm tra log của file **/var/log/neutron/l3-agent.log** trong trường hợp restart l3 sẽ xuất ra như sao:
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

----

- Ta kiểm tra log của file **/var/log/neutron/neutron-metadata-agent.log** khi thực hiện lệnh restart metadata agent.
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

----

- Ta kiểm tra log của file **/var/log/neutron/openvswitch-agent.log** sau lệnh restart OVS agent:
```sh
root@controller1:/var/log/neutron# service neutron-openvswitch-agent restart
neutron-openvswitch-agent stop/waiting
neutron-openvswitch-agent start/running, process 26788
```

Log được xuất ra như sau:
```sh
2016-11-03 14:40:54.480 2248 ERROR neutron.agent.linux.async_process [-] Error received from [ovsdb-client monitor Interface name,ofport,external_ids --format=json]: 2016-11-03T07:40:54Z|00001|fatal_signal|WARN|terminating with signal 15 (Terminated)
2016-11-03 14:40:54.484 2248 ERROR neutron.agent.linux.async_process [-] Process [ovsdb-client monitor Interface name,ofport,external_ids --format=json] dies due to the error: 2016-11-03T07:40:54Z|00001|fatal_signal|WARN|terminating with signal 15 (Terminated)
2016-11-03 14:40:55.683 2248 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-150711cb-545e-4669-bf60-5785f3be7562 - - - - -] Agent caught SIGTERM, quitting daemon loop.
2016-11-03 14:40:58.420 26788 INFO neutron.common.config [-] Logging enabled!
2016-11-03 14:40:58.422 26788 INFO neutron.common.config [-] /usr/bin/neutron-openvswitch-agent version 8.2.0
2016-11-03 14:40:59.632 26788 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-d36607fb-b3d5-45d0-a886-0c732162e70b - - - - -] Mapping physical network external to bridge br-ex
2016-11-03 14:41:02.473 26788 INFO neutron.agent.l2.extensions.manager [req-d36607fb-b3d5-45d0-a886-0c732162e70b - - - - -] Loaded agent extensions: []
2016-11-03 14:41:03.876 26788 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-d36607fb-b3d5-45d0-a886-0c732162e70b - - - - -] Agent initialized successfully, now running... 
2016-11-03 14:41:04.086 26788 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-d36607fb-b3d5-45d0-a886-0c732162e70b - - - - -] Configuring tunnel endpoints to other OVS agents
```

Ta cũng thấy sau lệnh restart trên thì file log **/var/log/openvswitch/ovs-vswitchd.log** cũng xuất hiện log sau:
```sh
2016-11-03T07:40:59.280Z|00038|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-03T07:40:59.394Z|00039|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-03T07:40:59.501Z|00040|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-03T07:41:00.161Z|00041|connmgr|INFO|br-ex<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-03T07:41:00.966Z|00042|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-03T07:41:01.070Z|00043|connmgr|INFO|br-ex<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-03T07:41:02.780Z|00044|connmgr|INFO|br-tun<->unix: 9 flow_mods in the last 0 s (9 adds)
2016-11-03T07:41:02.907Z|00045|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 adds)
```

Ngoài ra file **/var/log/neutron/neutron-server.log** cũng có dòng log:
```sh
2016-11-03 14:45:25.004 3072 WARNING neutron.plugins.ml2.drivers.type_tunnel [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Endpoint with ip 10.10.10.80 already exists
```

----

- Ta kiểm tra file log **/var/log/neutron/neutron-server.log** sau khi restart lại neutron server:
```sh
root@controller1:/var/log/neutron# service neutron-server restart
neutron-server stop/waiting
neutron-server start/running, process 28472
```

Có rất nhiều log xuất ra:
```sh
2016-11-03 14:47:28.020 3068 INFO neutron.wsgi [-] (3068) wsgi exited, is_accepting=True
2016-11-03 14:47:28.021 3067 INFO neutron.wsgi [-] (3067) wsgi exited, is_accepting=True
2016-11-03 14:47:28.875 1250 INFO oslo_service.service [-] Caught SIGTERM, stopping children
2016-11-03 14:47:28.879 1250 INFO oslo_service.service [-] Waiting on 2 children to exit
2016-11-03 14:47:28.881 1250 INFO oslo_service.service [-] Child 3067 exited with status 0
2016-11-03 14:47:28.884 1250 INFO oslo_service.service [-] Child 3068 exited with status 0
2016-11-03 14:47:28.887 1250 INFO oslo_service.service [-] Wait called after thread killed. Cleaning up.
2016-11-03 14:47:28.899 1250 INFO oslo_service.service [-] Waiting on 2 children to exit
2016-11-03 14:47:28.921 1250 INFO oslo_service.service [-] Child 3074 killed by signal 15
2016-11-03 14:47:28.924 1250 INFO oslo_service.service [-] Child 3072 killed by signal 15
2016-11-03 14:47:31.964 28472 INFO neutron.common.config [-] Logging enabled!
2016-11-03 14:47:31.966 28472 INFO neutron.common.config [-] /usr/bin/neutron-server version 8.2.0
2016-11-03 14:47:31.969 28472 INFO neutron.common.config [-] Logging enabled!
2016-11-03 14:47:31.969 28472 INFO neutron.common.config [-] /usr/bin/neutron-server version 8.2.0
2016-11-03 14:47:31.994 28472 INFO neutron.manager [-] Loading core plugin: ml2
2016-11-03 14:47:32.366 28472 INFO neutron.plugins.ml2.managers [-] Configured type driver names: ['flat', 'vlan', 'vxlan', 'gre']
2016-11-03 14:47:32.374 28472 INFO neutron.plugins.ml2.drivers.type_flat [-] Allowable flat physical_network names: ['external']
2016-11-03 14:47:32.477 28472 INFO neutron.plugins.ml2.drivers.type_vlan [-] Network VLAN ranges: {}
2016-11-03 14:47:32.508 28472 INFO neutron.plugins.ml2.managers [-] Loaded type driver names: ['flat', 'vlan', 'gre', 'vxlan']
2016-11-03 14:47:32.510 28472 INFO neutron.plugins.ml2.managers [-] Registered types: ['flat', 'vlan', 'gre', 'vxlan']
2016-11-03 14:47:32.512 28472 INFO neutron.plugins.ml2.managers [-] Tenant network_types: ['vlan', 'gre', 'vxlan']
2016-11-03 14:47:32.514 28472 INFO neutron.plugins.ml2.managers [-] Configured extension driver names: ['port_security']
2016-11-03 14:47:32.531 28472 INFO neutron.plugins.ml2.managers [-] Loaded extension driver names: ['port_security']
2016-11-03 14:47:32.532 28472 INFO neutron.plugins.ml2.managers [-] Registered extension drivers: ['port_security']
2016-11-03 14:47:32.534 28472 INFO neutron.plugins.ml2.managers [-] Configured mechanism driver names: ['openvswitch', 'l2population']
2016-11-03 14:47:32.545 28472 INFO neutron.plugins.ml2.managers [-] Loaded mechanism driver names: ['openvswitch', 'l2population']
2016-11-03 14:47:32.547 28472 INFO neutron.plugins.ml2.managers [-] Registered mechanism drivers: ['openvswitch', 'l2population']
2016-11-03 14:47:32.667 28472 INFO neutron.plugins.ml2.managers [-] Initializing driver for type 'flat'
2016-11-03 14:47:32.668 28472 INFO neutron.plugins.ml2.drivers.type_flat [-] ML2 FlatTypeDriver initialization complete
2016-11-03 14:47:32.669 28472 INFO neutron.plugins.ml2.managers [-] Initializing driver for type 'vlan'
2016-11-03 14:47:32.863 28472 INFO neutron.plugins.ml2.drivers.type_vlan [-] VlanTypeDriver initialization complete
2016-11-03 14:47:32.863 28472 INFO neutron.plugins.ml2.managers [-] Initializing driver for type 'gre'
2016-11-03 14:47:32.865 28472 INFO neutron.plugins.ml2.drivers.type_tunnel [-] gre ID ranges: [(300, 400)]
2016-11-03 14:47:32.876 28472 INFO neutron.plugins.ml2.managers [-] Initializing driver for type 'vxlan'
2016-11-03 14:47:32.877 28472 INFO neutron.plugins.ml2.drivers.type_tunnel [-] vxlan ID ranges: []
2016-11-03 14:47:32.884 28472 INFO neutron.plugins.ml2.managers [-] Initializing extension driver 'port_security'
2016-11-03 14:47:32.885 28472 INFO neutron.plugins.ml2.extensions.port_security [-] PortSecurityExtensionDriver initialization complete
2016-11-03 14:47:32.886 28472 INFO neutron.plugins.ml2.managers [-] Initializing mechanism driver 'openvswitch'
2016-11-03 14:47:32.888 28472 INFO neutron.plugins.ml2.managers [-] Initializing mechanism driver 'l2population'
2016-11-03 14:47:32.892 28472 INFO neutron.plugins.ml2.plugin [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Modular L2 Plugin initialization complete
2016-11-03 14:47:32.893 28472 INFO neutron.plugins.ml2.managers [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Got port-security extension from driver 'port_security'
2016-11-03 14:47:32.895 28472 INFO neutron.extensions.vlantransparent [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Disabled vlantransparent extension.
2016-11-03 14:47:32.897 28472 INFO neutron.manager [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loading Plugin: router
2016-11-03 14:47:32.952 28472 INFO neutron.db.l3_agentschedulers_db [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Skipping period L3 agent status check because automatic router rescheduling is disabled.
2016-11-03 14:47:33.014 28472 INFO neutron.manager [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loading Plugin: auto_allocate
2016-11-03 14:47:33.026 28472 INFO neutron.manager [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loading Plugin: tag
2016-11-03 14:47:33.035 28472 INFO neutron.manager [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loading Plugin: timestamp_core
2016-11-03 14:47:33.039 28472 INFO neutron.manager [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loading Plugin: network_ip_availability
2016-11-03 14:47:33.121 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Initializing extension manager.
2016-11-03 14:47:33.125 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: address-scope
2016-11-03 14:47:33.129 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: agent
2016-11-03 14:47:33.133 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: allowed-address-pairs
2016-11-03 14:47:33.137 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: auto-allocated-topology
2016-11-03 14:47:33.140 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: availability_zone
2016-11-03 14:47:33.144 28472 WARNING neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Extension bgp not supported by any of loaded plugins
2016-11-03 14:47:33.148 28472 WARNING neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Extension bgp_dragent_scheduler not supported by any of loaded plugins
2016-11-03 14:47:33.150 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: default-subnetpools
2016-11-03 14:47:33.153 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: dhcp_agent_scheduler
2016-11-03 14:47:33.155 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: dns-integration
2016-11-03 14:47:33.158 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: dvr
2016-11-03 14:47:33.160 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: external-net
2016-11-03 14:47:33.162 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: extra_dhcp_opt
2016-11-03 14:47:33.165 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: extraroute
2016-11-03 14:47:33.168 28472 WARNING neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Extension flavors not supported by any of loaded plugins
2016-11-03 14:47:33.171 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: router
2016-11-03 14:47:33.175 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: ext-gw-mode
2016-11-03 14:47:33.178 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: l3-ha
2016-11-03 14:47:33.182 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: l3_agent_scheduler
2016-11-03 14:47:33.186 28472 WARNING neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Extension metering not supported by any of loaded plugins
2016-11-03 14:47:33.189 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: multi-provider
2016-11-03 14:47:33.192 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: net-mtu
2016-11-03 14:47:33.195 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: network_availability_zone
2016-11-03 14:47:33.197 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: network-ip-availability
2016-11-03 14:47:33.200 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: binding
2016-11-03 14:47:33.204 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: port-security
2016-11-03 14:47:33.207 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: provider
2016-11-03 14:47:33.210 28472 WARNING neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Extension qos not supported by any of loaded plugins
2016-11-03 14:47:33.213 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: quotas
2016-11-03 14:47:33.217 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: rbac-policies
2016-11-03 14:47:33.220 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: router_availability_zone
2016-11-03 14:47:33.222 28472 WARNING neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Extension router-service-type not supported by any of loaded plugins
2016-11-03 14:47:33.228 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: security-group
2016-11-03 14:47:33.232 28472 WARNING neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Extension service-type not supported by any of loaded plugins
2016-11-03 14:47:33.239 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: standard-attr-description
2016-11-03 14:47:33.242 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: subnet_allocation
2016-11-03 14:47:33.245 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: tag
2016-11-03 14:47:33.248 28472 INFO neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Loaded extension: timestamp_core
2016-11-03 14:47:33.250 28472 WARNING neutron.api.extensions [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Extension vlan-transparent not supported by any of loaded plugins
2016-11-03 14:47:33.368 28472 INFO neutron.quota.resource_registry [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Creating instance of TrackedResource for resource:subnet
2016-11-03 14:47:33.447 28472 INFO neutron.quota.resource_registry [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Creating instance of TrackedResource for resource:subnetpool
2016-11-03 14:47:33.533 28472 INFO neutron.quota.resource_registry [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Creating instance of TrackedResource for resource:network
2016-11-03 14:47:33.592 28472 INFO neutron.quota.resource_registry [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Creating instance of TrackedResource for resource:port
2016-11-03 14:47:33.955 28472 INFO neutron.quota.resource_registry [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Creating instance of TrackedResource for resource:router
2016-11-03 14:47:34.013 28472 INFO neutron.quota.resource_registry [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Creating instance of TrackedResource for resource:floatingip
2016-11-03 14:47:34.073 28472 INFO neutron.quota.resource_registry [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Creating instance of TrackedResource for resource:security_group
2016-11-03 14:47:34.155 28472 INFO neutron.quota.resource_registry [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Creating instance of TrackedResource for resource:security_group_rule
2016-11-03 14:47:34.265 28472 INFO neutron.quota.resource_registry [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Creating instance of CountableResource for resource:rbac_policy
2016-11-03 14:47:34.364 28472 INFO oslo_service.service [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Starting 2 workers
2016-11-03 14:47:34.377 28496 INFO neutron.wsgi [-] (28496) wsgi starting up on http://0.0.0.0:9696
2016-11-03 14:47:34.381 28497 INFO neutron.wsgi [-] (28497) wsgi starting up on http://0.0.0.0:9696
2016-11-03 14:47:34.373 28472 INFO neutron.service [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Neutron service started, listening on 0.0.0.0:9696
2016-11-03 14:47:34.388 28472 INFO oslo_service.service [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Starting 1 workers
2016-11-03 14:47:34.400 28472 INFO oslo_service.service [req-b1b3c599-4995-4cc7-a954-3a1bdcfde8d9 - - - - -] Starting 1 workers
```

# LOG cấu hình neutron

Sau khi kiểm tra các log restart lại Neutron và các agent, ta chuyển sang các log của event tạo, sửa, xóa network; tạo, xóa instance

Event tạo network
----

- Khi login qua Dashboard, sẽ có log call tới API của Neutron server
```sh
2016-11-03 14:58:55.580 28497 INFO neutron.wsgi [req-dd02ca0a-55bd-421a-9ca3-970d07db42ff fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 14:58:55] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 19.240536
```

- Log trong file **/var/log/neutron/neutron-server.log** khi có các thao tác qua Dashboard:
	- tạo security group; click *Access & Security*
```sh
2016-11-03 15:04:27.034 28497 INFO neutron.wsgi [req-c916eb4b-76bb-449f-a444-87a589c80465 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:27] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 2.396602
2016-11-03 15:04:28.395 28497 INFO neutron.wsgi [req-028f8b25-1abb-47bc-b793-f00759e8dc3b fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:28] "GET /v2.0/security-groups.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 1841 1.327380
2016-11-03 15:04:38.981 28497 INFO neutron.wsgi [req-b2b713e6-9daa-43a8-ba45-ecfb78296e21 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:38] "GET /v2.0/floatingips.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 232 0.346900
2016-11-03 15:04:40.350 28496 INFO neutron.wsgi [req-024a91f9-6611-402d-a34b-4aaedc6bed4f fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:40] "GET /v2.0/ports.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 226 1.222667
2016-11-03 15:04:41.478 28497 INFO neutron.wsgi [req-d6003968-8f2e-45f8-a696-5783b73a1531 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:41] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 229 1.136155
2016-11-03 15:04:43.520 28496 INFO neutron.wsgi [req-831443f6-8ee7-49e6-844f-beb93a20bf32 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:43] "GET /v2.0/quotas/3262c9a75a494be288b4d77608334e0d.json HTTP/1.1" 200 385 0.160731
2016-11-03 15:04:45.553 28497 INFO neutron.wsgi [req-6b25611e-b7d3-4e77-89b9-008ba455ca83 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:45] "GET /v2.0/floatingips.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 232 0.041284
2016-11-03 15:04:45.800 28496 INFO neutron.wsgi [req-00d028a0-7bfd-454a-b914-09602d8dfc29 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:45] "GET /v2.0/ports.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 226 0.249942
2016-11-03 15:04:45.987 28497 INFO neutron.wsgi [req-8d565ce9-ce70-4eb8-9ce4-d1310bcf069b fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:45] "GET /v2.0/security-groups.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 1841 0.174525
2016-11-03 15:04:46.699 28496 INFO neutron.wsgi [req-13c2cfc5-f54b-44a2-bdbb-3d9d77027ca6 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:46] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 229 0.687022
2016-11-03 15:04:52.397 28497 INFO neutron.wsgi [req-35041703-861c-4cb0-80f6-52fba9512f8e fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:52] "GET /v2.0/subnets.json HTTP/1.1" 200 228 5.671959
2016-11-03 15:04:52.548 28496 INFO neutron.wsgi [req-11e5bc7c-509f-49d3-9581-3eff84164919 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:52] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 229 0.135758
2016-11-03 15:04:52.764 28497 INFO neutron.wsgi [req-e52fd315-0dd7-4255-947c-862428182b4a fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:52] "GET /v2.0/subnets.json HTTP/1.1" 200 228 0.224132
2016-11-03 15:04:53.705 28496 INFO neutron.wsgi [req-79efaa7d-a884-425c-b873-fa447a394b28 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:53] "GET /v2.0/subnets.json HTTP/1.1" 200 228 0.920306
2016-11-03 15:04:53.942 28496 INFO neutron.wsgi [req-3a628268-c59f-4577-bd93-e407f9287501 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:04:53] "GET /v2.0/routers.json HTTP/1.1" 200 228 0.217524
```

	- khi click vào *Manage Security Group Rules: default*
```sh
2016-11-03 15:06:28.902 28496 INFO neutron.wsgi [req-de78f91e-dfa3-490f-a496-ff2d26f7173d fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:06:28] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.024917
2016-11-03 15:06:29.095 28496 INFO neutron.wsgi [req-7e0be8ca-25ee-4661-9b21-cdf5ef806355 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:06:29] "GET /v2.0/security-groups/35a92ab4-ed7c-4917-82f6-67a4b70db22e.json HTTP/1.1" 200 1838 0.129792
2016-11-03 15:06:29.217 28497 INFO neutron.wsgi [req-0eaa5817-df8e-446a-81ad-799d9ee96046 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:06:29] "GET /v2.0/security-groups.json?fields=id&fields=name&id=35a92ab4-ed7c-4917-82f6-67a4b70db22e HTTP/1.1" 200 301 0.115681
```

	- click *Add Rule*
```sh
2016-11-03 15:08:10.126 28497 INFO neutron.wsgi [req-7edc3b63-93be-48d3-b42c-ed71e3d7cca7 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:08:10] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.013524
2016-11-03 15:08:10.342 28496 INFO neutron.wsgi [req-4db48cbe-f3f6-49ef-80eb-21082e7bbb56 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:08:10] "GET /v2.0/security-groups.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 1841 0.190224
```

	- add ingress any 0/0
```sh
2016-11-03 15:12:23.359 28496 INFO neutron.wsgi [req-9dcd9bf7-983b-41c6-9264-5ef9921e675f fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:12:23] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 3.836252
2016-11-03 15:12:23.560 28496 INFO neutron.wsgi [req-2b19fdee-b752-44a5-93ac-ff3f16270c56 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:12:23] "GET /v2.0/security-groups.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 1841 0.179114
2016-11-03 15:12:23.602 28496 INFO neutron.quota [req-69e8bd0d-7df4-461e-a4c7-3fb9985f7867 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] Loaded quota_driver: <neutron.db.quota.driver.DbQuotaDriver object at 0x7ff5c0073850>.
2016-11-03 15:12:24.475 28496 INFO neutron.wsgi [req-69e8bd0d-7df4-461e-a4c7-3fb9985f7867 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:12:24] "POST /v2.0/security-group-rules.json HTTP/1.1" 201 588 0.883664
2016-11-03 15:12:24.781 28497 INFO neutron.wsgi [req-ab3846dc-accd-46ce-9e86-2d67fb585c85 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:12:24] "GET /v2.0/security-groups.json?fields=id&fields=name&id=35a92ab4-ed7c-4917-82f6-67a4b70db22e HTTP/1.1" 200 301 0.263854
2016-11-03 15:12:24.961 28496 INFO neutron.wsgi [req-6ccb5d39-01f4-4c05-8a6d-73a6df2d188d fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:12:24] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.015335
2016-11-03 15:12:25.111 28497 INFO neutron.wsgi [req-2445f354-ac6a-42bb-ba6f-aa6800abc47b fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:12:25] "GET /v2.0/security-groups/35a92ab4-ed7c-4917-82f6-67a4b70db22e.json HTTP/1.1" 200 2184 0.130130
2016-11-03 15:12:25.346 28497 INFO neutron.wsgi [req-2a76a542-5048-487d-b4ba-d7cc56d2f28f fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:12:25] "GET /v2.0/security-groups.json?fields=id&fields=name&id=35a92ab4-ed7c-4917-82f6-67a4b70db22e HTTP/1.1" 200 301 0.214205
```

	Ngoài ra xuất hiện log tại các file **/var/log/neutron/openvswitch-agent.log** trên Controller node
```sh
2016-11-03 15:12:24.314 27907 INFO neutron.agent.securitygroups_rpc [req-69e8bd0d-7df4-461e-a4c7-3fb9985f7867 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] Security group rule updated [u'35a92ab4-ed7c-4917-82f6-67a4b70db22e']
```

	Và log tại file **/var/log/neutron/openvswitch-agent.log** trên Compute node:
```sh
2016-10-27 15:18:18.812 65813 INFO neutron.agent.securitygroups_rpc [req-69e8bd0d-7df4-461e-a4c7-3fb9985f7867 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] Security group rule updated [u'35a92ab4-ed7c-4917-82f6-67a4b70db22e']
```

	- click *Networks* trong tab Admin:
```sh
2016-11-03 15:15:52.006 28496 INFO neutron.wsgi [req-8cc934a7-9a51-49ef-b0ae-7cfb4f2e279d fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:15:52] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.044681
2016-11-03 15:15:52.228 28497 INFO neutron.wsgi [req-ae7c80e6-3936-4da3-8231-73c86247e3c9 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:15:52] "GET /v2.0/networks.json HTTP/1.1" 200 229 0.197492
2016-11-03 15:15:52.429 28497 INFO neutron.wsgi [req-9059fd41-225d-4c57-b38a-203093755aed fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:15:52] "GET /v2.0/subnets.json HTTP/1.1" 200 228 0.182854
```

	- click *Create network*:
```sh
2016-11-03 15:17:30.606 28496 INFO neutron.wsgi [req-8ca0cf6d-f991-4cbe-807f-481a4da70730 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:17:30] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 1.210543
```

	- Create *Network* FAIL:
```sh
2016-11-03 15:19:24.491 28496 INFO neutron.wsgi [req-be89f6a2-1afd-4c51-8776-a78bc101a71c fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:19:24] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.019059
2016-11-03 15:19:24.555 28497 INFO neutron.quota [req-0a5a435b-ab20-4966-ac94-7551c7c134ce fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] Loaded quota_driver: <neutron.db.quota.driver.DbQuotaDriver object at 0x7ff5bff12ed0>.
2016-11-03 15:19:25.003 28497 INFO neutron.api.v2.resource [req-0a5a435b-ab20-4966-ac94-7551c7c134ce fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] create failed (client error): Invalid input for operation: physical_network 'provider' unknown  for VLAN provider network.
2016-11-03 15:19:25.009 28497 INFO neutron.wsgi [req-0a5a435b-ab20-4966-ac94-7551c7c134ce fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:19:25] "POST /v2.0/networks.json HTTP/1.1" 400 386 0.470122
2016-11-03 15:19:25.147 28496 INFO neutron.wsgi [req-f55f73f3-9c20-41bc-a263-9e4e77addf46 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:19:25] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.016708
2016-11-03 15:19:25.342 28497 INFO neutron.wsgi [req-5102ff34-ec7a-4f44-9030-73771a2430f5 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:19:25] "GET /v2.0/networks.json HTTP/1.1" 200 229 0.189727
2016-11-03 15:19:25.580 28496 INFO neutron.wsgi [req-5f4bd8a8-aed7-4514-868a-6432ef82e079 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:19:25] "GET /v2.0/subnets.json HTTP/1.1" 200 228 0.228743
```

	- Create *Network* OK:
```sh
2016-11-03 15:21:45.659 28497 INFO neutron.wsgi [req-25725d04-293d-45f9-a7bb-1b6b0fb2f5f7 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:21:45] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.008045
2016-11-03 15:21:46.066 28496 INFO neutron.plugins.ml2.db [req-9baa56c3-6e45-48e1-b2e4-4798bf793028 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] Added segment 0629960f-6645-40ee-946c-166a8de42141 of type gre for network f3dfe7a0-bff6-4562-964f-5f3539b63961
2016-11-03 15:21:46.255 28496 INFO neutron.wsgi [req-9baa56c3-6e45-48e1-b2e4-4798bf793028 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:21:46] "POST /v2.0/networks.json HTTP/1.1" 201 830 0.581035
2016-11-03 15:21:46.332 28497 INFO neutron.wsgi [req-a2114a82-2d53-4ede-93aa-863cb7b91d57 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:21:46] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.016626
2016-11-03 15:21:46.697 28496 INFO neutron.wsgi [req-2cc817d9-3a8f-485c-9533-7244df3a0041 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:21:46] "GET /v2.0/networks.json HTTP/1.1" 200 828 0.358931
2016-11-03 15:21:46.897 28496 INFO neutron.wsgi [req-2d3431cd-52fc-4347-8beb-0f8ede725fd9 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:21:46] "GET /v2.0/subnets.json HTTP/1.1" 200 228 0.189064
2016-11-03 15:21:47.749 28497 INFO neutron.wsgi [req-6cec2c7a-5ccc-4657-93cf-c5cdd60b1e9d fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:21:47] "GET /v2.0/networks/f3dfe7a0-bff6-4562-964f-5f3539b63961/dhcp-agents.json HTTP/1.1" 200 227 0.064445
```

**Note**: lý do tạo network FAIL là do khi cấu hình ML2 ta đã định nghĩa ID cho các GRE, VLAN, VXLAN nên lúc tạo, phải lấy ID đúng với dải đã cấu hình. Như trong trường hợp trên 
tôi tạo ID ngoài dải sẽ báo FAIL.

	- click vào *provider* vừa tạo để cấu hình subnet:
```sh
2016-11-03 15:26:29.672 28496 INFO neutron.wsgi [req-3ec3b2f5-2fc0-4a59-ae1e-98f77f895936 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:26:29] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 1.359677
2016-11-03 15:26:29.955 28497 INFO neutron.wsgi [req-99486ff1-4261-4afc-9baf-af166ad54de7 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:26:29] "GET /v2.0/subnets.json?network_id=f3dfe7a0-bff6-4562-964f-5f3539b63961 HTTP/1.1" 200 228 0.273820
2016-11-03 15:26:30.161 28496 INFO neutron.wsgi [req-65d4bae7-4a0c-4fdf-b543-674660d05b78 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:26:30] "GET /v2.0/ports.json?network_id=f3dfe7a0-bff6-4562-964f-5f3539b63961 HTTP/1.1" 200 226 0.194044
2016-11-03 15:26:30.257 28496 INFO neutron.wsgi [req-b0b4f81b-a528-4e05-b892-6739434c50e6 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:26:30] "GET /v2.0/networks/f3dfe7a0-bff6-4562-964f-5f3539b63961/dhcp-agents.json HTTP/1.1" 200 227 0.079349
2016-11-03 15:26:30.512 28497 INFO neutron.wsgi [req-5a3f580b-8997-4d76-b347-7fdb57ad5f3d fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:26:30] "GET /v2.0/networks/f3dfe7a0-bff6-4562-964f-5f3539b63961.json HTTP/1.1" 200 825 0.241686
2016-11-03 15:26:31.955 28496 INFO neutron.wsgi [req-6e195094-1ea2-47f4-830e-5e8592a3fc3b fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:26:31] "GET /v2.0/networks/f3dfe7a0-bff6-4562-964f-5f3539b63961.json HTTP/1.1" 200 825 0.260206
```

	- click *create subnet*:
```sh
2016-11-03 15:28:00.558 28497 INFO neutron.wsgi [req-b8190184-bb05-41c7-98bf-48eac69f84d1 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:28:00] "GET /v2.0/networks/f3dfe7a0-bff6-4562-964f-5f3539b63961.json HTTP/1.1" 200 825 0.258621
2016-11-03 15:28:00.853 28497 INFO neutron.wsgi [req-8e853c88-39f4-4bd7-918e-ee2ab70ea72c fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:28:00] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.044403
2016-11-03 15:28:00.934 28497 INFO neutron.wsgi [req-a6b5c255-93dc-4885-b796-3042c763fef6 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:28:00] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.061363
```

	- click *next* sau khi điền form:
```sh
2016-11-03 15:29:33.494 28497 INFO neutron.wsgi [req-3d9a0aab-8356-485f-92bb-61ecd0432cac fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:29:33] "GET /v2.0/networks/f3dfe7a0-bff6-4562-964f-5f3539b63961.json HTTP/1.1" 200 825 1.025453
2016-11-03 15:29:33.554 28496 INFO neutron.wsgi [req-84147bb0-9957-4caa-8a24-3aa5deda4460 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:29:33] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.017067
2016-11-03 15:29:33.621 28497 INFO neutron.wsgi [req-9474843d-460c-418a-85cb-b699729c0fd3 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:29:33] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.043822
```

	- click complete subnet:
```sh
2016-11-03 15:31:03.211 28496 INFO neutron.wsgi [req-540ea07c-fc46-4f1d-9c96-399b10df6cef fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:31:03] "GET /v2.0/networks/f3dfe7a0-bff6-4562-964f-5f3539b63961.json HTTP/1.1" 200 825 0.138212
2016-11-03 15:31:03.253 28497 INFO neutron.wsgi [req-51be8164-bcfb-4da8-9f56-a5c453abcb2f fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:31:03] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.006941
2016-11-03 15:31:03.295 28497 INFO neutron.wsgi [req-88d48d2f-5ede-43ed-b4e0-b2591162fb3f fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:31:03] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.029594
2016-11-03 15:31:03.443 28496 INFO neutron.wsgi [req-c8233353-3e93-41e6-87bd-a087b1fbcd29 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:31:03] "GET /v2.0/networks/f3dfe7a0-bff6-4562-964f-5f3539b63961.json HTTP/1.1" 200 825 0.122349
2016-11-03 15:31:06.352 28497 INFO neutron.wsgi [req-a29ab490-f08a-43a6-bbc8-436e8676e224 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:31:06] "POST /v2.0/subnets.json HTTP/1.1" 201 788 2.902000
2016-11-03 15:31:08.073 28496 INFO neutron.wsgi [req-45cf9eb9-9100-41fc-9c01-9144d1744ab7 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:31:08] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.023935
2016-11-03 15:31:10.087 28497 INFO neutron.wsgi [req-8ea7be45-76b1-4a8c-b659-8efed9ec5959 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:31:10] "GET /v2.0/subnets.json?network_id=f3dfe7a0-bff6-4562-964f-5f3539b63961 HTTP/1.1" 200 786 1.952603
2016-11-03 15:31:14.904 28496 INFO neutron.wsgi [req-59fe4a63-3d18-4c42-8546-040574514d3a fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:31:14] "GET /v2.0/ports.json?network_id=f3dfe7a0-bff6-4562-964f-5f3539b63961 HTTP/1.1" 200 226 4.804635
2016-11-03 15:31:16.087 28497 INFO neutron.wsgi [req-5567cead-dfc3-4f3d-b2b9-2e922bd25845 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:31:16] "GET /v2.0/networks/f3dfe7a0-bff6-4562-964f-5f3539b63961/dhcp-agents.json HTTP/1.1" 200 787 1.164907
2016-11-03 15:31:16.616 28496 INFO neutron.wsgi [req-fdc88d99-8eb5-4e12-9e0e-b0469853c415 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:31:16] "GET /v2.0/networks/f3dfe7a0-bff6-4562-964f-5f3539b63961.json HTTP/1.1" 200 869 0.456912
2016-11-03 15:31:16.835 28497 INFO neutron.wsgi [req-131c7b23-b0c1-4786-9f23-833c8b7e3189 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:31:16] "GET /v2.0/subnets/786c63a0-18fe-4753-aab0-995df0d24a06.json HTTP/1.1" 200 783 0.222043
2016-11-03 15:31:18.292 28497 INFO neutron.wsgi [req-79db197a-beec-447a-811a-ff3e0a34c47f fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:31:18] "GET /v2.0/networks/f3dfe7a0-bff6-4562-964f-5f3539b63961.json HTTP/1.1" 200 869 0.289080
2016-11-03 15:31:18.550 28496 INFO neutron.wsgi [req-37a654d7-877f-4e7f-9198-8706d431623f fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:31:18] "GET /v2.0/subnets/786c63a0-18fe-4753-aab0-995df0d24a06.json HTTP/1.1" 200 783 0.237696
```

	Ngoài ra sau khi tạo subnet xong, cũng xuất hiện log ở một số file, **/var/log/neutron/openvswitch-agent.log** trên Controller node:
```sh
2016-11-03 15:31:19.842 27907 INFO neutron.agent.securitygroups_rpc [req-a76d0ec9-a8f3-45a9-b557-c68837a328c5 - - - - -] Provider rule updated
2016-11-03 15:31:22.398 27907 INFO neutron.agent.securitygroups_rpc [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Refresh firewall rules
2016-11-03 15:31:31.024 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Configuration for devices up [] and devices down [] completed.
2016-11-03 15:31:31.027 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Cleaning stale br-int flows
2016-11-03 15:31:31.697 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Cleaning stale br-ex flows
2016-11-03 15:31:31.874 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Cleaning stale br-tun flows
2016-11-03 15:31:33.832 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Port 7f2b8d45-a788-4f32-a5f0-b7a0bc4c663b updated. Details: {u'profile': {}, u'network_qos_policy_id': None, u'qos_policy_id': None, u'allowed_address_pairs': [], u'admin_state_up': True, u'network_id': u'f3dfe7a0-bff6-4562-964f-5f3539b63961', u'segmentation_id': 301, u'device_owner': u'network:dhcp', u'physical_network': None, u'mac_address': u'fa:16:3e:97:cf:10', u'device': u'7f2b8d45-a788-4f32-a5f0-b7a0bc4c663b', u'port_security_enabled': False, u'port_id': u'7f2b8d45-a788-4f32-a5f0-b7a0bc4c663b', u'fixed_ips': [{u'subnet_id': u'786c63a0-18fe-4753-aab0-995df0d24a06', u'ip_address': u'172.16.69.200'}], u'network_type': u'gre', u'security_groups': []}
2016-11-03 15:31:33.835 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Assigning 1 as local vlan for net-id=f3dfe7a0-bff6-4562-964f-5f3539b63961
2016-11-03 15:31:35.064 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Skipping ARP spoofing rules for port 'tap7f2b8d45-a7' because it has port security disabled
2016-11-03 15:31:36.867 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Configuration for devices up [u'7f2b8d45-a788-4f32-a5f0-b7a0bc4c663b'] and devices down [] completed.
```

	Và  **/var/log/neutron/openvswitch-agent.log** trên Compute node:
```sh
2016-10-27 15:38:48.956 65813 INFO neutron.agent.securitygroups_rpc [req-a76d0ec9-a8f3-45a9-b557-c68837a328c5 - - - - -] Provider rule updated
2016-10-27 15:38:51.864 65813 INFO neutron.agent.securitygroups_rpc [req-c5be491a-0477-4a42-8394-000e80000334 - - - - -] Refresh firewall rules
2016-10-27 15:39:01.692 65813 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-c5be491a-0477-4a42-8394-000e80000334 - - - - -] Configuration for devices up [] and devices down [] completed.
2016-10-27 15:39:01.694 65813 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-c5be491a-0477-4a42-8394-000e80000334 - - - - -] Cleaning stale br-int flows
2016-10-27 15:39:01.970 65813 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-c5be491a-0477-4a42-8394-000e80000334 - - - - -] Cleaning stale br-tun flows
```

	- Create networks trong tab *Project -> Netowrk -> Networks*:
```sh
2016-11-03 15:42:01.179 28497 INFO neutron.wsgi [req-5b017814-444d-4921-b88c-132f3c945eef fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:01] "GET /v2.0/networks.json?shared=False&tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 229 3.820678
2016-11-03 15:42:01.697 28496 INFO neutron.wsgi [req-ae5318b1-4424-4708-a557-de959bbf2110 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:01] "GET /v2.0/subnets.json HTTP/1.1" 200 786 0.452804
2016-11-03 15:42:01.934 28497 INFO neutron.wsgi [req-9d8b924a-eb28-4315-8731-3e29911be8e7 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:01] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 872 0.222951
2016-11-03 15:42:02.116 28496 INFO neutron.wsgi [req-51aae16d-f928-4d5f-b912-3af9767767bd fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:02] "GET /v2.0/subnets.json HTTP/1.1" 200 786 0.167662
2016-11-03 15:42:02.505 28496 INFO neutron.wsgi [req-c647a7a4-5056-45b7-8ba3-86caacc25951 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:02] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 872 0.374033
2016-11-03 15:42:02.727 28497 INFO neutron.wsgi [req-041692ca-b3f6-43e0-8987-8dc6488fe3eb fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:02] "GET /v2.0/subnets.json HTTP/1.1" 200 786 0.191295
2016-11-03 15:42:04.112 28496 INFO neutron.wsgi [req-89a964ad-3c53-4a1a-91a1-865c12218366 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:04] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.015512
2016-11-03 15:42:06.600 28497 INFO neutron.wsgi [req-7e53ad67-7f81-41c0-b967-f68fab6a3e73 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:06] "GET /v2.0/quotas/3262c9a75a494be288b4d77608334e0d.json HTTP/1.1" 200 385 0.047154
2016-11-03 15:42:09.015 28497 INFO neutron.wsgi [req-b00023fe-3adb-4f3a-a91e-a075ca7a7b50 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:09] "GET /v2.0/floatingips.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 232 0.065883
2016-11-03 15:42:09.229 28496 INFO neutron.wsgi [req-ded2120f-0eaa-45e9-8824-4c13d0057ea6 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:09] "GET /v2.0/ports.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 1099 0.202005
2016-11-03 15:42:09.500 28497 INFO neutron.wsgi [req-11588809-334a-4439-b025-d1477c54b40b fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:09] "GET /v2.0/security-groups.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 2187 0.256495
2016-11-03 15:42:09.772 28496 INFO neutron.wsgi [req-1e03165d-e91b-46b5-ac70-539007841c11 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:09] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 229 0.254364
2016-11-03 15:42:09.986 28497 INFO neutron.wsgi [req-ab081a08-06b6-4cb4-8d3f-e939c6f5bd7e fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:09] "GET /v2.0/subnets.json HTTP/1.1" 200 786 0.193575
2016-11-03 15:42:10.261 28496 INFO neutron.wsgi [req-dd443bf3-5958-4e90-9738-0af0b5a6798b fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:10] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 872 0.251111
2016-11-03 15:42:10.556 28497 INFO neutron.wsgi [req-8d2fba4d-5eac-4db5-b703-545ecee29268 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:10] "GET /v2.0/subnets.json HTTP/1.1" 200 786 0.282147
2016-11-03 15:42:10.770 28496 INFO neutron.wsgi [req-d3e25388-aa2a-4760-b4da-702fb92e998d fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:10] "GET /v2.0/subnets.json HTTP/1.1" 200 786 0.202723
2016-11-03 15:42:10.981 28497 INFO neutron.wsgi [req-ff6f124d-4463-493e-9c18-81da6c848fb6 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:42:10] "GET /v2.0/routers.json HTTP/1.1" 200 228 0.203342
```

	- click *create network*
```sh
2016-11-03 15:43:25.348 28496 INFO neutron.wsgi [req-f474693b-39cd-4e02-89dc-81367527310f fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:43:25] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.011177
2016-11-03 15:43:25.396 28497 INFO neutron.wsgi [req-27c3d9bc-878c-452b-b3b4-16a88135e685 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:43:25] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.034798
```


	- điền form rồi next:
```sh
2016-11-03 15:44:25.904 28496 INFO neutron.wsgi [req-0272fd93-b6ba-4340-9ad9-6caf7631a801 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:44:25] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.011531
2016-11-03 15:44:25.976 28497 INFO neutron.wsgi [req-1145b784-367a-4f10-84ff-57129cf06aaf fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:44:25] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.052300
```

	- điền form rồi next tiếp:
```sh
2016-11-03 15:45:42.060 28496 INFO neutron.wsgi [req-36aa25fc-0827-4304-8fbe-9f410dbe4ee7 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:45:42] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.014902
2016-11-03 15:45:42.135 28496 INFO neutron.wsgi [req-1ce3f8f3-9b64-4ccb-bc32-e1221679fffc fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:45:42] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.045305
```

	- điền form rồi create:
```sh
2016-11-03 15:46:48.452 28496 INFO neutron.wsgi [req-6add4f5c-abe6-422a-81ef-faa284f0f42a fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:48] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.032317
2016-11-03 15:46:48.544 28497 INFO neutron.wsgi [req-36a0a55c-0ae0-428e-8e06-231b1826459b fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:48] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.067998
2016-11-03 15:46:48.994 28496 INFO neutron.plugins.ml2.db [req-25fc9ab9-56d0-4844-b20d-29939263a9fb fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] Added segment 1d2b2ac5-b23e-416b-8fd3-88aee255c6f5 of type gre for network 6c3491fb-8901-4608-9e17-f70986595e23
2016-11-03 15:46:49.224 28496 INFO neutron.wsgi [req-25fc9ab9-56d0-4844-b20d-29939263a9fb fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:49] "POST /v2.0/networks.json HTTP/1.1" 201 814 0.641752
2016-11-03 15:46:51.012 28497 INFO neutron.wsgi [req-d44b710d-6ae9-45bf-8c88-bfe3d2ce5afa fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:51] "POST /v2.0/subnets.json HTTP/1.1" 201 793 1.764464
2016-11-03 15:46:51.428 28496 INFO neutron.wsgi [req-9944e15d-dd3d-4b8b-9db1-b677b6f45751 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:51] "GET /v2.0/networks.json?shared=False&tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 856 0.211996
2016-11-03 15:46:51.921 28496 INFO neutron.wsgi [req-01743f75-3b27-4d08-92b0-692bd061e0d7 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:51] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.468306
2016-11-03 15:46:52.132 28496 INFO neutron.wsgi [req-e11d15b7-1d41-44fd-8d42-f12df4d0bdb0 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:52] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 872 0.192651
2016-11-03 15:46:52.323 28497 INFO neutron.wsgi [req-d006ef15-0176-4dbb-969a-dfee9e030555 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:52] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.190432
2016-11-03 15:46:52.493 28496 INFO neutron.wsgi [req-7989e9b8-2cd7-46c3-874a-053ab3676fdc fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:52] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 872 0.175876
2016-11-03 15:46:52.874 28497 INFO neutron.wsgi [req-85f4671d-b708-47ee-b824-0eee7a3fb5a0 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:52] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.363821
2016-11-03 15:46:54.050 28496 INFO neutron.wsgi [req-2bc5fd9d-a118-47aa-b5bc-c21a29a773c0 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:54] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.038453
2016-11-03 15:46:54.508 28497 INFO neutron.wsgi [req-2d51d1e8-7d14-4b7f-b8d4-12c1eb587e20 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:54] "GET /v2.0/quotas/3262c9a75a494be288b4d77608334e0d.json HTTP/1.1" 200 385 0.040739
2016-11-03 15:46:55.285 28497 INFO neutron.wsgi [req-5ff8bebf-bf3d-4889-a4a0-5e61a77182b9 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:55] "GET /v2.0/floatingips.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 232 0.052847
2016-11-03 15:46:55.380 28496 INFO neutron.wsgi [req-b5a6cf0e-7ac3-4978-b514-196afb179f9f fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:55] "GET /v2.0/ports.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 1972 0.095669
2016-11-03 15:46:55.475 28497 INFO neutron.wsgi [req-7916a23b-7c16-42d8-ba65-e23cffb86d2d fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:55] "GET /v2.0/security-groups.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 2187 0.092865
2016-11-03 15:46:56.990 28496 INFO neutron.wsgi [req-e4c771b4-6821-4e1a-87b8-31710f87fb7e fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:56] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 856 1.521626
2016-11-03 15:46:57.373 28496 INFO neutron.wsgi [req-b6d8d4b6-fd38-4750-b54e-d698e51edef0 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:57] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.332918
2016-11-03 15:46:57.757 28497 INFO neutron.wsgi [req-0ae0256c-f5da-4545-8b16-1a02347a1031 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:57] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 872 0.373456
2016-11-03 15:46:58.027 28496 INFO neutron.wsgi [req-c0ed3937-1c68-44d8-9200-eccd6241e380 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:58] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.270484
2016-11-03 15:46:58.181 28497 INFO neutron.wsgi [req-5185e17f-3804-4c75-88ad-6fd3b74a0ff6 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:58] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.152021
2016-11-03 15:46:58.330 28496 INFO neutron.wsgi [req-7e73b444-b2bc-4791-99b2-ae3061471bf4 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:46:58] "GET /v2.0/routers.json HTTP/1.1" 200 228 0.159366
```

	**LOG khi tạo thêm 1 tenant network trong project**
```sh
2016-11-04 08:42:28.754 3268 INFO neutron.wsgi [req-a22dfb3d-0b3c-4d20-ba29-670d1f38e9eb 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:28] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.016915
2016-11-04 08:42:28.845 3275 INFO neutron.wsgi [req-fe105ffb-f0a4-4785-b0df-368a184b8e04 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:28] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.053967
2016-11-04 08:42:50.053 3275 INFO neutron.wsgi [req-135f568d-ff14-4b33-a39f-e82d38521dab 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:50] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.875719
2016-11-04 08:42:50.101 3268 INFO neutron.wsgi [req-af61bf0c-24f2-41fa-a3b9-2432df475ac4 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:50] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.040223
2016-11-04 08:42:55.270 3268 INFO neutron.wsgi [req-3a9a8f17-10a1-4772-adaa-f3c6b276bbee 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:55] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.020180
2016-11-04 08:42:55.341 3275 INFO neutron.wsgi [req-69a8b901-f030-45bf-9b70-57594ecee308 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:55] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.061664
2016-11-04 08:42:55.365 3268 INFO neutron.quota [req-7e1f25d2-e63e-48dc-941d-fa053243a4ed 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] Loaded quota_driver: <neutron.db.quota.driver.DbQuotaDriver object at 0x7faa2e434110>.
2016-11-04 08:42:55.748 3268 INFO neutron.plugins.ml2.db [req-7e1f25d2-e63e-48dc-941d-fa053243a4ed 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] Added segment a8c72379-372e-4498-bc67-9dcbe6527efb of type gre for network b3a81f00-7b15-48da-b071-2589f840deff
2016-11-04 08:42:55.909 3268 INFO neutron.wsgi [req-7e1f25d2-e63e-48dc-941d-fa053243a4ed 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:55] "POST /v2.0/networks.json HTTP/1.1" 201 818 0.551446
2016-11-04 08:42:55.953 3275 INFO neutron.quota [req-49bf4a92-c7d2-4e8d-b750-35d26ae4bfe9 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] Loaded quota_driver: <neutron.db.quota.driver.DbQuotaDriver object at 0x7faa2e0ca2d0>.
2016-11-04 08:42:56.636 3275 INFO neutron.wsgi [req-49bf4a92-c7d2-4e8d-b750-35d26ae4bfe9 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:56] "POST /v2.0/subnets.json HTTP/1.1" 201 785 0.701067
2016-11-04 08:42:57.059 3268 INFO neutron.wsgi [req-61f16ea5-8fe9-4ff0-b1a7-7eb8c93bd491 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:57] "GET /v2.0/networks.json?shared=False&tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 1489 0.347624
2016-11-04 08:42:57.295 3275 INFO neutron.wsgi [req-80ff3a3f-fed8-46fc-a5c1-9111eec72f9f 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:57] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.228418
2016-11-04 08:42:57.464 3268 INFO neutron.wsgi [req-48b46812-9c13-47b1-b031-db0e713c260e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:57] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 880 0.157075
2016-11-04 08:42:57.629 3275 INFO neutron.wsgi [req-f8d1f846-f9d2-4f2e-8227-d23df306f950 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:57] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.146145
2016-11-04 08:42:57.851 3268 INFO neutron.wsgi [req-25df5ea4-cfbc-40b4-8d36-78e933a95768 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:57] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 880 0.217298
2016-11-04 08:42:58.049 3275 INFO neutron.wsgi [req-321b0b12-fcbc-48a6-8e74-ebcf30e04e5c 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:58] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.192989
2016-11-04 08:42:58.361 3268 INFO neutron.wsgi [req-0d6c5e6b-bc3f-4598-8aaf-2d94b3f87231 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:58] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.018675
2016-11-04 08:42:58.716 3275 INFO neutron.wsgi [req-c3f7f328-3f5f-4555-84a8-0816d04e467a 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:58] "GET /v2.0/quotas/bd586f74227a48fab42852c8923c4d83.json HTTP/1.1" 200 385 0.031347
2016-11-04 08:42:59.180 3275 INFO neutron.wsgi [req-71b522eb-3572-46ed-9a74-fed72d5f8501 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:59] "GET /v2.0/ports.json?device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 1959 0.146607
2016-11-04 08:42:59.394 3275 INFO neutron.wsgi [req-90f55208-b36c-4a49-8319-28edfe49acc4 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:59] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.204368
2016-11-04 08:42:59.878 3268 INFO neutron.wsgi [req-c67c5c46-126a-4143-87bd-f9ad71e3453c 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:42:59] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 644 0.182213
2016-11-04 08:43:00.111 3275 INFO neutron.wsgi [req-78d36014-e73d-4432-a84c-75f7c5f77d3a 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:43:00] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 5420 0.221575
2016-11-04 08:43:00.295 3268 INFO neutron.wsgi [req-af4fed26-0f9e-4d1b-8272-37c2a32e7272 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:43:00] "GET /v2.0/security-groups.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 2187 0.181993
2016-11-04 08:43:00.422 3275 INFO neutron.wsgi [req-3b1fd941-1140-42cd-be20-f5a90ad556ea 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:43:00] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 1489 0.133295
2016-11-04 08:43:00.665 3268 INFO neutron.wsgi [req-eb63c261-057d-4393-8d34-e3989ce56356 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:43:00] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.251265
2016-11-04 08:43:00.955 3275 INFO neutron.wsgi [req-831c1d52-2c3f-4d05-bbf3-f1a415d59226 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:43:00] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 880 0.272090
2016-11-04 08:43:01.086 3268 INFO neutron.wsgi [req-4d4b398f-6358-4441-98a2-4d3fa72b02a6 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:43:01] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.140688
2016-11-04 08:43:01.245 3275 INFO neutron.wsgi [req-1b28d4e8-5731-4b82-ae1c-52b40afe9687 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:43:01] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.164412
2016-11-04 08:43:01.349 3268 INFO neutron.wsgi [req-e738c572-f6a8-4c1c-a1ae-937473ef3d2b 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 08:43:01] "GET /v2.0/routers.json HTTP/1.1" 200 729 0.116257


Log trong file /var/log/neutron/openvswitch-agent.log tại compute:
2016-11-04 08:42:58.500 1900 INFO neutron.agent.securitygroups_rpc [req-2e042440-cfdd-4959-98a0-c21fb11d0a7c - - - - -] Provider rule updated
2016-11-04 08:43:00.362 1900 INFO neutron.agent.securitygroups_rpc [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Refresh firewall rules
2016-11-04 08:43:01.331 1900 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Configuration for devices up [] and devices down [] completed.


Log trong file /var/log/openvswitch/ovs-vswitchd.log tại controller
2016-11-04T01:02:07.628Z|00112|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T01:02:07.766Z|00113|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T01:02:07.871Z|00114|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T01:02:07.970Z|00115|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T01:02:08.079Z|00116|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T01:02:08.181Z|00117|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T01:02:09.318Z|00118|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-04T01:02:09.371Z|00119|connmgr|INFO|br-tun<->unix: 2 flow_mods in the last 0 s (2 adds)
2016-11-04T01:02:09.503Z|00120|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 modifications)
2016-11-04T01:02:09.552Z|00121|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 modifications)
2016-11-04T01:43:00.126Z|00122|bridge|INFO|bridge br-int: added interface tap80ae3d1e-74 on port 11
2016-11-04T01:43:02.052Z|00123|netdev_linux|INFO|ioctl(SIOCGIFHWADDR) on tap80ae3d1e-74 device failed: No such device
2016-11-04T01:43:02.059Z|00124|netdev_linux|WARN|ioctl(SIOCGIFINDEX) on tap80ae3d1e-74 device failed: No such device
2016-11-04T01:43:04.963Z|00125|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 modifications)
2016-11-04T01:43:05.113Z|00126|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-04T01:43:05.595Z|00127|netdev_linux|WARN|tap80ae3d1e-74: removing policing failed: No such device
2016-11-04T01:43:06.530Z|00128|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T01:43:06.757Z|00129|ofp_util|INFO|Dropped 3 log messages in last 2460 seconds (most recently, 2459 seconds ago) due to excessive rate
2016-11-04T01:43:06.757Z|00130|ofp_util|INFO|normalization changed ofp_match, details:
2016-11-04T01:43:06.757Z|00131|ofp_util|INFO| pre: in_port=11,nw_proto=58,tp_src=136
2016-11-04T01:43:06.757Z|00132|ofp_util|INFO|post: in_port=11
2016-11-04T01:43:06.759Z|00133|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T01:43:06.948Z|00134|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T01:43:07.121Z|00135|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T01:43:07.310Z|00136|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)


Log trong file /var/log/neutron/openvswitch-agent.log tại controller
2016-11-04 08:42:58.503 2214 INFO neutron.agent.securitygroups_rpc [req-2e042440-cfdd-4959-98a0-c21fb11d0a7c - - - - -] Provider rule updated
2016-11-04 08:43:01.737 2214 INFO neutron.agent.securitygroups_rpc [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Refresh firewall rules
2016-11-04 08:43:03.109 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Configuration for devices up [] and devices down [] completed.
2016-11-04 08:43:04.814 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Port 80ae3d1e-7405-42f3-94ea-41185b2431a9 updated. Details: {u'profile': {}, u'network_qos_policy_id': None, u'qos_policy_id': None, u'allowed_address_pairs': [], u'admin_state_up': True, u'network_id': u'b3a81f00-7b15-48da-b071-2589f840deff', u'segmentation_id': 396, u'device_owner': u'network:dhcp', u'physical_network': None, u'mac_address': u'fa:16:3e:bb:78:70', u'device': u'80ae3d1e-7405-42f3-94ea-41185b2431a9', u'port_security_enabled': False, u'port_id': u'80ae3d1e-7405-42f3-94ea-41185b2431a9', u'fixed_ips': [{u'subnet_id': u'b0569f79-0201-47bd-910f-238a4fd130ce', u'ip_address': u'192.168.2.50'}], u'network_type': u'gre', u'security_groups': []}
2016-11-04 08:43:04.817 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Assigning 3 as local vlan for net-id=b3a81f00-7b15-48da-b071-2589f840deff
2016-11-04 08:43:06.328 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Skipping ARP spoofing rules for port 'tap80ae3d1e-74' because it has port security disabled
2016-11-04 08:43:08.130 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Configuration for devices up [u'80ae3d1e-7405-42f3-94ea-41185b2431a9'] and devices down [] completed.
```


	**LOG khi tạo thêm 1 router tương ứng với tenant ở trên (mục routers trong project)**
```sh
LOG trong /var/log/neutron/neutron-server.log
Click create router:
2016-11-04 09:05:11.595 17167 INFO neutron.wsgi [req-1f63c097-5db6-4687-84d0-393697ba5bac 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:05:11] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 880 1.651312
2016-11-04 09:05:12.211 17166 INFO neutron.wsgi [req-4aa2e8ab-98fe-45c6-8f56-4d84801059a8 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:05:12] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.610305

Khi create xong:
2016-11-04 09:06:13.027 17167 INFO neutron.wsgi [req-32fe1bc1-ccd1-4319-9bc3-d21683223982 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:13] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 880 0.142419
2016-11-04 09:06:13.240 17166 INFO neutron.wsgi [req-048f621f-a4d3-4e6d-b650-bb0d1b0adf35 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:13] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.192122
2016-11-04 09:06:13.279 17167 INFO neutron.quota [req-832dcc20-b614-4c4b-b956-551ed8c5d37e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] Loaded quota_driver: <neutron.db.quota.driver.DbQuotaDriver object at 0x7f89bc1af110>.
2016-11-04 09:06:13.530 17167 INFO neutron.wsgi [req-832dcc20-b614-4c4b-b956-551ed8c5d37e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:13] "POST /v2.0/routers.json HTTP/1.1" 201 552 0.268401
2016-11-04 09:06:17.117 17166 INFO neutron.wsgi [req-c64a28ff-fec9-48b6-8b9a-750bbb363e6a 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:17] "PUT /v2.0/routers/65c7d22a-3024-4a7b-a7da-1316aacff145.json HTTP/1.1" 200 728 3.570248
2016-11-04 09:06:17.487 17166 INFO neutron.wsgi [req-b0ac9ebc-2bf2-4b1c-975b-4a9b088e260d 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:17] "GET /v2.0/routers.json?tenant_id=bd586f74227a48fab42852c8923c4d83&search_opts=None HTTP/1.1" 200 1240 0.219100
2016-11-04 09:06:18.222 17166 INFO neutron.wsgi [req-5289b4c2-1b3f-461e-9289-7078f992c07a 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:18] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 880 0.711040
2016-11-04 09:06:18.497 17166 INFO neutron.wsgi [req-10a4a8f5-9cbb-4494-a420-1f5284f438d7 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:18] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.235215
2016-11-04 09:06:18.816 17167 INFO neutron.wsgi [req-451a5029-178f-49ed-9736-3ce2c5ebef16 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:18] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.014083
2016-11-04 09:06:19.576 17167 INFO neutron.wsgi [req-ba390a92-c99d-401b-8202-2f7b7d698f36 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:19] "GET /v2.0/quotas/bd586f74227a48fab42852c8923c4d83.json HTTP/1.1" 200 385 0.033270
2016-11-04 09:06:20.351 17166 INFO neutron.wsgi [req-3ef1dc8d-9af2-4d68-bb50-d24110fbce4c 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:20] "GET /v2.0/ports.json?device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 1959 0.150544
2016-11-04 09:06:20.631 17166 INFO neutron.wsgi [req-4a26733b-b8c3-47fc-9edc-92375088f176 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:20] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.266464
2016-11-04 09:06:20.823 17166 INFO neutron.wsgi [req-c8a331ac-5e5d-4f75-9836-881206f57336 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:20] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 644 0.048242
2016-11-04 09:06:20.965 17167 INFO neutron.wsgi [req-0e282640-bdb3-4916-9ca8-864b2ad2583b 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:20] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 5422 0.131247
2016-11-04 09:06:21.052 17166 INFO neutron.wsgi [req-2b44fa37-ec3c-4f1c-8082-b877e6ecad72 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:21] "GET /v2.0/security-groups.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 2187 0.087389
2016-11-04 09:06:21.222 17167 INFO neutron.wsgi [req-2e6c7e5d-6e63-43f1-87b9-33ee5e431774 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:21] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 1489 0.180343
2016-11-04 09:06:21.329 17166 INFO neutron.wsgi [req-4b489600-0e22-42e5-92e6-df88d4cc84ec 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:21] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.114653
2016-11-04 09:06:21.426 17167 INFO neutron.wsgi [req-bef97e6d-7978-4c83-9f05-24a41bb2c440 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:21] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 880 0.105219
2016-11-04 09:06:21.541 17166 INFO neutron.wsgi [req-6d87b46e-68e6-4fea-93e7-f4e63e78383d 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:21] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.117108
2016-11-04 09:06:22.455 17167 INFO neutron.wsgi [req-49680516-779f-40af-9ac1-191cfe6f4a54 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:22] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.917939
2016-11-04 09:06:22.668 17166 INFO neutron.wsgi [req-25d9b466-94e6-42b5-abd9-a0de0bc0fb93 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:06:22] "GET /v2.0/routers.json HTTP/1.1" 200 1240 0.202455

LOG tại file /var/log/openvswitch/ovs-vswitchd.log
2016-11-04T02:06:23.268Z|00137|bridge|INFO|bridge br-int: added interface qg-654db38f-db on port 12
2016-11-04T02:06:24.110Z|00138|netdev_linux|INFO|ioctl(SIOCGIFHWADDR) on qg-654db38f-db device failed: No such device
2016-11-04T02:06:24.161Z|00139|netdev_linux|WARN|ioctl(SIOCGIFINDEX) on qg-654db38f-db device failed: No such device
2016-11-04T02:06:25.875Z|00140|netdev_linux|WARN|qg-654db38f-db: removing policing failed: No such device
2016-11-04T02:06:26.873Z|00141|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T02:06:27.016Z|00142|ofp_util|INFO|normalization changed ofp_match, details:
2016-11-04T02:06:27.016Z|00143|ofp_util|INFO| pre: in_port=12,nw_proto=58,tp_src=136
2016-11-04T02:06:27.016Z|00144|ofp_util|INFO|post: in_port=12
2016-11-04T02:06:27.016Z|00145|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T02:06:27.271Z|00146|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T02:06:27.507Z|00147|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T02:06:27.778Z|00148|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)

LOG tại file /var/log/neutron/openvswitch-agent.log
2016-11-04 09:06:25.589 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Port 654db38f-db9e-4027-8151-609ede79c174 updated. Details: {u'profile': {}, u'network_qos_policy_id': None, u'qos_policy_id': None, u'allowed_address_pairs': [], u'admin_state_up': True, u'network_id': u'd3107881-083f-4bf7-a8b5-d8f0b3a46388', u'segmentation_id': None, u'device_owner': u'network:router_gateway', u'physical_network': u'external', u'mac_address': u'fa:16:3e:b0:d1:4e', u'device': u'654db38f-db9e-4027-8151-609ede79c174', u'port_security_enabled': False, u'port_id': u'654db38f-db9e-4027-8151-609ede79c174', u'fixed_ips': [{u'subnet_id': u'cf7096d2-5a2f-40d6-b462-3ed605a677ff', u'ip_address': u'172.16.69.204'}], u'network_type': u'flat', u'security_groups': []}
2016-11-04 09:06:26.651 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Skipping ARP spoofing rules for port 'qg-654db38f-db' because it has port security disabled
2016-11-04 09:06:28.572 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Configuration for devices up [u'654db38f-db9e-4027-8151-609ede79c174'] and devices down [] completed.
```

	**LOG khi chỉnh sửa với router ứng với tenant vừa tạo trên**
```sh
LOG trong file /var/log/neutron/neutron-server.log khi:
click router-tenant1
2016-11-04 09:08:50.966 17167 INFO neutron.wsgi [req-9d37daf1-7057-45b3-b0b7-919d65d424db 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:08:50] "GET /v2.0/routers/65c7d22a-3024-4a7b-a7da-1316aacff145.json HTTP/1.1" 200 734 0.160467
2016-11-04 09:08:51.082 17166 INFO neutron.wsgi [req-43100328-285a-4759-9639-8ed0505cbb3e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:08:51] "GET /v2.0/networks/d3107881-083f-4bf7-a8b5-d8f0b3a46388.json HTTP/1.1" 200 877 0.117542
2016-11-04 09:08:51.164 17167 INFO neutron.wsgi [req-97010a84-e866-47c8-acfe-852463e18b82 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:08:51] "GET /v2.0/ports.json?device_id=65c7d22a-3024-4a7b-a7da-1316aacff145 HTTP/1.1" 200 1036 0.075162
2016-11-04 09:08:51.204 17166 INFO neutron.wsgi [req-542cf006-f87c-4183-b228-3ca48ae50146 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:08:51] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.022501

Click add interface:
2016-11-04 09:10:39.286 17167 INFO neutron.wsgi [req-ddc141eb-5e23-480e-b900-10abeb049c81 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:10:39] "GET /v2.0/routers/65c7d22a-3024-4a7b-a7da-1316aacff145.json HTTP/1.1" 200 734 1.496620
2016-11-04 09:10:39.666 17167 INFO neutron.wsgi [req-cef8fc99-0fa8-4837-97ae-cc5ab1edae52 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:10:39] "GET /v2.0/networks.json?shared=False&tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 1489 0.332266
2016-11-04 09:10:39.906 17166 INFO neutron.wsgi [req-dc73d41b-1689-4ad5-a380-41eea1f75cac 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:10:39] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.218704
2016-11-04 09:10:40.529 17167 INFO neutron.wsgi [req-eac9bc35-b765-4ab5-aed6-cfb4322920ca 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:10:40] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 880 0.607590
2016-11-04 09:10:40.780 17166 INFO neutron.wsgi [req-777735d7-6d7a-4d44-a94d-0674f6dc5a2e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:10:40] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.231230
2016-11-04 09:10:40.880 17167 INFO neutron.wsgi [req-3c665319-de6e-465d-b296-b26e262c4259 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:10:40] "GET /v2.0/ports.json?device_id=65c7d22a-3024-4a7b-a7da-1316aacff145 HTTP/1.1" 200 1036 0.088172

Click submit
2016-11-04 09:11:42.002 17166 INFO neutron.wsgi [req-98de5d67-cf93-476d-80a1-8dc8119ed1db 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:11:42] "GET /v2.0/routers/65c7d22a-3024-4a7b-a7da-1316aacff145.json HTTP/1.1" 200 734 0.127822
2016-11-04 09:11:42.188 17167 INFO neutron.wsgi [req-2a1d7b77-c74c-4556-b097-8adbef6480c3 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:11:42] "GET /v2.0/networks.json?shared=False&tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 1489 0.169508
2016-11-04 09:11:42.352 17166 INFO neutron.wsgi [req-cdfba88d-1d90-486b-889f-e718b231f2fc 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:11:42] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.151440
2016-11-04 09:11:42.526 17167 INFO neutron.wsgi [req-f58605a3-7299-4345-8cad-a9dd73f958d7 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:11:42] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 880 0.152534
2016-11-04 09:11:42.673 17166 INFO neutron.wsgi [req-03f39a0d-6ef3-4c23-a8fa-5aa39fb4e694 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:11:42] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.133513
2016-11-04 09:11:42.801 17167 INFO neutron.wsgi [req-4e2466ef-0b55-40e4-9f5f-70842ec77860 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:11:42] "GET /v2.0/ports.json?device_id=65c7d22a-3024-4a7b-a7da-1316aacff145 HTTP/1.1" 200 1036 0.107339
2016-11-04 09:11:44.982 17166 INFO neutron.wsgi [req-5d685a16-d1c6-40d9-a32c-3848b6d9b427 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:11:44] "PUT /v2.0/routers/65c7d22a-3024-4a7b-a7da-1316aacff145/add_router_interface.json HTTP/1.1" 200 523 2.160275
2016-11-04 09:11:45.235 17166 INFO neutron.wsgi [req-692d6446-5031-4ea5-877b-4bd061bfb39c 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:11:45] "GET /v2.0/ports/8aece323-4060-479d-9a3e-458d68e13be1.json HTTP/1.1" 200 1012 0.248104
2016-11-04 09:11:45.853 17166 INFO neutron.wsgi [req-ddde55d3-7932-4a18-8855-48e85aad782e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:11:45] "GET /v2.0/routers/65c7d22a-3024-4a7b-a7da-1316aacff145.json HTTP/1.1" 200 734 0.524346
2016-11-04 09:11:46.241 17166 INFO neutron.wsgi [req-b1bac714-18a5-435b-99c4-5a8dbd94ae3e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:11:46] "GET /v2.0/networks/d3107881-083f-4bf7-a8b5-d8f0b3a46388.json HTTP/1.1" 200 877 0.353994
2016-11-04 09:11:46.365 17167 INFO neutron.wsgi [req-c7cc3e55-4c42-4352-a096-698f4cf94c88 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:11:46] "GET /v2.0/ports.json?device_id=65c7d22a-3024-4a7b-a7da-1316aacff145 HTTP/1.1" 200 1827 0.120915
2016-11-04 09:11:46.731 17166 INFO neutron.wsgi [req-0daa67c7-1d0d-42c7-b5c2-f8c9e5e5dfe8 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:11:46] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.021809

Log tương ứng tại file /var/log/openvswitch/ovs-vswitchd.log controller
2016-11-04T02:11:49.244Z|00149|bridge|INFO|bridge br-int: added interface qr-8aece323-40 on port 13
2016-11-04T02:11:49.942Z|00150|netdev_linux|WARN|qr-8aece323-40: removing policing failed: No such device
2016-11-04T02:11:49.986Z|00151|netdev_linux|INFO|ioctl(SIOCGIFHWADDR) on qr-8aece323-40 device failed: No such device
2016-11-04T02:11:50.064Z|00152|netdev_linux|WARN|ioctl(SIOCGIFINDEX) on qr-8aece323-40 device failed: No such device
2016-11-04T02:11:51.108Z|00153|netdev_linux|WARN|qr-8aece323-40: removing policing failed: No such device
2016-11-04T02:11:52.453Z|00154|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T02:11:52.646Z|00155|ofp_util|INFO|normalization changed ofp_match, details:
2016-11-04T02:11:52.646Z|00156|ofp_util|INFO| pre: in_port=13,nw_proto=58,tp_src=136
2016-11-04T02:11:52.646Z|00157|ofp_util|INFO|post: in_port=13
2016-11-04T02:11:52.647Z|00158|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T02:11:52.850Z|00159|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T02:11:52.995Z|00160|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T02:11:53.311Z|00161|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)

Log tương ứng tại file  /var/log/neutron/openvswitch-agent.log trong controller
2016-11-04 09:11:50.675 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Port 8aece323-4060-479d-9a3e-458d68e13be1 updated. Details: {u'profile': {}, u'network_qos_policy_id': None, u'qos_policy_id': None, u'allowed_address_pairs': [], u'admin_state_up': True, u'network_id': u'b3a81f00-7b15-48da-b071-2589f840deff', u'segmentation_id': 396, u'device_owner': u'network:router_interface', u'physical_network': None, u'mac_address': u'fa:16:3e:5f:f2:b7', u'device': u'8aece323-4060-479d-9a3e-458d68e13be1', u'port_security_enabled': False, u'port_id': u'8aece323-4060-479d-9a3e-458d68e13be1', u'fixed_ips': [{u'subnet_id': u'b0569f79-0201-47bd-910f-238a4fd130ce', u'ip_address': u'192.168.2.1'}], u'network_type': u'gre', u'security_groups': []}
2016-11-04 09:11:51.440 2214 INFO neutron.agent.securitygroups_rpc [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Refresh firewall rules
2016-11-04 09:11:52.257 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Skipping ARP spoofing rules for port 'qr-8aece323-40' because it has port security disabled
2016-11-04 09:11:53.830 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Configuration for devices up [u'8aece323-4060-479d-9a3e-458d68e13be1'] and devices down [] completed.

Log tương ứng tại file /var/log/neutron/l3-agent.log trong controller:
2016-11-04 09:11:52.451 2219 INFO neutron.agent.linux.interface [-] Device qg-654db38f-db already exists

Log tương ứng tại file /var/log/neutron/dhcp-agent.log trong controller
2016-11-04 09:11:44.966 2218 INFO neutron.agent.dhcp.agent [req-5d685a16-d1c6-40d9-a32c-3848b6d9b427 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] Trigger reload_allocations for port admin_state_up=True, allowed_address_pairs=[], binding:host_id=, binding:profile=, binding:vif_details=, binding:vif_type=unbound, binding:vnic_type=normal, created_at=2016-11-04T02:11:43, description=, device_id=65c7d22a-3024-4a7b-a7da-1316aacff145, device_owner=network:router_interface, dns_name=None, extra_dhcp_opts=[], fixed_ips=[{u'subnet_id': u'b0569f79-0201-47bd-910f-238a4fd130ce', u'ip_address': u'192.168.2.1'}], id=8aece323-4060-479d-9a3e-458d68e13be1, mac_address=fa:16:3e:5f:f2:b7, name=, network_id=b3a81f00-7b15-48da-b071-2589f840deff, port_security_enabled=False, security_groups=[], status=DOWN, tenant_id=bd586f74227a48fab42852c8923c4d83, updated_at=2016-11-04T02:11:43
```

	Ngoài ra, sau khi create sẽ xuất hiện log tại **/var/log/neutron/openvswitch-agent.log** trên Controller node:
```sh
2016-11-03 15:46:53.486 27907 INFO neutron.agent.securitygroups_rpc [req-a76d0ec9-a8f3-45a9-b557-c68837a328c5 - - - - -] Provider rule updated
2016-11-03 15:46:58.062 27907 INFO neutron.agent.securitygroups_rpc [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Refresh firewall rules
2016-11-03 15:46:59.227 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Configuration for devices up [] and devices down [] completed.
2016-11-03 15:47:00.989 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Port 1bad3ac7-90f3-465d-bfea-1c3680fb4d5c updated. Details: {u'profile': {}, u'network_qos_policy_id': None, u'qos_policy_id': None, u'allowed_address_pairs': [], u'admin_state_up': True, u'network_id': u'6c3491fb-8901-4608-9e17-f70986595e23', u'segmentation_id': 385, u'device_owner': u'network:dhcp', u'physical_network': None, u'mac_address': u'fa:16:3e:9b:de:b2', u'device': u'1bad3ac7-90f3-465d-bfea-1c3680fb4d5c', u'port_security_enabled': False, u'port_id': u'1bad3ac7-90f3-465d-bfea-1c3680fb4d5c', u'fixed_ips': [{u'subnet_id': u'acb835d4-cb3d-44a9-a554-50f1b9f08556', u'ip_address': u'192.168.10.50'}], u'network_type': u'gre', u'security_groups': []}
2016-11-03 15:47:00.993 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Assigning 2 as local vlan for net-id=6c3491fb-8901-4608-9e17-f70986595e23
2016-11-03 15:47:02.171 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Skipping ARP spoofing rules for port 'tap1bad3ac7-90' because it has port security disabled
2016-11-03 15:47:04.235 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Configuration for devices up [u'1bad3ac7-90f3-465d-bfea-1c3680fb4d5c'] and devices down [] completed.
```

	Log tại **/var/log/openvswitch/ovs-vswitchd.log**  trên Controller node:
```sh
2016-11-03T08:46:55.594Z|00186|bridge|INFO|bridge br-int: added interface tap1bad3ac7-90 on port 4
2016-11-03T08:46:58.956Z|00187|netdev_linux|WARN|tap1bad3ac7-90: removing policing failed: No such device
2016-11-03T08:46:58.995Z|00188|netdev_linux|INFO|ioctl(SIOCGIFHWADDR) on tap1bad3ac7-90 device failed: No such device
2016-11-03T08:46:59.053Z|00189|netdev_linux|WARN|ioctl(SIOCGIFINDEX) on tap1bad3ac7-90 device failed: No such device
2016-11-03T08:47:01.170Z|00190|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-03T08:47:01.572Z|00191|netdev_linux|WARN|tap1bad3ac7-90: removing policing failed: No such device
2016-11-03T08:47:02.396Z|00192|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-03T08:47:02.541Z|00193|ofp_util|INFO|normalization changed ofp_match, details:
2016-11-03T08:47:02.541Z|00194|ofp_util|INFO| pre: in_port=4,nw_proto=58,tp_src=136
2016-11-03T08:47:02.541Z|00195|ofp_util|INFO|post: in_port=4
2016-11-03T08:47:02.541Z|00196|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-03T08:47:02.713Z|00197|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-03T08:47:02.954Z|00198|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-03T08:47:03.230Z|00199|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
```	

	Và log tại **/var/log/neutron/openvswitch-agent.log** trên Compute node:
```sh
2016-10-27 15:55:40.370 65813 INFO neutron.agent.securitygroups_rpc [req-a76d0ec9-a8f3-45a9-b557-c68837a328c5 - - - - -] Provider rule updated
2016-10-27 15:55:43.916 65813 INFO neutron.agent.securitygroups_rpc [req-c5be491a-0477-4a42-8394-000e80000334 - - - - -] Refresh firewall rules
2016-10-27 15:55:45.550 65813 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-c5be491a-0477-4a42-8394-000e80000334 - - - - -] Configuration for devices up [] and devices down [] completed.
```

	- click router trong *project -> network -> routers*
```sh
2016-11-03 15:51:28.339 28497 INFO neutron.wsgi [req-dd959ab4-0c0d-4c08-ae5f-0ab7617e1de3 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:28] "GET /v2.0/routers.json?tenant_id=3262c9a75a494be288b4d77608334e0d&search_opts=None HTTP/1.1" 200 228 3.178261
2016-11-03 15:51:28.549 28497 INFO neutron.wsgi [req-21f2309c-1675-4892-b7c2-814b70d881d3 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:28] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 872 0.198901
2016-11-03 15:51:28.753 28496 INFO neutron.wsgi [req-e90383f8-2800-4cb4-82c4-d17f669f078c fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:28] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.186982
2016-11-03 15:51:30.027 28496 INFO neutron.wsgi [req-b843c592-afb7-4c3c-b68f-258d1ddd646c fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:30] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.015445
2016-11-03 15:51:30.451 28497 INFO neutron.wsgi [req-4036446d-45bc-48f1-93c7-edd5e98ec895 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:30] "GET /v2.0/quotas/3262c9a75a494be288b4d77608334e0d.json HTTP/1.1" 200 385 0.021634
2016-11-03 15:51:31.332 28496 INFO neutron.wsgi [req-812a935b-2ca1-4c37-b26c-22b5d1576515 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:31] "GET /v2.0/floatingips.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 232 0.075506
2016-11-03 15:51:31.497 28497 INFO neutron.wsgi [req-15680a05-b9b7-465e-9de4-a93f77948a3b fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:31] "GET /v2.0/ports.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 1974 0.140063
2016-11-03 15:51:31.696 28496 INFO neutron.wsgi [req-cdff2bd3-e706-4a8a-9508-d2f53f2812fa fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:31] "GET /v2.0/security-groups.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 2187 0.147500
2016-11-03 15:51:32.208 28497 INFO neutron.wsgi [req-c8eac099-98f1-47c3-a6cd-d1638a68914d fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:32] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 856 0.340943
2016-11-03 15:51:32.406 28496 INFO neutron.wsgi [req-3c626c3b-7e64-4708-82f7-c92297e4c517 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:32] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.165941
2016-11-03 15:51:32.564 28497 INFO neutron.wsgi [req-e8a234f7-5fb7-4f83-be54-7e24df54490e fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:32] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 872 0.144523
2016-11-03 15:51:32.689 28496 INFO neutron.wsgi [req-7bd749f0-d0fc-46fb-a8fb-93637d4c99da fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:32] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.094571
2016-11-03 15:51:32.845 28496 INFO neutron.wsgi [req-0501b973-acb9-4775-a880-aa146a60b251 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:32] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.149555
2016-11-03 15:51:33.044 28497 INFO neutron.wsgi [req-ec38fd96-aeb5-4708-a94f-fc254b4747ee fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:51:33] "GET /v2.0/routers.json HTTP/1.1" 200 228 0.181362
```

	- click create router:
```sh
2016-11-03 15:52:03.865 28496 INFO neutron.wsgi [req-7ce1f8a5-769b-4ebd-b84d-75600253a80f fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:52:03] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 872 0.209526
2016-11-03 15:52:04.068 28497 INFO neutron.wsgi [req-cec8f0bd-251d-4251-ae8b-08e8984ba125 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:52:04] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.192823
```

	- create router xong:
```sh
2016-11-03 15:52:58.752 28497 INFO neutron.wsgi [req-e04625df-1077-4d54-a39f-5c4eabcb1063 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:52:58] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 872 0.408361
2016-11-03 15:52:58.973 28496 INFO neutron.wsgi [req-54ebfd51-2828-428b-afbc-37a4e1c5e04e fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:52:58] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.204896
2016-11-03 15:52:59.360 28497 INFO neutron.wsgi [req-999d5d81-29e2-4728-89db-b6ba50dfaba0 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:52:59] "POST /v2.0/routers.json HTTP/1.1" 201 544 0.373011
2016-11-03 15:53:01.597 28496 INFO neutron.wsgi [req-53339ea2-dc2f-45d3-a5b6-1529586ea11d fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:01] "PUT /v2.0/routers/119cbe06-6852-443f-8118-7f35e19c3abe.json HTTP/1.1" 200 720 2.236887
2016-11-03 15:53:02.147 28496 INFO neutron.wsgi [req-41797d47-2bfb-4040-8bd5-a585a062d699 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:02] "GET /v2.0/routers.json?tenant_id=3262c9a75a494be288b4d77608334e0d&search_opts=None HTTP/1.1" 200 729 0.256946
2016-11-03 15:53:02.540 28497 INFO neutron.wsgi [req-d70e439e-3234-4cfb-af4a-5cdf45481020 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:02] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 872 0.390940
2016-11-03 15:53:02.826 28496 INFO neutron.wsgi [req-7cd50c2f-8c57-48ec-9c33-f7967e3dd877 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:02] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.283745
2016-11-03 15:53:03.412 28497 INFO neutron.wsgi [req-a6dc7b54-3ecd-4af1-bb0b-66ceec0fc104 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:03] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.017687
2016-11-03 15:53:03.786 28497 INFO neutron.wsgi [req-51f0b524-da01-42ee-b434-7078932195fc fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:03] "GET /v2.0/quotas/3262c9a75a494be288b4d77608334e0d.json HTTP/1.1" 200 385 0.023266
2016-11-03 15:53:04.782 28497 INFO neutron.wsgi [req-7834f317-c801-4a42-8cda-4c742aa2ad45 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:04] "GET /v2.0/floatingips.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 232 0.125353
2016-11-03 15:53:04.937 28496 INFO neutron.wsgi [req-797e5bc8-a110-4095-830c-35fd4900b266 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:04] "GET /v2.0/ports.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 1974 0.151827
2016-11-03 15:53:05.120 28497 INFO neutron.wsgi [req-4d9a5e31-fc30-4ddc-b095-628fa8ad05ed fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:05] "GET /v2.0/security-groups.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 2187 0.178625
2016-11-03 15:53:05.460 28496 INFO neutron.wsgi [req-72bb416c-e841-47cd-a23c-6b8137133f4c fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:05] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 856 0.346213
2016-11-03 15:53:05.707 28497 INFO neutron.wsgi [req-8bc46855-751b-44e2-b65b-dbec4c3ba1c2 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:05] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.238371
2016-11-03 15:53:06.432 28496 INFO neutron.wsgi [req-059f99e9-cb20-4d0c-a08b-299d4c950b28 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:06] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 872 0.710114
2016-11-03 15:53:06.574 28497 INFO neutron.wsgi [req-33fea946-8dc5-4513-bace-98a39c999fcb fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:06] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.129624
2016-11-03 15:53:06.726 28496 INFO neutron.wsgi [req-a2c3ecde-5cee-4876-811e-b93b154be032 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:06] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.151178
2016-11-03 15:53:06.995 28497 INFO neutron.wsgi [req-ba631bec-1e3e-46c8-be50-1b9242b13688 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:53:06] "GET /v2.0/routers.json HTTP/1.1" 200 729 0.238078
```

	Ngoài ra, sau khi create sẽ xuất hiện log tại **/var/log/neutron/openvswitch-agent.log** trên Controller node:
```sh
2016-11-03 15:53:15.921 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Port f572d4d3-e16c-4ca0-97c6-6c3b2c94e5c3 updated. Details: {u'profile': {}, u'network_qos_policy_id': None, u'qos_policy_id': None, u'allowed_address_pairs': [], u'admin_state_up': True, u'network_id': u'f3dfe7a0-bff6-4562-964f-5f3539b63961', u'segmentation_id': 301, u'device_owner': u'network:router_gateway', u'physical_network': None, u'mac_address': u'fa:16:3e:f7:a0:9f', u'device': u'f572d4d3-e16c-4ca0-97c6-6c3b2c94e5c3', u'port_security_enabled': False, u'port_id': u'f572d4d3-e16c-4ca0-97c6-6c3b2c94e5c3', u'fixed_ips': [{u'subnet_id': u'786c63a0-18fe-4753-aab0-995df0d24a06', u'ip_address': u'172.16.69.201'}], u'network_type': u'gre', u'security_groups': []}
2016-11-03 15:53:16.807 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Skipping ARP spoofing rules for port 'qg-f572d4d3-e1' because it has port security disabled
2016-11-03 15:53:18.697 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Configuration for devices up [u'f572d4d3-e16c-4ca0-97c6-6c3b2c94e5c3'] and devices down [] completed.
```

	Log tại **/var/log/openvswitch/ovs-vswitchd.log**  trên Controller node:
```sh
2016-11-03T08:53:13.193Z|00200|bridge|INFO|bridge br-int: added interface qg-f572d4d3-e1 on port 5
2016-11-03T08:53:13.861Z|00201|netdev_linux|WARN|qg-f572d4d3-e1: removing policing failed: No such device
2016-11-03T08:53:13.934Z|00202|netdev_linux|INFO|ioctl(SIOCGIFHWADDR) on qg-f572d4d3-e1 device failed: No such device
2016-11-03T08:53:14.008Z|00203|netdev_linux|WARN|ioctl(SIOCGIFINDEX) on qg-f572d4d3-e1 device failed: No such device
2016-11-03T08:53:16.203Z|00204|netdev_linux|WARN|qg-f572d4d3-e1: removing policing failed: No such device
2016-11-03T08:53:16.977Z|00205|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-03T08:53:17.501Z|00206|ofp_util|INFO|normalization changed ofp_match, details:
2016-11-03T08:53:17.501Z|00207|ofp_util|INFO| pre: in_port=5,nw_proto=58,tp_src=136
2016-11-03T08:53:17.501Z|00208|ofp_util|INFO|post: in_port=5
2016-11-03T08:53:17.502Z|00209|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-03T08:53:17.783Z|00210|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-03T08:53:17.958Z|00211|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-03T08:53:18.124Z|00212|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
```

	- click modify *Radmin* vừa tạo:
```sh
2016-11-03 15:56:10.971 28496 INFO neutron.wsgi [req-a0b29cf6-614d-4704-8752-a671d4479189 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:56:10] "GET /v2.0/routers/119cbe06-6852-443f-8118-7f35e19c3abe.json HTTP/1.1" 200 726 0.199272
2016-11-03 15:56:12.100 28496 INFO neutron.wsgi [req-7e534024-dc51-4232-b4fc-c02555b6101e fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:56:12] "GET /v2.0/networks/f3dfe7a0-bff6-4562-964f-5f3539b63961.json HTTP/1.1" 200 869 1.120912
2016-11-03 15:56:12.383 28497 INFO neutron.wsgi [req-50d92e7d-96b3-4fd7-b5f3-8655f599e3e1 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:56:12] "GET /v2.0/ports.json?device_id=119cbe06-6852-443f-8118-7f35e19c3abe HTTP/1.1" 200 1036 0.160162
2016-11-03 15:56:12.456 28496 INFO neutron.wsgi [req-4a67172c-1cf0-46fd-a740-1b04f18071f5 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:56:12] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.012663
```


	- click add interface
```sh
2016-11-03 15:58:39.973 28497 INFO neutron.wsgi [req-32503a24-d6b4-43db-9921-e9447095202f fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:58:39] "GET /v2.0/routers/119cbe06-6852-443f-8118-7f35e19c3abe.json HTTP/1.1" 200 726 0.118414
2016-11-03 15:58:40.135 28496 INFO neutron.wsgi [req-06cd4501-6143-44e4-93d0-10bad8ab9b25 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:58:40] "GET /v2.0/networks.json?shared=False&tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 856 0.149603
2016-11-03 15:58:40.313 28497 INFO neutron.wsgi [req-d0a44f3e-0d10-44ea-b546-3b42b2387b77 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:58:40] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.152807
2016-11-03 15:58:41.228 28496 INFO neutron.wsgi [req-edeb656b-c277-4be1-a530-996afa310c79 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:58:41] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 872 0.901925
2016-11-03 15:58:41.543 28497 INFO neutron.wsgi [req-7d7c1b14-c72a-4474-8527-b7f0dbe6766d fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:58:41] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.294679
2016-11-03 15:58:41.656 28496 INFO neutron.wsgi [req-f21ca297-bfbe-4d32-aac5-122df3835d89 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:58:41] "GET /v2.0/ports.json?device_id=119cbe06-6852-443f-8118-7f35e19c3abe HTTP/1.1" 200 1036 0.107704
```

	- click submit:
```sh
2016-11-03 15:59:27.775 28497 INFO neutron.wsgi [req-c9bb673a-59e8-4c06-b735-f089be3f8b2c fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:59:27] "GET /v2.0/routers/119cbe06-6852-443f-8118-7f35e19c3abe.json HTTP/1.1" 200 726 0.141670
2016-11-03 15:59:28.398 28496 INFO neutron.wsgi [req-1ad025d7-20d0-4745-b890-a60941b85007 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:59:28] "GET /v2.0/networks.json?shared=False&tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 856 0.612990
2016-11-03 15:59:28.546 28497 INFO neutron.wsgi [req-1415a9c6-d2b6-4759-8c0f-2c5ed93990f2 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:59:28] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.143985
2016-11-03 15:59:28.716 28496 INFO neutron.wsgi [req-67e97675-573d-40f8-9d2f-87a048f7a655 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:59:28] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 872 0.146583
2016-11-03 15:59:28.914 28497 INFO neutron.wsgi [req-069acc1a-901c-48b8-81bf-68a96d3fbf77 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:59:28] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.185561
2016-11-03 15:59:29.016 28497 INFO neutron.wsgi [req-101996ca-048d-41c9-a731-b11cc62b351e fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:59:29] "GET /v2.0/ports.json?device_id=119cbe06-6852-443f-8118-7f35e19c3abe HTTP/1.1" 200 1036 0.090198
2016-11-03 15:59:31.056 28496 INFO neutron.wsgi [req-cca8064f-1226-4244-b1fe-15c3f01e36ea fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:59:31] "PUT /v2.0/routers/119cbe06-6852-443f-8118-7f35e19c3abe/add_router_interface.json HTTP/1.1" 200 523 1.997551
2016-11-03 15:59:31.385 28496 INFO neutron.wsgi [req-62a9e49f-affa-4d13-bd2e-91f15ac17323 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:59:31] "GET /v2.0/ports/6e2d27eb-673c-4be6-8923-1bcc47dc0a36.json HTTP/1.1" 200 1013 0.322698
2016-11-03 15:59:31.935 28496 INFO neutron.wsgi [req-3ae3bba7-ede5-41c3-97ff-680ee61de839 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:59:31] "GET /v2.0/routers/119cbe06-6852-443f-8118-7f35e19c3abe.json HTTP/1.1" 200 726 0.424205
2016-11-03 15:59:32.463 28497 INFO neutron.wsgi [req-cd716a7e-f871-486d-8529-f28ab57c544c fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:59:32] "GET /v2.0/networks/f3dfe7a0-bff6-4562-964f-5f3539b63961.json HTTP/1.1" 200 869 0.523092
2016-11-03 15:59:32.911 28496 INFO neutron.wsgi [req-3cba5f23-2a97-4ec8-8834-abf2fbc8ec18 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:59:32] "GET /v2.0/ports.json?device_id=119cbe06-6852-443f-8118-7f35e19c3abe HTTP/1.1" 200 1828 0.459767
2016-11-03 15:59:32.947 28497 INFO neutron.wsgi [req-447b34d0-5513-4935-92fd-f7534997db07 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 15:59:32] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.028877
```

	Ngoài ra xuất hiện thêm log ở các file **/var/log/neutron/dhcp-agent.log** trên controller
```sh
2016-11-03 15:59:31.067 24274 INFO neutron.agent.dhcp.agent [req-cca8064f-1226-4244-b1fe-15c3f01e36ea fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] Trigger reload_allocations for port admin_state_up=True, allowed_address_pairs=[], binding:host_id=, binding:profile=, binding:vif_details=, binding:vif_type=unbound, binding:vnic_type=normal, created_at=2016-11-03T08:59:29, description=, device_id=119cbe06-6852-443f-8118-7f35e19c3abe, device_owner=network:router_interface, dns_name=None, extra_dhcp_opts=[], fixed_ips=[{u'subnet_id': u'acb835d4-cb3d-44a9-a554-50f1b9f08556', u'ip_address': u'192.168.10.1'}], id=6e2d27eb-673c-4be6-8923-1bcc47dc0a36, mac_address=fa:16:3e:fc:46:f6, name=, network_id=6c3491fb-8901-4608-9e17-f70986595e23, port_security_enabled=False, security_groups=[], status=DOWN, tenant_id=3262c9a75a494be288b4d77608334e0d, updated_at=2016-11-03T08:59:29
```

	Log tại file **/var/log/neutron/l3-agent.log**
```sh
2016-11-03 15:59:36.679 25054 INFO neutron.agent.linux.interface [-] Device qg-f572d4d3-e1 already exists
```

	Log tại file **/var/log/neutron/openvswitch-agent.log** trên Controller node:
```sh
2016-11-03 15:59:35.365 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Port 6e2d27eb-673c-4be6-8923-1bcc47dc0a36 updated. Details: {u'profile': {}, u'network_qos_policy_id': None, u'qos_policy_id': None, u'allowed_address_pairs': [], u'admin_state_up': True, u'network_id': u'6c3491fb-8901-4608-9e17-f70986595e23', u'segmentation_id': 385, u'device_owner': u'network:router_interface', u'physical_network': None, u'mac_address': u'fa:16:3e:fc:46:f6', u'device': u'6e2d27eb-673c-4be6-8923-1bcc47dc0a36', u'port_security_enabled': False, u'port_id': u'6e2d27eb-673c-4be6-8923-1bcc47dc0a36', u'fixed_ips': [{u'subnet_id': u'acb835d4-cb3d-44a9-a554-50f1b9f08556', u'ip_address': u'192.168.10.1'}], u'network_type': u'gre', u'security_groups': []}
2016-11-03 15:59:36.054 27907 INFO neutron.agent.securitygroups_rpc [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Refresh firewall rules
2016-11-03 15:59:36.672 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Skipping ARP spoofing rules for port 'qr-6e2d27eb-67' because it has port security disabled
2016-11-03 15:59:38.522 27907 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-9830cd37-247b-494e-a384-b499a2f8b329 - - - - -] Configuration for devices up [u'6e2d27eb-673c-4be6-8923-1bcc47dc0a36'] and devices down [] completed.
```
	
	Log tại **/var/log/openvswitch/ovs-vswitchd.log**  trên Controller node:
```sh
2016-11-03T08:59:34.212Z|00213|bridge|INFO|bridge br-int: added interface qr-6e2d27eb-67 on port 6
2016-11-03T08:59:34.911Z|00214|netdev_linux|INFO|ioctl(SIOCGIFHWADDR) on qr-6e2d27eb-67 device failed: No such device
2016-11-03T08:59:34.928Z|00215|netdev_linux|WARN|ioctl(SIOCGIFINDEX) on qr-6e2d27eb-67 device failed: No such device
2016-11-03T08:59:34.928Z|00216|netdev_linux|WARN|qr-6e2d27eb-67: removing policing failed: No such device
2016-11-03T08:59:36.903Z|00217|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-03T08:59:37.076Z|00218|ofp_util|INFO|normalization changed ofp_match, details:
2016-11-03T08:59:37.076Z|00219|ofp_util|INFO| pre: in_port=6,nw_proto=58,tp_src=136
2016-11-03T08:59:37.076Z|00220|ofp_util|INFO|post: in_port=6
2016-11-03T08:59:37.076Z|00221|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-03T08:59:37.223Z|00222|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-03T08:59:37.380Z|00223|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-03T08:59:37.769Z|00224|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
```

	- click instances trong *Project -> Compute -> Instances*:
```sh
2016-11-03 16:03:38.119 28496 INFO neutron.wsgi [req-f63810fa-5112-4cc7-b29f-cadb9df0f76b fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:03:38] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.020938
```

	- click launch instance:
```sh
2016-11-03 16:04:40.133 28497 INFO neutron.wsgi [req-e479b3c3-e27e-4af3-a2cd-bd4628516f5a fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:04:40] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.060393
2016-11-03 16:04:40.616 28496 INFO neutron.wsgi [req-fe0134b2-54f5-4667-a7e0-77e331c624fa fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:04:40] "GET /v2.0/security-groups.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 2187 0.462366
2016-11-03 16:04:43.286 28497 INFO neutron.wsgi [req-7e4fa05b-a616-4ff8-bff4-abcab98394d0 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:04:43] "GET /v2.0/networks.json?shared=False&tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 856 0.296546
2016-11-03 16:04:43.681 28496 INFO neutron.wsgi [req-b485b816-5003-4230-b72e-77cb12d1c70d fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:04:43] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.341924
2016-11-03 16:04:43.904 28496 INFO neutron.wsgi [req-bdf01a57-020a-4bc0-ad75-82f82ec97837 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:04:43] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 872 0.213269
2016-11-03 16:04:44.090 28497 INFO neutron.wsgi [req-4cb5ff5a-1323-4482-9577-ffe7f36446ee fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:04:44] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.178276
2016-11-03 16:04:44.701 28496 INFO neutron.wsgi [req-1458ad89-f2a9-4e24-95c5-59ba6d1fd8ba fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:04:44] "GET /v2.0/ports.json?network_id=6c3491fb-8901-4608-9e17-f70986595e23 HTTP/1.1" 200 1944 0.199948
2016-11-03 16:04:44.884 28496 INFO neutron.wsgi [req-861d490d-fc7f-49c9-aefe-936e7b06b512 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:04:44] "GET /v2.0/ports.json?network_id=f3dfe7a0-bff6-4562-964f-5f3539b63961 HTTP/1.1" 200 1911 0.178149
```


	- complete launch instance:
```sh
2016-11-03 16:06:34.580 28496 INFO neutron.wsgi [req-f9df3d66-2362-4506-85db-20f97246de95 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:06:34] "GET /v2.0/security-groups.json?fields=id&id=35a92ab4-ed7c-4917-82f6-67a4b70db22e HTTP/1.1" 200 282 0.325645
2016-11-03 16:06:34.728 28496 INFO neutron.wsgi [req-b6bdbc4f-4025-4a5f-b524-3da5d54b4c4b fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:06:34] "GET /v2.0/security-groups/35a92ab4-ed7c-4917-82f6-67a4b70db22e.json HTTP/1.1" 200 2184 0.093739
2016-11-03 16:06:35.581 28496 INFO neutron.wsgi [req-6bbd7339-5e37-464e-b5d5-0009e9af8249 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:06:35] "GET /v2.0/networks.json?id=6c3491fb-8901-4608-9e17-f70986595e23 HTTP/1.1" 200 856 0.846542
2016-11-03 16:06:35.709 28496 INFO neutron.wsgi [req-5093cf43-458a-4073-a905-2336247d3188 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:06:35] "GET /v2.0/quotas/3262c9a75a494be288b4d77608334e0d.json HTTP/1.1" 200 385 0.126454
2016-11-03 16:06:36.059 28496 INFO neutron.wsgi [req-1217a540-6df9-43c9-8444-3dff13ca1168 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:06:36] "GET /v2.0/ports.json?fields=id&tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 369 0.345995
2016-11-03 16:06:47.809 28496 INFO neutron.wsgi [req-3d01bd75-a85f-410a-a61b-56b796e502e4 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:06:47] "GET /v2.0/ports.json?device_id=8028f0be-4bf7-42c5-9857-ee35250beb15 HTTP/1.1" 200 226 0.198748
2016-11-03 16:06:54.262 28496 INFO neutron.wsgi [req-4e69eabf-70b6-4b93-ba8b-04b76dca4c94 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:06:54] "GET /v2.0/ports.json?device_id=8028f0be-4bf7-42c5-9857-ee35250beb15 HTTP/1.1" 200 226 0.111703
2016-11-03 16:06:54.588 28497 INFO neutron.wsgi [req-536498af-7382-4f02-b9e6-dca401a143af fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:06:54] "GET /v2.0/ports.json?device_id=8028f0be-4bf7-42c5-9857-ee35250beb15 HTTP/1.1" 200 226 0.183312
2016-11-03 16:06:54.647 28496 INFO neutron.wsgi [req-63f617bd-50ef-4fef-b2f9-165267f14a15 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:06:54] "GET /v2.0/floatingips.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 232 0.052334
2016-11-03 16:06:54.756 28497 INFO neutron.wsgi [req-eefc078c-e0c2-4dcb-8222-27bc14bf379d fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:06:54] "GET /v2.0/ports.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 2818 0.094760
2016-11-03 16:06:54.932 28496 INFO neutron.wsgi [req-b2520df2-e252-4603-815d-ae2e906f8c73 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:06:54] "GET /v2.0/networks.json HTTP/1.1" 200 1501 0.155138
2016-11-03 16:06:55.119 28497 INFO neutron.wsgi [req-a6e4c3d6-0be3-464c-b52b-428ab796e289 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:06:55] "GET /v2.0/subnets.json HTTP/1.1" 200 1351 0.169608
2016-11-03 16:06:56.146 28496 INFO neutron.wsgi [req-0a81881b-90fc-4890-b79f-67f3c9c90568 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:06:56] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.010091
2016-11-03 16:07:07.562 28497 INFO neutron.wsgi [req-03afffce-3414-4ecc-a627-25b0d4a60f20 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.81 - - [03/Nov/2016 16:07:07] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.026379
2016-11-03 16:07:10.154 28497 INFO neutron.wsgi [req-10ff2776-e1b3-48e2-b4a4-b1c5f62b5069 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.81 - - [03/Nov/2016 16:07:10] "GET /v2.0/networks.json?id=6c3491fb-8901-4608-9e17-f70986595e23 HTTP/1.1" 200 856 0.440730
2016-11-03 16:07:13.342 28496 INFO neutron.wsgi [req-3acf45fb-d4fc-40d2-a038-ec36b6858662 fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.80 - - [03/Nov/2016 16:07:13] "GET /v2.0/ports.json?device_id=8028f0be-4bf7-42c5-9857-ee35250beb15 HTTP/1.1" 200 226 0.062090
2016-11-03 16:07:15.854 28497 INFO neutron.wsgi [req-d8057958-a80a-4517-aa8f-1f7bd9c9a00e fc0becc74871437581d1b3bb74cc6404 3262c9a75a494be288b4d77608334e0d - - -] 10.10.10.81 - - [03/Nov/2016 16:07:15] "GET /v2.0/security-groups.json?tenant_id=3262c9a75a494be288b4d77608334e0d HTTP/1.1" 200 2187 0.336095
```

Tạo instance với project tenant-network được tạo phía trên
----
- Click Intances trong *Project -> Compute -> Instances*
```sh
2016-11-04 09:20:54.459 17166 INFO neutron.wsgi [req-32e49175-452b-4dd9-8592-1d94398d23ce 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:20:54] "GET /v2.0/ports.json?device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 1959 0.129457
2016-11-04 09:20:54.660 17166 INFO neutron.wsgi [req-2fa3d835-2482-419a-a911-672113a4b6ee 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:20:54] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.187180
2016-11-04 09:20:54.821 17167 INFO neutron.wsgi [req-a9fbb512-604b-4fbd-a907-6bc714b0a339 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:20:54] "GET /v2.0/ports.json?device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 1959 0.117908
2016-11-04 09:20:54.884 17166 INFO neutron.wsgi [req-65daddd4-4540-4260-bd76-939372f7f6ab 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:20:54] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=015c3cb1-6b2c-49dd-98fc-813487ae1aa0&port_id=ce521cc5-e5ad-4d76-846a-a41eb3076301 HTTP/1.1" 200 644 0.057902
2016-11-04 09:20:55.004 17167 INFO neutron.wsgi [req-8f6b4cf3-e3e2-4a0f-85ba-0ea63d7f279e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:20:55] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 6265 0.111392
2016-11-04 09:20:55.246 17166 INFO neutron.wsgi [req-193e41cb-e71f-4953-97c6-b9370087fa35 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:20:55] "GET /v2.0/networks.json?id=304a7243-e2e8-49e4-8b01-bff41b80aecf&id=d3107881-083f-4bf7-a8b5-d8f0b3a46388 HTTP/1.1" 200 1509 0.207799
2016-11-04 09:20:55.454 17167 INFO neutron.wsgi [req-329d9fa3-3d3a-40b4-8c1a-145a2cca9d3b 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:20:55] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.195688
2016-11-04 09:20:55.955 17167 INFO neutron.wsgi [req-16d07f49-a8f8-44b8-b905-6e25cfce6143 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:20:55] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.009069
```
Trong log trên là thời điểm đã có 02 instance được khởi tạo trước đó với tenant network khác.

- Click Launch Instance:
```sh
2016-11-04 09:23:05.962 17166 INFO neutron.wsgi [req-4fd113f9-8f74-4386-8a2a-bb3f9fa80187 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:23:05] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.021390
2016-11-04 09:23:06.623 17166 INFO neutron.wsgi [req-8f261dcd-abdf-4051-bce5-c1ac7b66922c 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:23:06] "GET /v2.0/security-groups.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 2187 0.610059
2016-11-04 09:23:07.522 17167 INFO neutron.wsgi [req-7c84aaa8-dc39-4b55-a534-cb04824ceec3 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:23:07] "GET /v2.0/networks.json?shared=False&tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 1489 0.456359
2016-11-04 09:23:08.092 17166 INFO neutron.wsgi [req-4737e34e-4feb-47d2-98d5-dfccc7a132fd 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:23:08] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.418387
2016-11-04 09:23:08.350 17166 INFO neutron.wsgi [req-932f7a37-c58b-4c98-a60c-dbb39fb8ee1a 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:23:08] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 880 0.254049
2016-11-04 09:23:08.723 17166 INFO neutron.wsgi [req-29d3e2c7-eb50-4273-ae0a-a5a90e0c5e7b 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:23:08] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.369841
2016-11-04 09:23:09.699 17167 INFO neutron.wsgi [req-c5993b36-bb10-4697-a175-35502ff2f9a5 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:23:09] "GET /v2.0/ports.json?network_id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 1942 0.168593
2016-11-04 09:23:09.822 17166 INFO neutron.wsgi [req-ca630f8c-cf24-4b5a-bc28-c9b8634564ea 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:23:09] "GET /v2.0/ports.json?network_id=304a7243-e2e8-49e4-8b01-bff41b80aecf HTTP/1.1" 200 2808 0.226705
2016-11-04 09:23:09.842 17166 INFO neutron.wsgi [req-5e9dea2a-c7d3-453c-941f-74fc9f01fda7 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:23:09] "GET /v2.0/ports.json?network_id=d3107881-083f-4bf7-a8b5-d8f0b3a46388 HTTP/1.1" 200 4342 0.250004
```

- Sau khi điền các thông số và launch instance:
```sh
2016-11-04 09:24:13.950 17167 INFO neutron.wsgi [req-e17f7809-bbce-4dde-a4fa-ce305ef464c6 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:13] "GET /v2.0/security-groups.json?fields=id&id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 282 0.080845
2016-11-04 09:24:14.072 17167 INFO neutron.wsgi [req-87dd2399-4ff0-40d9-b0c8-abfb50f37862 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:14] "GET /v2.0/security-groups/4c770efd-2459-48c9-aaf9-b429d375a996.json HTTP/1.1" 200 2184 0.113650
2016-11-04 09:24:14.223 17167 INFO neutron.wsgi [req-59fba6a1-78db-4a6f-abd4-6545e68267a4 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:14] "GET /v2.0/networks.json?id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 860 0.138859
2016-11-04 09:24:14.301 17167 INFO neutron.wsgi [req-55f5db76-60c4-4d5e-b1e4-5376d2e4c76c 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:14] "GET /v2.0/quotas/bd586f74227a48fab42852c8923c4d83.json HTTP/1.1" 200 385 0.072102
2016-11-04 09:24:14.407 17167 INFO neutron.wsgi [req-b1551796-40d2-4c05-8cdc-0325dd503bba 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:14] "GET /v2.0/ports.json?fields=id&tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 561 0.094828
2016-11-04 09:24:15.738 17166 INFO neutron.wsgi [req-4fb2f348-bbd2-4aae-93e2-89277229bffc 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:15] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 226 0.102277
2016-11-04 09:24:16.193 17167 INFO neutron.wsgi [req-fb2c05d2-b09a-48d2-91b6-3fad637eb752 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:16] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 1959 0.053738
2016-11-04 09:24:16.276 17167 INFO neutron.wsgi [req-fb6bd930-f71c-4c79-9dc8-50909a6bb446 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:16] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.079463
2016-11-04 09:24:16.419 17167 INFO neutron.wsgi [req-97d0979c-6c08-4764-8ba1-6b9cfc7c14ed 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:16] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 1959 0.117587
2016-11-04 09:24:16.495 17166 INFO neutron.wsgi [req-cc5e09ee-b31d-4fe3-b77d-043040f9d1ba 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:16] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=015c3cb1-6b2c-49dd-98fc-813487ae1aa0&port_id=ce521cc5-e5ad-4d76-846a-a41eb3076301 HTTP/1.1" 200 644 0.071932
2016-11-04 09:24:16.637 17167 INFO neutron.wsgi [req-77343326-326b-4331-b82a-c43dd990667d 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:16] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 6265 0.135146
2016-11-04 09:24:16.883 17166 INFO neutron.wsgi [req-2b2dbd69-eddb-47ea-a234-d4dc1909a9b2 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:16] "GET /v2.0/networks.json?id=304a7243-e2e8-49e4-8b01-bff41b80aecf&id=d3107881-083f-4bf7-a8b5-d8f0b3a46388 HTTP/1.1" 200 1509 0.232600
2016-11-04 09:24:17.214 17167 INFO neutron.wsgi [req-cfd4b326-ffdc-4339-9600-19a5fa76c277 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:17] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.326587
2016-11-04 09:24:18.016 17166 INFO neutron.wsgi [req-4007f86a-69b0-4ad9-9c3d-c810c29c04ad 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:18] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.041048
2016-11-04 09:24:18.233 17167 INFO neutron.wsgi [req-c8a95a66-2446-49c1-a4bf-d030012b12f0 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.81 - - [04/Nov/2016 09:24:18] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.047464
2016-11-04 09:24:18.570 17167 INFO neutron.wsgi [req-1c83c4ce-cb1a-44c2-b50f-e09329789867 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.81 - - [04/Nov/2016 09:24:18] "GET /v2.0/networks.json?id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 860 0.330563
2016-11-04 09:24:18.890 17167 INFO neutron.wsgi [req-211aea33-747b-4e50-aeb5-dff7d5e54c4c 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.81 - - [04/Nov/2016 09:24:18] "GET /v2.0/security-groups.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 2187 0.251096
2016-11-04 09:24:20.606 17167 INFO neutron.wsgi [req-c1c838ec-f436-4794-a590-858377aeed4e 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:24:20] "POST /v2.0/ports.json HTTP/1.1" 201 1091 1.681490
2016-11-04 09:24:20.790 17167 INFO neutron.wsgi [req-449b55f6-0822-4c78-afa0-8eea21bbab48 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:24:20] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83&device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1089 0.146054
2016-11-04 09:24:20.811 17166 INFO neutron.wsgi [req-07519385-c8da-46aa-98c5-9787f64bc08a 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:20] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1089 0.137366
2016-11-04 09:24:20.891 17167 INFO neutron.wsgi [req-25d58b74-c58c-49cc-80e3-299bd73aef14 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:24:20] "GET /v2.0/floatingips.json?fixed_ip_address=192.168.2.51&port_id=77a12669-d998-4196-b487-655249e9e546 HTTP/1.1" 200 232 0.068998
2016-11-04 09:24:21.162 17166 INFO neutron.wsgi [req-fee38eda-f244-430a-8176-3970c3bee6f5 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:21] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.335092
2016-11-04 09:24:21.483 17167 INFO neutron.wsgi [req-1d50fdde-1ddb-4d81-8171-8d5660a25437 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.81 - - [04/Nov/2016 09:24:21] "GET /v2.0/subnets.json?id=b0569f79-0201-47bd-910f-238a4fd130ce HTTP/1.1" 200 783 0.579604
2016-11-04 09:24:21.757 17166 INFO neutron.wsgi [req-5a8fd0b3-a475-484d-a813-46dfc327f083 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:21] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1089 0.391434
2016-11-04 09:24:22.028 17166 INFO neutron.wsgi [req-36170a72-f5b9-4435-ae50-23d5ad69b08b 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:22] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=77a12669-d998-4196-b487-655249e9e546 HTTP/1.1" 200 232 0.240566
2016-11-04 09:24:22.103 17167 INFO neutron.wsgi [req-efbd213b-fa64-46f4-8594-d973e3221c85 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.81 - - [04/Nov/2016 09:24:22] "GET /v2.0/ports.json?network_id=b3a81f00-7b15-48da-b071-2589f840deff&device_owner=network%3Adhcp HTTP/1.1" 200 1098 0.602950
2016-11-04 09:24:22.464 17166 INFO neutron.wsgi [req-994ab6b3-d3ae-4c61-a67e-ea5b107d87a2 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:22] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7129 0.385518
2016-11-04 09:24:22.741 17167 INFO neutron.wsgi [req-1b4979fb-33d8-4755-a6c2-21e522e25373 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:22] "GET /v2.0/networks.json?id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 860 0.275058
2016-11-04 09:24:23.423 17167 INFO neutron.wsgi [req-52775ad2-aba7-474f-ba9f-09d4731240f3 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:23] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.623359
2016-11-04 09:24:23.582 17167 INFO neutron.wsgi [req-e916a367-5a1e-4f66-b01a-2a51ed5fc793 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:23] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.023882
2016-11-04 09:24:26.923 17167 INFO neutron.wsgi [req-903e0283-66bc-42e9-8083-c1568e7cd2a1 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:26] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1089 0.070116
2016-11-04 09:24:27.030 17167 INFO neutron.wsgi [req-54704523-0c06-4eed-8b4a-0af488b0a3c8 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:27] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.103026
2016-11-04 09:24:27.148 17166 INFO neutron.wsgi [req-c3c25f84-c9af-4f24-bcca-c894c6d3706d 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:27] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1089 0.058228
2016-11-04 09:24:27.188 17167 INFO neutron.wsgi [req-787f0fbb-57d5-4687-82f2-e278c4731e00 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:27] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=77a12669-d998-4196-b487-655249e9e546 HTTP/1.1" 200 232 0.032047
2016-11-04 09:24:27.258 17166 INFO neutron.wsgi [req-464b0189-38d9-48cb-9db4-099a91bc62d1 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:27] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7129 0.061868
2016-11-04 09:24:27.393 17166 INFO neutron.wsgi [req-10d95f2e-4b1a-420a-b137-f014f65290ef 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:27] "GET /v2.0/networks.json?id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 860 0.128692
2016-11-04 09:24:27.577 17167 INFO neutron.wsgi [req-308f45b2-5234-4aa3-bb1f-d3535ac86ae8 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:27] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.181717
2016-11-04 09:24:27.792 17166 INFO neutron.wsgi [req-bd940b4f-44b8-4db1-886c-2488591eba0e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:27] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.021740
2016-11-04 09:24:33.730 17167 INFO neutron.wsgi [req-4b08eb43-4a2c-4c11-a847-439b117b6a2d 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:33] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1090 0.103633
2016-11-04 09:24:33.950 17167 INFO neutron.wsgi [req-d49080d6-6e35-4f4a-a3cf-5a79917947cc 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:33] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.215596
2016-11-04 09:24:34.311 17166 INFO neutron.wsgi [req-fd7c0ed2-f29e-4097-8384-f96e7f80aea8 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:34] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1090 0.165016
2016-11-04 09:24:34.370 17167 INFO neutron.wsgi [req-6a240561-aa86-46d5-89a5-8f137bdd62bf 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:34] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=77a12669-d998-4196-b487-655249e9e546 HTTP/1.1" 200 232 0.050197
2016-11-04 09:24:34.472 17167 INFO neutron.wsgi [req-73941f17-c2b5-4463-abfd-ebb228377f3a 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:34] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.095439
2016-11-04 09:24:34.606 17166 INFO neutron.wsgi [req-cf6ac2ff-899c-4d0c-9a16-4033628312a9 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:34] "GET /v2.0/networks.json?id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 860 0.132023
2016-11-04 09:24:34.861 17167 INFO neutron.wsgi [req-97cec3f4-5438-4a30-ab68-e84233065b8f 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:34] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.253784
2016-11-04 09:24:34.950 17166 INFO neutron.wsgi [req-75e45122-7cae-433c-a7e3-3fa3aa45bd92 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:34] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.015050
2016-11-04 09:24:38.218 17168 INFO neutron.notifiers.nova [-] Nova event response: {u'status': u'completed', u'tag': u'77a12669-d998-4196-b487-655249e9e546', u'name': u'network-vif-plugged', u'server_uuid': u'c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2', u'code': 200}
2016-11-04 09:24:43.199 17166 INFO neutron.wsgi [req-f337d8c7-6dee-4201-a476-757bb707a9fd 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:43] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.069900
2016-11-04 09:24:43.288 17166 INFO neutron.wsgi [req-3708e114-cd1a-45ef-a699-13bc70fa206c 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:43] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.081632
2016-11-04 09:24:43.405 17167 INFO neutron.wsgi [req-34bcb194-7332-4a66-ab41-b589325b0e4b 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:43] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.056784
2016-11-04 09:24:43.442 17166 INFO neutron.wsgi [req-9f8056b8-49d9-4d8e-a496-e8856253724e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:43] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=77a12669-d998-4196-b487-655249e9e546 HTTP/1.1" 200 232 0.034877
2016-11-04 09:24:43.509 17167 INFO neutron.wsgi [req-98b12d28-d829-4bb8-aca7-e81c5f1f4fef 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:43] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.060281
2016-11-04 09:24:43.722 17166 INFO neutron.wsgi [req-a99d97c9-2077-464d-8f33-7e584124f58f 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:43] "GET /v2.0/networks.json?id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 860 0.203553
2016-11-04 09:24:43.846 17166 INFO neutron.wsgi [req-308237fc-c4dc-41f4-a470-ae75e590591e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:43] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.118731
2016-11-04 09:24:44.073 17167 INFO neutron.wsgi [req-a3ae317f-65df-44da-8125-93b7711d5ed0 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:44] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.005054
2016-11-04 09:24:50.339 17166 INFO neutron.wsgi [req-a4fa3f2e-26f1-494b-b0f1-441955c00bce 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:50] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 1.535841
2016-11-04 09:24:50.423 17166 INFO neutron.wsgi [req-1a36b61c-d2d4-4988-b8dc-ded4b17f3fea 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:50] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.077727
2016-11-04 09:24:52.372 17167 INFO neutron.wsgi [req-1b6a7ba2-52bc-4cfe-bb2a-93ab49375460 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:52] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.941788
2016-11-04 09:24:52.470 17167 INFO neutron.wsgi [req-6f1abe2e-ae7c-42ed-b61d-e1eda783bf3e 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.80 - - [04/Nov/2016 09:24:52] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.085134
```

Các log ở trên là trong file neutron-server tại Controller node, sau khi create xong instance ta thấy xuất hiện thêm log ở một số file sau:
	- /var/log/neutron/dhcp-agent.log trên controller node
	```sh
	2016-11-04 09:24:20.588 2218 INFO neutron.agent.dhcp.agent [req-c1c838ec-f436-4794-a590-858377aeed4e 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] Trigger reload_allocations for port admin_state_up=True, allowed_address_pairs=[], binding:host_id=compute1, binding:profile=, binding:vif_details=ovs_hybrid_plug=True, port_filter=True, binding:vif_type=ovs, binding:vnic_type=normal, created_at=2016-11-04T02:24:19, description=, device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2, device_owner=compute:nova, dns_name=None, extra_dhcp_opts=[], fixed_ips=[{u'subnet_id': u'b0569f79-0201-47bd-910f-238a4fd130ce', u'ip_address': u'192.168.2.51'}], id=77a12669-d998-4196-b487-655249e9e546, mac_address=fa:16:3e:73:a2:e8, name=, network_id=b3a81f00-7b15-48da-b071-2589f840deff, port_security_enabled=True, security_groups=[u'4c770efd-2459-48c9-aaf9-b429d375a996'], status=DOWN, tenant_id=bd586f74227a48fab42852c8923c4d83, updated_at=2016-11-04T02:24:20
	```
	- /var/log/neutron/neutron-metadata-agent.log trên Controller node:
	```sh
	2016-11-04 09:24:50.582 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:50] "GET /2009-04-04/meta-data/instance-id HTTP/1.1" 200 127 3.045639
	2016-11-04 09:24:52.516 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:52] "GET /2009-04-04/meta-data/public-keys HTTP/1.1" 404 176 1.889635
	2016-11-04 09:24:52.648 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:52] "GET /2009-04-04/meta-data/instance-id HTTP/1.1" 200 127 0.085370
	2016-11-04 09:24:52.677 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:52] "GET /2009-04-04/meta-data/ami-launch-index HTTP/1.1" 200 117 0.006897
	2016-11-04 09:24:52.711 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:52] "GET /2009-04-04/meta-data/instance-type HTTP/1.1" 200 123 0.009987
	2016-11-04 09:24:52.743 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:52] "GET /2009-04-04/meta-data/local-ipv4 HTTP/1.1" 200 129 0.009448
	2016-11-04 09:24:52.777 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:52] "GET /2009-04-04/meta-data/public-ipv4 HTTP/1.1" 200 116 0.008264
	2016-11-04 09:24:52.809 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:52] "GET /2009-04-04/meta-data/hostname HTTP/1.1" 200 138 0.007013
	2016-11-04 09:24:52.843 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:52] "GET /2009-04-04/meta-data/local-hostname HTTP/1.1" 200 138 0.010594
	2016-11-04 09:24:52.881 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:52] "GET /2009-04-04/user-data HTTP/1.1" 404 176 0.010995
	2016-11-04 09:24:52.930 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:52] "GET /2009-04-04/meta-data/block-device-mapping HTTP/1.1" 200 124 0.007718
	2016-11-04 09:24:52.967 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:52] "GET /2009-04-04/meta-data/block-device-mapping/ami HTTP/1.1" 200 119 0.010003
	2016-11-04 09:24:53.002 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:53] "GET /2009-04-04/meta-data/block-device-mapping/root HTTP/1.1" 200 124 0.009185
	2016-11-04 09:24:53.124 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:53] "GET /2009-04-04/meta-data/public-hostname HTTP/1.1" 200 138 0.095101
	2016-11-04 09:24:53.161 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:24:53] "GET /2009-04-04/meta-data/placement/availability-zone HTTP/1.1" 200 120 0.009116
	```
	- /var/log/neutron/openvswitch-agent.log trên controller node
	```sh
	2016-11-04 09:11:50.675 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Port 8aece323-4060-479d-9a3e-458d68e13be1 updated. Details: {u'profile': {}, u'network_qos_policy_id': None, u'qos_policy_id': None, u'allowed_address_pairs': [], u'admin_state_up': True, u'network_id': u'b3a81f00-7b15-48da-b071-2589f840deff', u'segmentation_id': 396, u'device_owner': u'network:router_interface', u'physical_network': None, u'mac_address': u'fa:16:3e:5f:f2:b7', u'device': u'8aece323-4060-479d-9a3e-458d68e13be1', u'port_security_enabled': False, u'port_id': u'8aece323-4060-479d-9a3e-458d68e13be1', u'fixed_ips': [{u'subnet_id': u'b0569f79-0201-47bd-910f-238a4fd130ce', u'ip_address': u'192.168.2.1'}], u'network_type': u'gre', u'security_groups': []}
	2016-11-04 09:11:51.440 2214 INFO neutron.agent.securitygroups_rpc [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Refresh firewall rules
	2016-11-04 09:11:52.257 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Skipping ARP spoofing rules for port 'qr-8aece323-40' because it has port security disabled
	2016-11-04 09:11:53.830 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Configuration for devices up [u'8aece323-4060-479d-9a3e-458d68e13be1'] and devices down [] completed.
	2016-11-04 09:24:20.250 2214 INFO neutron.agent.securitygroups_rpc [req-c1c838ec-f436-4794-a590-858377aeed4e 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] Security group member updated [u'4c770efd-2459-48c9-aaf9-b429d375a996']
	```
	- /var/log/openvswitch/ovs-vswitchd.log trên controller node
	```sh
	2016-11-04T02:11:49.244Z|00149|bridge|INFO|bridge br-int: added interface qr-8aece323-40 on port 13
	2016-11-04T02:11:49.942Z|00150|netdev_linux|WARN|qr-8aece323-40: removing policing failed: No such device
	2016-11-04T02:11:49.986Z|00151|netdev_linux|INFO|ioctl(SIOCGIFHWADDR) on qr-8aece323-40 device failed: No such device
	2016-11-04T02:11:50.064Z|00152|netdev_linux|WARN|ioctl(SIOCGIFINDEX) on qr-8aece323-40 device failed: No such device
	2016-11-04T02:11:51.108Z|00153|netdev_linux|WARN|qr-8aece323-40: removing policing failed: No such device
	2016-11-04T02:11:52.453Z|00154|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
	2016-11-04T02:11:52.646Z|00155|ofp_util|INFO|normalization changed ofp_match, details:
	2016-11-04T02:11:52.646Z|00156|ofp_util|INFO| pre: in_port=13,nw_proto=58,tp_src=136
	2016-11-04T02:11:52.646Z|00157|ofp_util|INFO|post: in_port=13
	2016-11-04T02:11:52.647Z|00158|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
	2016-11-04T02:11:52.850Z|00159|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
	2016-11-04T02:11:52.995Z|00160|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
	2016-11-04T02:11:53.311Z|00161|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
	2016-11-04T02:24:34.775Z|00162|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 adds)
	2016-11-04T02:24:34.970Z|00163|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 modifications)
	```
	- /var/log/neutron/openvswitch-agent.log trên Compute node
	```sh 
	2016-11-04 09:24:20.249 1900 INFO neutron.agent.securitygroups_rpc [req-c1c838ec-f436-4794-a590-858377aeed4e 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] Security group member updated [u'4c770efd-2459-48c9-aaf9-b429d375a996']
	2016-11-04 09:24:22.829 1900 INFO neutron.agent.securitygroups_rpc [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Refresh firewall rules
	2016-11-04 09:24:24.082 1900 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Configuration for devices up [] and devices down [] completed.
	2016-11-04 09:24:28.178 1900 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Port 77a12669-d998-4196-b487-655249e9e546 updated. Details: {u'profile': {}, u'network_qos_policy_id': None, u'qos_policy_id': None, u'allowed_address_pairs': [], u'admin_state_up': True, u'network_id': u'b3a81f00-7b15-48da-b071-2589f840deff', u'segmentation_id': 396, u'device_owner': u'compute:nova', u'physical_network': None, u'mac_address': u'fa:16:3e:73:a2:e8', u'device': u'77a12669-d998-4196-b487-655249e9e546', u'port_security_enabled': True, u'port_id': u'77a12669-d998-4196-b487-655249e9e546', u'fixed_ips': [{u'subnet_id': u'b0569f79-0201-47bd-910f-238a4fd130ce', u'ip_address': u'192.168.2.51'}], u'network_type': u'gre', u'security_groups': [u'4c770efd-2459-48c9-aaf9-b429d375a996']}
	2016-11-04 09:24:28.181 1900 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Assigning 3 as local vlan for net-id=b3a81f00-7b15-48da-b071-2589f840deff
	2016-11-04 09:24:29.563 1900 INFO neutron.agent.securitygroups_rpc [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Preparing filters for devices set([u'77a12669-d998-4196-b487-655249e9e546'])
	2016-11-04 09:24:34.854 1900 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Configuration for devices up [u'77a12669-d998-4196-b487-655249e9e546'] and devices down [] completed.
	```
	- /var/log/openvswitch/ovs-vswitchd.log trên Compute node
	```sh
	2016-11-04T02:24:24.899Z|00082|bridge|INFO|bridge br-int: added interface qvo77a12669-d9 on port 5
	2016-11-04T02:24:28.441Z|00083|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 modifications)
	2016-11-04T02:24:28.649Z|00084|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 adds)
	2016-11-04T02:24:31.543Z|00085|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
	2016-11-04T02:24:31.916Z|00086|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
	2016-11-04T02:24:32.431Z|00087|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
	2016-11-04T02:24:32.674Z|00088|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
	2016-11-04T02:24:32.895Z|00089|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
	2016-11-04T02:24:33.139Z|00090|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
	2016-11-04T02:24:33.420Z|00091|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
	2016-11-04T02:24:34.708Z|00092|connmgr|INFO|br-tun<->unix: 2 flow_mods in the last 0 s (2 adds)
	2016-11-04T02:24:34.855Z|00093|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 modifications)
	```
	
- Add thêm IP floating cho instance vừa tạo:
	- Log tại neutron-server khi click associ floating ip
	```sh	
2016-11-04 09:33:17.841 17167 INFO neutron.wsgi [req-33c1279d-4d4c-4da9-af93-f3f9e6065380 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:33:17] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 1.717329
2016-11-04 09:33:17.939 17166 INFO neutron.wsgi [req-ef273127-2c27-4710-bc4e-269515678296 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:33:17] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 644 0.073353
2016-11-04 09:33:18.170 17167 INFO neutron.wsgi [req-4ae0c176-3abe-44b9-9100-b085ced6b252 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:33:18] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.211055
2016-11-04 09:33:18.367 17166 INFO neutron.wsgi [req-8bba87ad-839f-4a3e-ab75-113fec4b4133 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:33:18] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.172060
2016-11-04 09:33:18.900 17167 INFO neutron.wsgi [req-a8130c34-d175-41f9-9894-fcc1f7f3d97f 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:33:18] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 2825 0.166540
2016-11-04 09:33:19.188 17167 INFO neutron.wsgi [req-78990584-c74f-4248-9152-74b1be42b294 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:33:19] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.272321
2016-11-04 09:33:19.414 17166 INFO neutron.wsgi [req-1935c67e-094b-4271-ab66-9eb56e1091bf 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:33:19] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 880 0.182947
2016-11-04 09:33:19.539 17167 INFO neutron.wsgi [req-0d2cbf4d-049b-4ec8-9568-7270181763e0 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:33:19] "GET /v2.0/routers.json HTTP/1.1" 200 1240 0.112422
2016-11-04 09:33:19.728 17166 INFO neutron.wsgi [req-c75fb64b-8181-4cbf-8208-a281a41fe87e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:33:19] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 880 0.172552
2016-11-04 09:33:19.891 17167 INFO neutron.wsgi [req-8d13f480-72d2-4fe4-9b90-839d03c02438 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:33:19] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.160328
	```
	- Click buttom +
	```sh
	2016-11-04 09:34:17.523 17167 INFO neutron.wsgi [req-d129ea9c-4b48-495c-8c49-4dc6a8426511 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:17] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.014647
2016-11-04 09:34:17.703 17166 INFO neutron.wsgi [req-347a8066-25f4-4957-a3e6-40808dc38950 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:17] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 880 0.161437
2016-11-04 09:34:17.933 17167 INFO neutron.wsgi [req-7f1f17bb-4c58-4464-9b64-604a08b11b32 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:17] "GET /v2.0/quotas/bd586f74227a48fab42852c8923c4d83.json HTTP/1.1" 200 385 0.055566
2016-11-04 09:34:18.593 17166 INFO neutron.wsgi [req-7a765cba-e322-4974-a20e-1cecc7e5fcc2 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:18] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 2825 0.189129
2016-11-04 09:34:18.775 17166 INFO neutron.wsgi [req-47b72c07-f416-4542-af31-0c9bca78d76f 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:18] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.169544
2016-11-04 09:34:18.961 17166 INFO neutron.wsgi [req-1f2865d7-c295-4514-bf1b-c20ab0a630e4 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:18] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 644 0.047286
2016-11-04 09:34:19.107 17167 INFO neutron.wsgi [req-d40d0287-5de9-4e42-8f8f-71ca72412b12 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:19] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.121981
2016-11-04 09:34:19.300 17166 INFO neutron.wsgi [req-70047cc8-5349-4c42-b394-d933aa331000 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:19] "GET /v2.0/security-groups.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 2187 0.174884
2016-11-04 09:34:19.545 17167 INFO neutron.wsgi [req-4d7562ac-7762-451e-8d81-6eebca71679b 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:19] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 1489 0.216803
2016-11-04 09:34:19.697 17166 INFO neutron.wsgi [req-12fa36cd-14b1-493b-a549-6d9b5205df80 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:19] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.140479
2016-11-04 09:34:19.921 17167 INFO neutron.wsgi [req-d4ad717c-77dc-4a1c-ae70-6fda7f3018dc 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:19] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 880 0.218823
2016-11-04 09:34:20.161 17166 INFO neutron.wsgi [req-2443b99a-020d-47fe-b407-85c44fdcca54 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:20] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.224566
2016-11-04 09:34:20.614 17167 INFO neutron.wsgi [req-d94b9348-9bae-436b-9e48-04fa62fdcdbe 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:20] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.439203
2016-11-04 09:34:20.702 17166 INFO neutron.wsgi [req-2f505294-6774-4ae3-8c33-945f0fa99199 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:34:20] "GET /v2.0/routers.json HTTP/1.1" 200 1240 0.086733
	```
	- Click allocate IP:
	```sh
	2016-11-04 09:35:10.482 17167 INFO neutron.wsgi [req-6b040cab-4e07-402a-9274-1c42b5d9d066 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:10] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.023444
2016-11-04 09:35:10.673 17166 INFO neutron.wsgi [req-5e121591-c0e5-4a1d-92ec-6fa182221a99 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:10] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 880 0.165741
2016-11-04 09:35:10.736 17167 INFO neutron.wsgi [req-4ddc2888-befc-417a-93e5-fe8606db8aa9 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:10] "GET /v2.0/quotas/bd586f74227a48fab42852c8923c4d83.json HTTP/1.1" 200 385 0.010349
2016-11-04 09:35:11.087 17166 INFO neutron.wsgi [req-4c74ccba-2471-4d8b-a663-e6b3872dd4a0 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:11] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 2825 0.146936
2016-11-04 09:35:11.383 17166 INFO neutron.wsgi [req-fdcade67-634b-4f50-8181-17cc59c9b5b2 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:11] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.281166
2016-11-04 09:35:11.540 17167 INFO neutron.wsgi [req-d61ea854-8639-45b9-873d-3dc97c93cd3f 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:11] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 644 0.032124
2016-11-04 09:35:11.619 17166 INFO neutron.wsgi [req-701f2ce0-8a0b-4a1e-a7c1-2b939a2f868a 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:11] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.068638
2016-11-04 09:35:11.750 17167 INFO neutron.wsgi [req-3749492b-ec95-4feb-aeab-5cc3553e00af 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:11] "GET /v2.0/security-groups.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 2187 0.124783
2016-11-04 09:35:12.017 17166 INFO neutron.wsgi [req-59631953-7960-4d26-9f87-c74f6ddfe316 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:12] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 1489 0.237778
2016-11-04 09:35:12.314 17166 INFO neutron.wsgi [req-e13af277-0c74-4e8d-9a43-1f07d7e40f5c 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:12] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.267672
2016-11-04 09:35:12.646 17167 INFO neutron.wsgi [req-a12ca950-12ad-4c94-88d8-1ae45b72de54 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:12] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 880 0.325015
2016-11-04 09:35:13.391 17166 INFO neutron.wsgi [req-88da676a-9e14-4e4d-97c4-dc31cd7085f9 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:13] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.732351
2016-11-04 09:35:13.573 17167 INFO neutron.wsgi [req-e6c7b618-799b-487e-a64d-07f4c18096ba 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:13] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.137117
2016-11-04 09:35:13.678 17166 INFO neutron.wsgi [req-2e8df05f-a489-4d4f-823d-a07c29d3342d 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:13] "GET /v2.0/routers.json HTTP/1.1" 200 1240 0.101247
2016-11-04 09:35:15.086 17167 INFO neutron.wsgi [req-c10b3c50-e7a5-4460-b020-cc7a826625c3 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:35:15] "POST /v2.0/floatingips.json HTTP/1.1" 201 566 1.394173
	```
	- Sau khi có IP, click Associate:
	```sh
	2016-11-04 09:36:11.807 17167 INFO neutron.wsgi [req-6bbbb58c-effc-41bf-8b2e-c49d9f001ce6 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:11] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.009572
2016-11-04 09:36:11.866 17166 INFO neutron.wsgi [req-3c18c45b-e4fb-4406-a626-b5d2baf97f36 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:11] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 977 0.044497
2016-11-04 09:36:12.026 17167 INFO neutron.wsgi [req-ac16b7b0-7aaf-4fed-9430-5bdd19f08680 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:12] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.138084
2016-11-04 09:36:12.173 17166 INFO neutron.wsgi [req-ad02059c-56fd-423e-9073-b4219ef3cf05 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:12] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.119576
2016-11-04 09:36:12.595 17166 INFO neutron.wsgi [req-06661361-5705-4a0c-a979-8b9bb17f89a6 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:12] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 2825 0.087678
2016-11-04 09:36:12.724 17166 INFO neutron.wsgi [req-be748709-873b-4fca-a924-2449ceec577d 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:12] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.116858
2016-11-04 09:36:12.904 17167 INFO neutron.wsgi [req-b746919f-e7cd-416c-8b2c-99ba9f54a273 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:12] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 880 0.147750
2016-11-04 09:36:13.137 17166 INFO neutron.wsgi [req-5fb0e1e0-49cd-4728-ab62-bdfa4bbdd4e3 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:13] "GET /v2.0/routers.json HTTP/1.1" 200 1240 0.208346
2016-11-04 09:36:13.456 17167 INFO neutron.wsgi [req-19792ec6-f790-4241-849b-41e728cc41b5 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:13] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 880 0.275079
2016-11-04 09:36:13.661 17166 INFO neutron.wsgi [req-f0dbcd67-8374-4144-85d5-cbbef37351bd 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:13] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.170642
2016-11-04 09:36:15.245 17167 INFO neutron.wsgi [req-9558e697-556a-4b53-846a-8e77d573ccef 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:15] "PUT /v2.0/floatingips/ffc5dbec-2b0e-4f7d-9a98-9dfbf569ac82.json HTTP/1.1" 200 639 1.562804
2016-11-04 09:36:15.578 17167 INFO neutron.wsgi [req-78d04f2d-8e32-4e49-8cbb-785bb4ccd5a9 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:15] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.057369
2016-11-04 09:36:15.661 17167 INFO neutron.wsgi [req-91f1336f-306c-4864-baef-00a7a5683050 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:15] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.080848
2016-11-04 09:36:15.789 17166 INFO neutron.wsgi [req-48741f8e-002e-4c20-b077-286768ca2982 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:15] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.065840
2016-11-04 09:36:15.868 17167 INFO neutron.wsgi [req-231228ed-0b54-45ff-96c2-bbfc14935c8f 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:15] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=77a12669-d998-4196-b487-655249e9e546 HTTP/1.1" 200 642 0.084778
2016-11-04 09:36:15.985 17166 INFO neutron.wsgi [req-46a46205-09cd-4bc7-89c9-7b0654491f0b 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:15] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.124834
2016-11-04 09:36:16.280 17167 INFO neutron.wsgi [req-57615c22-a95a-403f-bf97-fb7d437ba8db 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:16] "GET /v2.0/networks.json?id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 860 0.290270
2016-11-04 09:36:16.793 17166 INFO neutron.wsgi [req-0e00b4e0-514b-463c-b719-8a410b03a4e8 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:16] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.492932
2016-11-04 09:36:17.312 17166 INFO neutron.wsgi [req-50717900-4b3e-456f-b34e-5162c1d28108 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:17] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.035138
2016-11-04 09:36:19.536 17167 INFO neutron.wsgi [req-966ab203-b302-44a3-81b9-0584cbce33f6 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:19] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 2825 0.193210
2016-11-04 09:36:19.774 17167 INFO neutron.wsgi [req-d570b15e-ff92-4f6a-8ef3-60d358043e85 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:19] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.233608
2016-11-04 09:36:20.192 17166 INFO neutron.wsgi [req-ab56ff06-d31c-4314-8925-3f450f9c2703 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:20] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 2825 0.329312
2016-11-04 09:36:20.416 17167 INFO neutron.wsgi [req-b6965093-9c1a-46f6-99b1-063a7030c625 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:20] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=015c3cb1-6b2c-49dd-98fc-813487ae1aa0&port_id=77a12669-d998-4196-b487-655249e9e546&port_id=ce521cc5-e5ad-4d76-846a-a41eb3076301 HTTP/1.1" 200 1057 0.153924
2016-11-04 09:36:20.699 17166 INFO neutron.wsgi [req-5e90013f-68fc-444c-aeae-262191d91c91 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:20] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.236817
2016-11-04 09:36:20.928 17167 INFO neutron.wsgi [req-7a0202b1-de5a-40c6-8661-ad94c5cec154 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:20] "GET /v2.0/networks.json?id=304a7243-e2e8-49e4-8b01-bff41b80aecf&id=b3a81f00-7b15-48da-b071-2589f840deff&id=d3107881-083f-4bf7-a8b5-d8f0b3a46388 HTTP/1.1" 200 2141 0.226862
2016-11-04 09:36:20.941 17167 INFO neutron.notifiers.nova [-] Nova event response: {u'status': u'completed', u'code': 200, u'name': u'network-changed', u'server_uuid': u'c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2'}
2016-11-04 09:36:21.104 17167 INFO neutron.wsgi [req-4fba4728-9447-4463-8b85-3fb48a30492a 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:36:21] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83&device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.155517
2016-11-04 09:36:21.300 17166 INFO neutron.wsgi [req-eac34ef7-d176-4ad1-96a5-e1876cbcf332 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:36:21] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.351170
2016-11-04 09:36:21.325 17167 INFO neutron.wsgi [req-2d48348d-6cc0-4088-a733-ddcbb0db498e 51358268cc894066a5876114769ac86e 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:36:21] "GET /v2.0/networks.json?id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 860 0.211898
2016-11-04 09:36:21.377 17167 INFO neutron.wsgi [req-139578e4-9eaf-42f3-a92c-7e1e95d203d9 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:36:21] "GET /v2.0/floatingips.json?fixed_ip_address=192.168.2.51&port_id=77a12669-d998-4196-b487-655249e9e546 HTTP/1.1" 200 644 0.039433
2016-11-04 09:36:21.510 17167 INFO neutron.wsgi [req-4dc5bb4b-584d-42cb-b235-6141ac685739 51358268cc894066a5876114769ac86e 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:36:21] "GET /v2.0/subnets.json?id=b0569f79-0201-47bd-910f-238a4fd130ce HTTP/1.1" 200 783 0.121601
2016-11-04 09:36:21.671 17167 INFO neutron.wsgi [req-4bbb94b7-faa3-4bf5-961d-42a11bae00e5 51358268cc894066a5876114769ac86e 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:36:21] "GET /v2.0/ports.json?network_id=b3a81f00-7b15-48da-b071-2589f840deff&device_owner=network%3Adhcp HTTP/1.1" 200 1098 0.146659
	```

==> Toàn bộ Log của các sự kiện tạo network, subnet, instance.

## LOG "soft reboot" instance:
Neutron-server:
```sh
2016-11-04 09:41:00.043 17166 INFO neutron.wsgi [req-6ed4d819-dfa2-4ff5-99fc-33e393467cb7 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:00] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 2825 0.137455
2016-11-04 09:41:00.209 17166 INFO neutron.wsgi [req-66971c92-7aed-46f2-a680-102d5d061e22 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:00] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.148433
2016-11-04 09:41:00.385 17167 INFO neutron.wsgi [req-aae97895-b5de-4082-b9fa-066146255a3c 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:00] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 2825 0.137695
2016-11-04 09:41:00.472 17166 INFO neutron.wsgi [req-d07474d6-dcd4-4513-8f38-d53ea67a413a 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:00] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=015c3cb1-6b2c-49dd-98fc-813487ae1aa0&port_id=77a12669-d998-4196-b487-655249e9e546&port_id=ce521cc5-e5ad-4d76-846a-a41eb3076301 HTTP/1.1" 200 1057 0.084658
2016-11-04 09:41:00.650 17166 INFO neutron.wsgi [req-9be3a03c-b54f-4a04-bbb3-dc661feb1fbe 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:00] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.159578
2016-11-04 09:41:00.917 17167 INFO neutron.wsgi [req-eca22461-257a-4529-968e-28f5206de233 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:00] "GET /v2.0/networks.json?id=304a7243-e2e8-49e4-8b01-bff41b80aecf&id=b3a81f00-7b15-48da-b071-2589f840deff&id=d3107881-083f-4bf7-a8b5-d8f0b3a46388 HTTP/1.1" 200 2141 0.283509
2016-11-04 09:41:01.093 17166 INFO neutron.wsgi [req-c3f0538c-5518-48a4-8a17-2e184d52b3bf 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:01] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.168629
2016-11-04 09:41:02.555 17167 INFO neutron.wsgi [req-af509719-0586-4c9a-b14a-2fe16dde7d77 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:41:02] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83&device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.155104
2016-11-04 09:41:02.986 17167 INFO neutron.wsgi [req-106cd264-8976-4171-857f-3ea6684fbb1b 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.81 - - [04/Nov/2016 09:41:02] "GET /v2.0/networks.json?id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 860 0.407343
2016-11-04 09:41:03.047 17167 INFO neutron.wsgi [req-01536b42-692b-4b8c-9694-73c276a7fa63 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:41:03] "GET /v2.0/floatingips.json?fixed_ip_address=192.168.2.51&port_id=77a12669-d998-4196-b487-655249e9e546 HTTP/1.1" 200 644 0.047506
2016-11-04 09:41:03.207 17167 INFO neutron.wsgi [req-520c7651-3808-4af7-b513-d74c302c04c3 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.81 - - [04/Nov/2016 09:41:03] "GET /v2.0/subnets.json?id=b0569f79-0201-47bd-910f-238a4fd130ce HTTP/1.1" 200 783 0.150025
2016-11-04 09:41:03.322 17167 INFO neutron.wsgi [req-4acdc437-3a1f-4831-b1e7-08b8012ff707 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:03] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.121996
2016-11-04 09:41:03.443 17167 INFO neutron.wsgi [req-d939ac54-629e-4f91-9206-c70bb42c4bac 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.81 - - [04/Nov/2016 09:41:03] "GET /v2.0/ports.json?network_id=b3a81f00-7b15-48da-b071-2589f840deff&device_owner=network%3Adhcp HTTP/1.1" 200 1098 0.177789
2016-11-04 09:41:03.506 17167 INFO neutron.wsgi [req-b965dbed-1440-42a7-8035-425fc3d238c2 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:03] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.167731
2016-11-04 09:41:03.650 17167 INFO neutron.wsgi [req-68ad158f-e039-41ad-b937-db28cb0386e4 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:03] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.056357
2016-11-04 09:41:03.705 17166 INFO neutron.wsgi [req-d1311d2a-0795-49f0-9634-425082dddd3d 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:03] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=77a12669-d998-4196-b487-655249e9e546 HTTP/1.1" 200 644 0.049907
2016-11-04 09:41:03.802 17167 INFO neutron.wsgi [req-dcde2dca-45eb-48bc-8307-fe7f20fd08ef 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:03] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.107351
2016-11-04 09:41:03.919 17166 INFO neutron.wsgi [req-d3c3cd4e-713a-47e1-8001-98d91a9e49b7 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:03] "GET /v2.0/networks.json?id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 860 0.126750
2016-11-04 09:41:04.297 17166 INFO neutron.wsgi [req-1c68d954-8e22-4cfa-ac14-a878e736c94a 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:04] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.372479
2016-11-04 09:41:04.456 17167 INFO neutron.wsgi [req-2b095e22-5c56-4bbd-b11c-d421b956d3e9 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:04] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.020783
2016-11-04 09:41:05.152 17166 INFO neutron.wsgi [req-fde97142-b15f-4044-b935-825d8afa5647 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:05] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 2825 0.083173
2016-11-04 09:41:05.281 17166 INFO neutron.wsgi [req-8a2e8cab-186f-4727-b0da-9760e49fbb08 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:05] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.120252
2016-11-04 09:41:05.424 17167 INFO neutron.wsgi [req-b1e86b9c-3b87-4dc7-a959-90108a8245f8 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:05] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 2825 0.112010
2016-11-04 09:41:05.494 17166 INFO neutron.wsgi [req-906638d0-87ad-4756-8f51-e53035310dcf 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:05] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=015c3cb1-6b2c-49dd-98fc-813487ae1aa0&port_id=77a12669-d998-4196-b487-655249e9e546&port_id=ce521cc5-e5ad-4d76-846a-a41eb3076301 HTTP/1.1" 200 1057 0.057151
2016-11-04 09:41:05.601 17167 INFO neutron.wsgi [req-902163c1-1da2-41ba-a051-3f7150d3ce9c 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:05] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.094208
2016-11-04 09:41:05.741 17166 INFO neutron.wsgi [req-aa0c4aa0-2242-4688-a20d-d7dddc36d3ac 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:05] "GET /v2.0/networks.json?id=304a7243-e2e8-49e4-8b01-bff41b80aecf&id=b3a81f00-7b15-48da-b071-2589f840deff&id=d3107881-083f-4bf7-a8b5-d8f0b3a46388 HTTP/1.1" 200 2141 0.136027
2016-11-04 09:41:05.894 17167 INFO neutron.wsgi [req-ea0de665-1eea-467a-ae6b-564eaac89d72 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:05] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.140043
2016-11-04 09:41:08.664 17167 INFO neutron.wsgi [req-370881f0-26a7-47f2-80f3-f1866441ce39 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:08] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.056912
2016-11-04 09:41:08.757 17167 INFO neutron.wsgi [req-aed295e7-95a1-48cb-98fb-d091cdbba0bd 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:08] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.089719
2016-11-04 09:41:08.874 17166 INFO neutron.wsgi [req-7d7c7f78-18b6-477e-aa10-3c663e1b80fe 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:08] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.058277
2016-11-04 09:41:08.931 17167 INFO neutron.wsgi [req-7df1c0a4-b371-421f-b80c-77c6862df4b0 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:08] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=77a12669-d998-4196-b487-655249e9e546 HTTP/1.1" 200 644 0.046886
2016-11-04 09:41:08.996 17167 INFO neutron.wsgi [req-ac612ae0-8bf2-43cb-a823-575cfa968ad8 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:08] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.041715
2016-11-04 09:41:09.076 17166 INFO neutron.wsgi [req-412f3d85-4972-44c6-b92e-504a26916dc5 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:09] "GET /v2.0/networks.json?id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 860 0.063721
2016-11-04 09:41:09.139 17167 INFO neutron.wsgi [req-6ec3be9e-5b7b-437c-9690-0304c4cc1c7c 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:09] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.055894
2016-11-04 09:41:09.209 17167 INFO neutron.wsgi [req-0e328723-e549-4688-88f2-721b8381afd8 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:09] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.011623
2016-11-04 09:41:09.675 17167 INFO neutron.wsgi [req-82993b8f-f5e1-4d07-b460-a94f768c4860 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:41:09] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b HTTP/1.1" 200 1092 0.150435
2016-11-04 09:41:10.004 17167 INFO neutron.wsgi [req-77805eb9-350f-4681-958f-a4122e6908e9 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:41:10] "GET /v2.0/networks.json?id=d3107881-083f-4bf7-a8b5-d8f0b3a46388 HTTP/1.1" 200 880 0.307839
2016-11-04 09:41:10.118 17167 INFO neutron.wsgi [req-fed5dac3-dd4d-4acb-8a86-922072dcbad8 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:41:10] "GET /v2.0/floatingips.json?fixed_ip_address=172.16.69.202&port_id=015c3cb1-6b2c-49dd-98fc-813487ae1aa0 HTTP/1.1" 200 232 0.098166
2016-11-04 09:41:10.249 17167 INFO neutron.wsgi [req-4085cb83-7a99-4c79-bdeb-aeef0eebd74f 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:41:10] "GET /v2.0/subnets.json?id=cf7096d2-5a2f-40d6-b462-3ed605a677ff HTTP/1.1" 200 786 0.121017
2016-11-04 09:41:10.381 17167 INFO neutron.wsgi [req-b606e0c9-1298-4dd3-8c33-4d87d4c60008 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 09:41:10] "GET /v2.0/ports.json?network_id=d3107881-083f-4bf7-a8b5-d8f0b3a46388&device_owner=network%3Adhcp HTTP/1.1" 200 1099 0.109561
2016-11-04 09:41:12.729 17167 INFO neutron.wsgi [req-948e341c-8b59-4309-94b6-fb22d08dc6ce 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:12] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.074592
2016-11-04 09:41:12.810 17167 INFO neutron.wsgi [req-0e2fae82-4d7b-483e-a9ae-4e63051e2c56 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:12] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.070463
2016-11-04 09:41:12.936 17166 INFO neutron.wsgi [req-96befd9e-b943-4555-a014-48487c8cf91b 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:12] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.067563
2016-11-04 09:41:13.001 17167 INFO neutron.wsgi [req-bb64c24c-c2d8-45cc-8358-e484bd80d8f6 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:13] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=77a12669-d998-4196-b487-655249e9e546 HTTP/1.1" 200 644 0.054357
2016-11-04 09:41:13.102 17167 INFO neutron.wsgi [req-141a9800-2f4a-46c4-a1dc-c2370fe590e6 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:13] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.090315
2016-11-04 09:41:13.238 17166 INFO neutron.wsgi [req-b5bef13d-8fd8-4039-bf95-72d21eeefe1b 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:13] "GET /v2.0/networks.json?id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 860 0.114367
2016-11-04 09:41:13.441 17167 INFO neutron.wsgi [req-3f5879b7-7af8-441a-ba1a-c57e05f34a9e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:13] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.187504
2016-11-04 09:41:13.704 17167 INFO neutron.wsgi [req-e3b5cc1d-632b-4ba2-90aa-6cb57f42d974 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:13] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.009544
2016-11-04 09:41:20.978 17167 INFO neutron.wsgi [req-fdc54be3-cc09-4f3a-83be-2eaa02385a44 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:20] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 1.045866
2016-11-04 09:41:21.098 17167 INFO neutron.wsgi [req-c348f108-2664-49df-93d1-016ac8bb6649 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:21] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.113868
2016-11-04 09:41:22.247 17166 INFO neutron.wsgi [req-aa222882-ed85-4dc2-bfd9-a33da76f5826 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:22] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.925744
2016-11-04 09:41:22.365 17166 INFO neutron.wsgi [req-80897a33-3b48-4255-900d-666b66a71245 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.80 - - [04/Nov/2016 09:41:22] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.112183
```
LOG trên /var/log/neutron/neutron-metadata-agent.log của controller
```sh
2016-11-04 09:41:21.129 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:21] "GET /2009-04-04/meta-data/instance-id HTTP/1.1" 200 127 1.491930
2016-11-04 09:41:22.401 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/meta-data/public-keys HTTP/1.1" 404 176 1.229277
2016-11-04 09:41:22.451 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/meta-data/instance-id HTTP/1.1" 200 127 0.014381
2016-11-04 09:41:22.483 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/meta-data/ami-launch-index HTTP/1.1" 200 117 0.015333
2016-11-04 09:41:22.515 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/meta-data/instance-type HTTP/1.1" 200 123 0.015282
2016-11-04 09:41:22.552 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/meta-data/local-ipv4 HTTP/1.1" 200 129 0.015890
2016-11-04 09:41:22.587 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/meta-data/public-ipv4 HTTP/1.1" 200 130 0.016743
2016-11-04 09:41:22.644 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/meta-data/hostname HTTP/1.1" 200 138 0.016057
2016-11-04 09:41:22.677 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/meta-data/local-hostname HTTP/1.1" 200 138 0.009935
2016-11-04 09:41:22.711 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/user-data HTTP/1.1" 404 176 0.009579
2016-11-04 09:41:22.751 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/meta-data/block-device-mapping HTTP/1.1" 200 124 0.010042
2016-11-04 09:41:22.793 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/meta-data/block-device-mapping/ami HTTP/1.1" 200 119 0.017575
2016-11-04 09:41:22.818 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/meta-data/block-device-mapping/root HTTP/1.1" 200 124 0.007945
2016-11-04 09:41:22.850 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/meta-data/public-hostname HTTP/1.1" 200 138 0.009129
2016-11-04 09:41:22.891 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 09:41:22] "GET /2009-04-04/meta-data/placement/availability-zone HTTP/1.1" 200 120 0.010325
```
LOG trên /var/log/openvswitch/ovs-vswitchd.log của compute
```sh
2016-11-04T02:41:06.197Z|00094|netlink_notifier|WARN|received bad netlink message
2016-11-04T02:41:06.197Z|00095|netlink_notifier|WARN|received bad netlink message
```

## LOG khi xóa instance
### Đối với instance không nằm trong bất ký tennant network nào, do khi tạo instance không chọn zone nova:
LOG tại neutron-server
```sh
2016-11-04 09:57:56.032 17166 INFO neutron.wsgi [req-39cd00e6-36c8-44b5-a924-a5f0fda14a29 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:56] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 2825 0.153437
2016-11-04 09:57:56.208 17166 INFO neutron.wsgi [req-d2e4e07a-4e52-4ba4-9c7f-290dffd1f3a5 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:56] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.159509
2016-11-04 09:57:56.576 17167 INFO neutron.wsgi [req-5c7ce817-5e11-4071-8756-24a60d5cc547 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:56] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b&device_id=b445a2c9-b152-43e8-82d2-c3d4982989c6 HTTP/1.1" 200 2825 0.333079
2016-11-04 09:57:56.650 17166 INFO neutron.wsgi [req-81275535-4d68-4768-966d-777197064ad9 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:56] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=015c3cb1-6b2c-49dd-98fc-813487ae1aa0&port_id=77a12669-d998-4196-b487-655249e9e546&port_id=ce521cc5-e5ad-4d76-846a-a41eb3076301 HTTP/1.1" 200 1057 0.053128
2016-11-04 09:57:56.769 17167 INFO neutron.wsgi [req-c98bb14a-6422-4ff4-8ccf-8f61bd19cecf 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:56] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.101935
2016-11-04 09:57:56.953 17166 INFO neutron.wsgi [req-5663d9f6-deba-43d2-80a4-f4ff542a6b20 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:56] "GET /v2.0/networks.json?id=304a7243-e2e8-49e4-8b01-bff41b80aecf&id=b3a81f00-7b15-48da-b071-2589f840deff&id=d3107881-083f-4bf7-a8b5-d8f0b3a46388 HTTP/1.1" 200 2141 0.165046
2016-11-04 09:57:57.133 17167 INFO neutron.wsgi [req-cffacc78-a93a-4dfb-9629-32cc8df9db02 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:57] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.165224
2016-11-04 09:57:58.547 17167 INFO neutron.wsgi [req-bed29b8b-6506-4404-82b8-fd5b926ada82 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:58] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.086644
2016-11-04 09:57:58.660 17167 INFO neutron.wsgi [req-ef3a6ab9-6ba3-457d-bc63-8b2e62f82708 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:58] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.105834
2016-11-04 09:57:58.823 17166 INFO neutron.wsgi [req-2348ffd3-f422-4cd2-8eb8-55fc95674505 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:58] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2 HTTP/1.1" 200 1091 0.074307
2016-11-04 09:57:58.890 17166 INFO neutron.wsgi [req-955f1c6c-4215-4d9a-93ef-ba34496117b7 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:58] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=77a12669-d998-4196-b487-655249e9e546 HTTP/1.1" 200 644 0.055052
2016-11-04 09:57:59.017 17167 INFO neutron.wsgi [req-4af01a53-9911-446f-9cd1-ccdba551d2ab 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:59] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.118745
2016-11-04 09:57:59.238 17166 INFO neutron.wsgi [req-928d86a2-e1e6-41f2-a081-2763e49dd18e 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:59] "GET /v2.0/networks.json?id=b3a81f00-7b15-48da-b071-2589f840deff HTTP/1.1" 200 860 0.197152
2016-11-04 09:57:59.485 17167 INFO neutron.wsgi [req-76f2318a-5127-4d35-9753-7932e69b448b 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:59] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.241798
2016-11-04 09:57:59.803 17166 INFO neutron.wsgi [req-72edc55c-488a-4f59-b175-c629ee7c4ed2 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:57:59] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.010146
2016-11-04 09:58:00.923 17167 INFO neutron.wsgi [req-5348994a-0153-4256-8188-737a00d4e7d0 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:58:00] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b HTTP/1.1" 200 2825 0.112232
2016-11-04 09:58:01.086 17167 INFO neutron.wsgi [req-65418567-37e6-4551-ad34-21d25fdd8898 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:58:01] "GET /v2.0/security-groups.json?id=4c770efd-2459-48c9-aaf9-b429d375a996 HTTP/1.1" 200 2187 0.149541
2016-11-04 09:58:01.308 17167 INFO neutron.wsgi [req-f92db049-8a0d-4166-ba91-8e39bdbb7d0d 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:58:01] "GET /v2.0/ports.json?device_id=c8f2ef2a-666d-4d00-b30d-7aed8d9d65c2&device_id=4beeb754-07b9-40fb-92f0-4cdc761399fe&device_id=35a6b2b6-e87c-40a3-87d0-94e1d669340b HTTP/1.1" 200 2825 0.173732
2016-11-04 09:58:01.468 17166 INFO neutron.wsgi [req-82069a3a-f46a-4591-9516-9d4f494f61f1 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:58:01] "GET /v2.0/floatingips.json?tenant_id=bd586f74227a48fab42852c8923c4d83&port_id=015c3cb1-6b2c-49dd-98fc-813487ae1aa0&port_id=77a12669-d998-4196-b487-655249e9e546&port_id=ce521cc5-e5ad-4d76-846a-a41eb3076301 HTTP/1.1" 200 1057 0.152477
2016-11-04 09:58:01.688 17167 INFO neutron.wsgi [req-691e492d-75f5-4919-a0a0-d29001e6b719 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:58:01] "GET /v2.0/ports.json?tenant_id=bd586f74227a48fab42852c8923c4d83 HTTP/1.1" 200 7131 0.209158
2016-11-04 09:58:02.139 17166 INFO neutron.wsgi [req-0c7661fe-c699-4814-9b3e-eb03c2574ca0 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:58:02] "GET /v2.0/networks.json?id=304a7243-e2e8-49e4-8b01-bff41b80aecf&id=b3a81f00-7b15-48da-b071-2589f840deff&id=d3107881-083f-4bf7-a8b5-d8f0b3a46388 HTTP/1.1" 200 2141 0.443865
2016-11-04 09:58:02.279 17167 INFO neutron.wsgi [req-acd89884-c2a3-40b6-ba9f-86bbc5d3b757 013aa135935b4cfd874a59a1921fc094 bd586f74227a48fab42852c8923c4d83 - - -] 10.10.10.80 - - [04/Nov/2016 09:58:02] "GET /v2.0/subnets.json HTTP/1.1" 200 1903 0.136865
```


## Test trường hợp tạo 1 user tannt trên Horizon và gán vào group demo
- Tạo một tenant network
Log tại neutron khi click create
```sh
2016-11-04 10:31:30.952 17167 INFO neutron.wsgi [req-e5a5cfa4-0971-4aad-a610-f245e63ad045 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:31:30] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 1.465379
2016-11-04 10:31:31.048 17166 INFO neutron.wsgi [req-34707c0c-ff95-4d57-b089-98e9997af7e0 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:31:31] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.078819
```

click tab subnet:
```sh
2016-11-04 10:32:21.335 17167 INFO neutron.wsgi [req-360b7bbc-cb83-4638-a536-b2e8396cace5 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:32:21] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.020349
2016-11-04 10:32:21.429 17167 INFO neutron.wsgi [req-e0602519-2e87-4c79-a4a4-30c6ac15280f b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:32:21] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.071527
```
click subnet detail:
```sh
2016-11-04 10:33:31.094 17166 INFO neutron.wsgi [req-d1bdb0e5-20ce-492f-9102-2ec3ed273b30 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:33:31] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.025352
2016-11-04 10:33:31.164 17167 INFO neutron.wsgi [req-8034ac73-4422-4a22-9220-a0f586783e01 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:33:31] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.060056
```
click create:
```sh
2016-11-04 10:34:24.744 17167 INFO neutron.wsgi [req-d0d7dc0d-67ef-4001-a37f-09cbf4852128 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:24] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.020762
2016-11-04 10:34:24.825 17166 INFO neutron.wsgi [req-4dfa0f98-7480-43c8-baad-292cceebc0ab b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:24] "GET /v2.0/subnetpools.json HTTP/1.1" 200 232 0.060677
2016-11-04 10:34:25.102 17167 INFO neutron.plugins.ml2.db [req-626de132-423c-48a0-aefc-2ae2529d25d8 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] Added segment 0d40a42b-f9e6-411c-b72e-9a0276823af7 of type gre for network e9a14ace-1fa6-427c-98d2-1174ec03507c
2016-11-04 10:34:25.315 17167 INFO neutron.wsgi [req-626de132-423c-48a0-aefc-2ae2529d25d8 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:25] "POST /v2.0/networks.json HTTP/1.1" 201 716 0.471076
2016-11-04 10:34:27.292 17167 INFO neutron.wsgi [req-59765e57-d70e-449a-8abf-39761c7827aa b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:27] "POST /v2.0/subnets.json HTTP/1.1" 201 786 1.939122
2016-11-04 10:34:27.706 17167 INFO neutron.wsgi [req-c766e02b-e9cb-4b75-81ca-6f025e2c79c1 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:27] "GET /v2.0/networks.json?shared=False&tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 758 0.323761
2016-11-04 10:34:27.957 17166 INFO neutron.wsgi [req-7ee65788-11d8-4705-a502-b7c0bc5a0039 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:27] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.248050
2016-11-04 10:34:28.159 17166 INFO neutron.wsgi [req-9faaf85d-f983-4e6f-99aa-b691d46d3c10 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:28] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 772 0.191808
2016-11-04 10:34:28.290 17167 INFO neutron.wsgi [req-2e73adf0-c88a-45e4-85b4-de019f79a9ee b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:28] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.129306
2016-11-04 10:34:28.648 17166 INFO neutron.wsgi [req-d3c9a296-e94c-4377-9e28-2dcc30a1c12e b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:28] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 772 0.343105
2016-11-04 10:34:29.031 17167 INFO neutron.wsgi [req-15185461-0a0f-47ab-94e0-6f4b533678bd b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:29] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.376186
2016-11-04 10:34:29.670 17167 INFO neutron.wsgi [req-46bccbf8-3424-4bab-b7cf-7915adbd0792 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:29] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.046947
2016-11-04 10:34:30.259 17167 INFO neutron.wsgi [req-d5ab39c0-5e4a-495d-b64c-766429fae94b b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:30] "GET /v2.0/quotas/bfb67b8eaead4f1684710db4608d1c48.json HTTP/1.1" 200 385 0.057295
2016-11-04 10:34:30.663 17166 INFO neutron.wsgi [req-a4cd5336-0051-4198-8789-95edf6d1d90f b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:30] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 232 0.046404
2016-11-04 10:34:31.266 17167 INFO neutron.wsgi [req-11cdd103-fc4e-429b-821e-6a11ba1650f6 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:31] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 941 0.602557
2016-11-04 10:34:31.665 17166 INFO neutron.wsgi [req-3ac7fa1a-c1ee-4234-871f-881febbce4c0 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:31] "GET /v2.0/security-groups.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 1841 0.373487
2016-11-04 10:34:32.077 17167 INFO neutron.wsgi [req-38c17d1e-cc27-47e0-9939-2f684c113cd9 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:32] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 758 0.396495
2016-11-04 10:34:32.266 17166 INFO neutron.wsgi [req-62c019f1-04a7-430b-bcb0-7e70debff91d b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:32] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.195847
2016-11-04 10:34:32.402 17167 INFO neutron.wsgi [req-4d8dd5fe-66f3-466c-b376-6d1fc181619c b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:32] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 772 0.143687
2016-11-04 10:34:32.541 17166 INFO neutron.wsgi [req-ba924d28-1d70-452c-84b4-a2c4438bde59 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:32] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.134139
2016-11-04 10:34:32.665 17167 INFO neutron.wsgi [req-e583c0a0-2f45-43d7-8c90-d8cd30a6dccc b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:32] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.130581
2016-11-04 10:34:32.784 17166 INFO neutron.wsgi [req-b6c791f7-28c6-4a34-9f53-e6d699363e4d b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:34:32] "GET /v2.0/routers.json HTTP/1.1" 200 228 0.126241
```
Phát sinh Log tại /var/log/neutron/openvswitch-agent.log trên controller
```sh
2016-11-04 10:34:29.999 2214 INFO neutron.agent.securitygroups_rpc [req-2e042440-cfdd-4959-98a0-c21fb11d0a7c - - - - -] Provider rule updated
2016-11-04 10:34:32.602 2214 INFO neutron.agent.securitygroups_rpc [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Refresh firewall rules
2016-11-04 10:34:33.943 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Configuration for devices up [] and devices down [] completed.
2016-11-04 10:34:35.545 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Port 095b13c7-6e91-4ddb-b8d6-124279eaf327 updated. Details: {u'profile': {}, u'network_qos_policy_id': None, u'qos_policy_id': None, u'allowed_address_pairs': [], u'admin_state_up': True, u'network_id': u'e9a14ace-1fa6-427c-98d2-1174ec03507c', u'segmentation_id': 379, u'device_owner': u'network:dhcp', u'physical_network': None, u'mac_address': u'fa:16:3e:f6:40:e0', u'device': u'095b13c7-6e91-4ddb-b8d6-124279eaf327', u'port_security_enabled': False, u'port_id': u'095b13c7-6e91-4ddb-b8d6-124279eaf327', u'fixed_ips': [{u'subnet_id': u'9aa3a9b4-9ee0-41ee-b778-b0e1f403b446', u'ip_address': u'192.168.2.50'}], u'network_type': u'gre', u'security_groups': []}
2016-11-04 10:34:35.550 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Assigning 5 as local vlan for net-id=e9a14ace-1fa6-427c-98d2-1174ec03507c
2016-11-04 10:34:37.109 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Skipping ARP spoofing rules for port 'tap095b13c7-6e' because it has port security disabled
2016-11-04 10:34:39.107 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Configuration for devices up [u'095b13c7-6e91-4ddb-b8d6-124279eaf327'] and devices down [] completed.
```
Log tại /var/log/openvswitch/ovs-vswitchd.log trên controller
```sh
2016-11-04T03:34:31.929Z|00361|bridge|INFO|bridge br-int: added interface tap095b13c7-6e on port 15
2016-11-04T03:34:34.078Z|00362|netdev_linux|WARN|tap095b13c7-6e: removing policing failed: No such device
2016-11-04T03:34:34.116Z|00363|netdev_linux|INFO|ioctl(SIOCGIFHWADDR) on tap095b13c7-6e device failed: No such device
2016-11-04T03:34:34.133Z|00364|netdev_linux|WARN|ioctl(SIOCGIFINDEX) on tap095b13c7-6e device failed: No such device
2016-11-04T03:34:35.752Z|00365|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 modifications)
2016-11-04T03:34:35.933Z|00366|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-04T03:34:36.375Z|00367|netdev_linux|WARN|tap095b13c7-6e: removing policing failed: No such device
2016-11-04T03:34:37.286Z|00368|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:34:37.582Z|00369|ofp_util|INFO|normalization changed ofp_match, details:
2016-11-04T03:34:37.582Z|00370|ofp_util|INFO| pre: in_port=15,nw_proto=58,tp_src=136
2016-11-04T03:34:37.582Z|00371|ofp_util|INFO|post: in_port=15
2016-11-04T03:34:37.584Z|00372|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:34:37.850Z|00373|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:34:38.108Z|00374|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:34:38.382Z|00375|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
```
Log tại /var/log/neutron/openvswitch-agent.log trên compute
```sh
2016-11-04 10:34:29.957 1900 INFO neutron.agent.securitygroups_rpc [req-2e042440-cfdd-4959-98a0-c21fb11d0a7c - - - - -] Provider rule updated
2016-11-04 10:34:31.050 1900 INFO neutron.agent.securitygroups_rpc [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Refresh firewall rules
2016-11-04 10:34:32.388 1900 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Configuration for devices up [] and devices down [] completed.
```

Create router
Log tại neutron server:
```sh
2016-11-04 10:39:55.893 17166 INFO neutron.wsgi [req-fe9ff939-9618-4573-9dfa-e9cdbd035715 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:39:55] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 772 0.356640
2016-11-04 10:39:56.103 17167 INFO neutron.wsgi [req-032a3d2c-3ee5-4591-a7f0-a45c74513de2 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:39:56] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.214767
```
create complete:
```sh
2016-11-04 10:41:00.017 17166 INFO neutron.wsgi [req-7aff6460-bf5f-4d12-8c59-cd11d5b923e6 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:00] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 772 0.183589
2016-11-04 10:41:00.334 17166 INFO neutron.wsgi [req-5b851524-c89f-4f7c-b7b3-6ae8315d398e b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:00] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.298018
2016-11-04 10:41:00.697 17166 INFO neutron.wsgi [req-3f765703-2f02-4623-88d5-8f780c812413 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:00] "POST /v2.0/routers.json HTTP/1.1" 201 516 0.354952
2016-11-04 10:41:04.191 17166 INFO neutron.wsgi [req-faddeb89-2ca2-4e47-99ee-199710fb0c3a b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:04] "PUT /v2.0/routers/e47ef1e7-3709-4cef-bb0c-8c55acba97ea.json HTTP/1.1" 200 692 3.462640
2016-11-04 10:41:04.783 17166 INFO neutron.wsgi [req-5f76e251-45d4-45a7-ba56-98c4b1ac77f5 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:04] "GET /v2.0/routers.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48&search_opts=None HTTP/1.1" 200 701 0.348896
2016-11-04 10:41:04.970 17166 INFO neutron.wsgi [req-9c3ee6c8-97ee-476a-bb57-7575aa16722c b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:04] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 772 0.179424
2016-11-04 10:41:05.096 17167 INFO neutron.wsgi [req-6d5e4788-f3ed-4d10-9372-fb67013852a8 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:05] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.118254
2016-11-04 10:41:05.906 17166 INFO neutron.wsgi [req-80fba74d-3e89-42b0-9111-1da81448c119 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:05] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.047429
2016-11-04 10:41:06.617 17167 INFO neutron.wsgi [req-48cc8f46-d76b-4bd5-a9f9-50e73b403f88 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:06] "GET /v2.0/quotas/bfb67b8eaead4f1684710db4608d1c48.json HTTP/1.1" 200 385 0.066481
2016-11-04 10:41:07.270 17166 INFO neutron.wsgi [req-2d24dd85-0360-4fdf-9c6f-001b835db1e1 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:07] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 232 0.051349
2016-11-04 10:41:07.417 17167 INFO neutron.wsgi [req-b6353293-ad76-4bb1-ab1c-a242df529cfb b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:07] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 943 0.133038
2016-11-04 10:41:07.674 17166 INFO neutron.wsgi [req-f65ff8cc-65ad-4afe-9fca-beb01d788b3a b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:07] "GET /v2.0/security-groups.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 1841 0.240116
2016-11-04 10:41:07.905 17167 INFO neutron.wsgi [req-c45be7d3-2b1e-4b9e-9fb4-0a61f7199c54 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:07] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 758 0.198007
2016-11-04 10:41:08.122 17166 INFO neutron.wsgi [req-33485b4b-e5c8-4d7b-a139-31ab557d4f9a b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:08] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.222501
2016-11-04 10:41:08.572 17167 INFO neutron.wsgi [req-243b478d-5cde-4189-8e98-e7516b050439 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:08] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 772 0.443616
2016-11-04 10:41:09.146 17166 INFO neutron.wsgi [req-30d86525-2140-4784-b1fb-2b0c4384d77e b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:09] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.553750
2016-11-04 10:41:09.459 17167 INFO neutron.wsgi [req-b2053bc5-4dea-45a0-bd74-df0c0486163a b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:09] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.284128
2016-11-04 10:41:09.669 17166 INFO neutron.wsgi [req-2166fcb5-a2f0-4a2d-a0c4-0e3bd46f8380 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:41:09] "GET /v2.0/routers.json HTTP/1.1" 200 701 0.198350
```
Thêm log tại /var/log/neutron/openvswitch-agent.log controller
```sh
2016-11-04 10:41:14.116 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Port 4ed640a1-de89-4fc2-a744-f99e652a4fe6 updated. Details: {u'profile': {}, u'network_qos_policy_id': None, u'qos_policy_id': None, u'allowed_address_pairs': [], u'admin_state_up': True, u'network_id': u'd3107881-083f-4bf7-a8b5-d8f0b3a46388', u'segmentation_id': None, u'device_owner': u'network:router_gateway', u'physical_network': u'external', u'mac_address': u'fa:16:3e:c4:16:d9', u'device': u'4ed640a1-de89-4fc2-a744-f99e652a4fe6', u'port_security_enabled': False, u'port_id': u'4ed640a1-de89-4fc2-a744-f99e652a4fe6', u'fixed_ips': [{u'subnet_id': u'cf7096d2-5a2f-40d6-b462-3ed605a677ff', u'ip_address': u'172.16.69.206'}], u'network_type': u'flat', u'security_groups': []}
2016-11-04 10:41:15.673 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Skipping ARP spoofing rules for port 'qg-4ed640a1-de' because it has port security disabled
2016-11-04 10:41:17.776 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Configuration for devices up [u'4ed640a1-de89-4fc2-a744-f99e652a4fe6'] and devices down [] completed.
```
Và log tại /var/log/openvswitch/ovs-vswitchd.log controller
```sh
2016-11-04T03:41:11.641Z|00376|bridge|INFO|bridge br-int: added interface qg-4ed640a1-de on port 16
2016-11-04T03:41:12.691Z|00377|netdev_linux|INFO|ioctl(SIOCGIFHWADDR) on qg-4ed640a1-de device failed: No such device
2016-11-04T03:41:12.702Z|00378|netdev_linux|WARN|ioctl(SIOCGIFINDEX) on qg-4ed640a1-de device failed: No such device
2016-11-04T03:41:14.776Z|00379|netdev_linux|WARN|qg-4ed640a1-de: removing policing failed: No such device
2016-11-04T03:41:16.078Z|00380|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:41:16.366Z|00381|ofp_util|INFO|normalization changed ofp_match, details:
2016-11-04T03:41:16.366Z|00382|ofp_util|INFO| pre: in_port=16,nw_proto=58,tp_src=136
2016-11-04T03:41:16.366Z|00383|ofp_util|INFO|post: in_port=16
2016-11-04T03:41:16.370Z|00384|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:41:16.580Z|00385|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:41:16.834Z|00386|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:41:17.090Z|00387|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
```
Click vào router vừa tạo, log tại neutron-server
```sh
2016-11-04 10:44:36.396 17166 INFO neutron.wsgi [req-38f8fca0-d05f-4d70-bf1b-54bb8667f782 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:44:36] "GET /v2.0/routers/e47ef1e7-3709-4cef-bb0c-8c55acba97ea.json HTTP/1.1" 200 698 2.461569
2016-11-04 10:44:36.575 17167 INFO neutron.wsgi [req-a1f2707a-e775-4454-869d-7752b9e75676 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:44:36] "GET /v2.0/networks/d3107881-083f-4bf7-a8b5-d8f0b3a46388.json HTTP/1.1" 200 769 0.168532
2016-11-04 10:44:36.708 17166 INFO neutron.wsgi [req-af277d89-9a4f-4b2b-86a6-b9bdb41afe65 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:44:36] "GET /v2.0/ports.json?device_id=e47ef1e7-3709-4cef-bb0c-8c55acba97ea HTTP/1.1" 200 226 0.124130
2016-11-04 10:44:36.744 17166 INFO neutron.wsgi [req-dc92f9cd-3934-4715-958f-dff24269c458 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:44:36] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.017455
```
Chuyển tab interface và add interface:
```sh
2016-11-04 10:45:31.410 17167 INFO neutron.wsgi [req-68d5c91d-82be-4e9b-a4b5-53320e08e0ab b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:45:31] "GET /v2.0/routers/e47ef1e7-3709-4cef-bb0c-8c55acba97ea.json HTTP/1.1" 200 698 0.192074
2016-11-04 10:45:31.634 17166 INFO neutron.wsgi [req-0befb502-a718-4542-8733-c88a1e8cfc08 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:45:31] "GET /v2.0/networks.json?shared=False&tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 758 0.207151
2016-11-04 10:45:32.166 17167 INFO neutron.wsgi [req-535ba0a7-97b3-4b7c-aa80-faeb32222e2c b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:45:32] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.519662
2016-11-04 10:45:32.492 17166 INFO neutron.wsgi [req-c466b7b6-b1c7-4581-9889-30d57132e57d b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:45:32] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 772 0.317236
2016-11-04 10:45:32.776 17166 INFO neutron.wsgi [req-7aeb566d-c92a-429c-b780-70628a91b726 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:45:32] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.256988
2016-11-04 10:45:32.935 17167 INFO neutron.wsgi [req-f2890705-212a-461b-bef4-3721f113235b b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:45:32] "GET /v2.0/ports.json?device_id=e47ef1e7-3709-4cef-bb0c-8c55acba97ea HTTP/1.1" 200 226 0.106298
```
Chọn subnet ở network vừa tạo và submit:
```sh
2016-11-04 10:46:25.989 17166 INFO neutron.wsgi [req-6b97ad16-9bc1-4af7-85a0-2e36526250b2 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:46:25] "GET /v2.0/routers/e47ef1e7-3709-4cef-bb0c-8c55acba97ea.json HTTP/1.1" 200 698 0.302907
2016-11-04 10:46:26.488 17166 INFO neutron.wsgi [req-d93ddb77-c220-474a-8a4a-f0b0c58384ff b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:46:26] "GET /v2.0/networks.json?shared=False&tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 758 0.461310
2016-11-04 10:46:26.748 17167 INFO neutron.wsgi [req-dd57f64d-9685-4efe-bda7-a1c397c80ab1 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:46:26] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.259550
2016-11-04 10:46:27.049 17166 INFO neutron.wsgi [req-a06a27b8-abf8-4b76-bacd-04ccb3281bcf b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:46:27] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 772 0.282939
2016-11-04 10:46:27.198 17167 INFO neutron.wsgi [req-038bba7b-bea5-474e-a3be-f60a411fadb2 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:46:27] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.135362
2016-11-04 10:46:27.395 17166 INFO neutron.wsgi [req-617b0523-546f-4298-ab99-53933cdb1eb7 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:46:27] "GET /v2.0/ports.json?device_id=e47ef1e7-3709-4cef-bb0c-8c55acba97ea HTTP/1.1" 200 226 0.173236
2016-11-04 10:46:30.687 17167 INFO neutron.wsgi [req-d85c8060-8bd7-426a-9586-afb617b25689 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:46:30] "PUT /v2.0/routers/e47ef1e7-3709-4cef-bb0c-8c55acba97ea/add_router_interface.json HTTP/1.1" 200 523 3.280122
2016-11-04 10:46:31.154 17166 INFO neutron.wsgi [req-b1e7d240-5825-4c8d-bbff-696acb52603e b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:46:31] "GET /v2.0/ports/92465b41-0555-4d5a-91e4-e7b3653e21f3.json HTTP/1.1" 200 908 0.450501
2016-11-04 10:46:31.517 17167 INFO neutron.wsgi [req-5e87fcf0-b261-430f-9556-4397e7c9bfca b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:46:31] "GET /v2.0/routers/e47ef1e7-3709-4cef-bb0c-8c55acba97ea.json HTTP/1.1" 200 698 0.257662
2016-11-04 10:46:31.910 17166 INFO neutron.wsgi [req-26581cf3-98b2-4a53-b46c-30372bba1bce b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:46:31] "GET /v2.0/networks/d3107881-083f-4bf7-a8b5-d8f0b3a46388.json HTTP/1.1" 200 769 0.371708
2016-11-04 10:46:32.144 17167 INFO neutron.wsgi [req-6c742320-8cf0-408c-b3b0-307ebee98d57 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:46:32] "GET /v2.0/ports.json?device_id=e47ef1e7-3709-4cef-bb0c-8c55acba97ea HTTP/1.1" 200 911 0.230232
2016-11-04 10:46:32.167 17166 INFO neutron.wsgi [req-d87a6590-4ec1-4df8-af59-13d57ca2ca48 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:46:32] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.016205
```
Có thêm log tại /var/log/neutron/l3-agent.log controller
```sh
2016-11-04 10:46:38.254 2219 INFO neutron.agent.linux.interface [-] Device qg-4ed640a1-de already exists
```
thêm tại /var/log/neutron/openvswitch-agent.log controller
```sh
2016-11-04 10:46:36.624 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Port 92465b41-0555-4d5a-91e4-e7b3653e21f3 updated. Details: {u'profile': {}, u'network_qos_policy_id': None, u'qos_policy_id': None, u'allowed_address_pairs': [], u'admin_state_up': True, u'network_id': u'e9a14ace-1fa6-427c-98d2-1174ec03507c', u'segmentation_id': 379, u'device_owner': u'network:router_interface', u'physical_network': None, u'mac_address': u'fa:16:3e:a4:89:1b', u'device': u'92465b41-0555-4d5a-91e4-e7b3653e21f3', u'port_security_enabled': False, u'port_id': u'92465b41-0555-4d5a-91e4-e7b3653e21f3', u'fixed_ips': [{u'subnet_id': u'9aa3a9b4-9ee0-41ee-b778-b0e1f403b446', u'ip_address': u'192.168.2.1'}], u'network_type': u'gre', u'security_groups': []}
2016-11-04 10:46:38.366 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Skipping ARP spoofing rules for port 'qr-92465b41-05' because it has port security disabled
2016-11-04 10:46:40.632 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Configuration for devices up [u'92465b41-0555-4d5a-91e4-e7b3653e21f3'] and devices down [] completed.
```
thêm tại /var/log/neutron/dhcp-agent.log controller
```sh
2016-11-04 10:46:30.436 2218 INFO neutron.agent.dhcp.agent [req-d85c8060-8bd7-426a-9586-afb617b25689 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] Trigger reload_allocations for port admin_state_up=True, allowed_address_pairs=[], binding:host_id=, binding:profile=, binding:vif_details=, binding:vif_type=unbound, binding:vnic_type=normal, created_at=2016-11-04T03:46:28, description=, device_id=e47ef1e7-3709-4cef-bb0c-8c55acba97ea, device_owner=network:router_interface, dns_name=None, extra_dhcp_opts=[], fixed_ips=[{u'subnet_id': u'9aa3a9b4-9ee0-41ee-b778-b0e1f403b446', u'ip_address': u'192.168.2.1'}], id=92465b41-0555-4d5a-91e4-e7b3653e21f3, mac_address=fa:16:3e:a4:89:1b, name=, network_id=e9a14ace-1fa6-427c-98d2-1174ec03507c, port_security_enabled=False, security_groups=[], status=DOWN, tenant_id=bfb67b8eaead4f1684710db4608d1c48, updated_at=2016-11-04T03:46:28
```
thêm tại /var/log/openvswitch/ovs-vswitchd.log controller
```sh
2016-11-04T03:46:34.182Z|00388|bridge|INFO|bridge br-int: added interface qr-92465b41-05 on port 17
2016-11-04T03:46:35.113Z|00389|netdev_linux|INFO|ioctl(SIOCGIFHWADDR) on qr-92465b41-05 device failed: No such device
2016-11-04T03:46:35.173Z|00390|netdev_linux|WARN|ioctl(SIOCGIFINDEX) on qr-92465b41-05 device failed: No such device
2016-11-04T03:46:37.173Z|00391|netdev_linux|WARN|qr-92465b41-05: removing policing failed: No such device
2016-11-04T03:46:38.614Z|00392|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:46:38.921Z|00393|ofp_util|INFO|normalization changed ofp_match, details:
2016-11-04T03:46:38.921Z|00394|ofp_util|INFO| pre: in_port=17,nw_proto=58,tp_src=136
2016-11-04T03:46:38.921Z|00395|ofp_util|INFO|post: in_port=17
2016-11-04T03:46:38.923Z|00396|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:46:39.123Z|00397|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:46:39.376Z|00398|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:46:39.685Z|00399|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
```

- Các log khi create instance cho user tannt:
	- neutron-server
```sh
2016-11-04 10:56:24.657 17167 INFO neutron.wsgi [req-6d141691-8110-4e47-bb27-42244b4e49bf b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:24] "GET /v2.0/security-groups.json?fields=id&id=b8a9970e-4c41-4b35-a109-e45368a94a34 HTTP/1.1" 200 282 0.069635
2016-11-04 10:56:24.719 17167 INFO neutron.wsgi [req-2b030b23-2fff-44a7-916a-5ff170b1dbfe b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:24] "GET /v2.0/security-groups/b8a9970e-4c41-4b35-a109-e45368a94a34.json HTTP/1.1" 200 2184 0.051862
2016-11-04 10:56:24.991 17167 INFO neutron.wsgi [req-fcd750ea-ff35-4ed6-aaed-32a50fcb5162 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:24] "GET /v2.0/networks.json?id=e9a14ace-1fa6-427c-98d2-1174ec03507c HTTP/1.1" 200 758 0.264609
2016-11-04 10:56:25.020 17167 INFO neutron.wsgi [req-965f32a3-443a-41fc-b81d-6cf7c36d458f b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:25] "GET /v2.0/quotas/bfb67b8eaead4f1684710db4608d1c48.json HTTP/1.1" 200 385 0.021657
2016-11-04 10:56:25.096 17167 INFO neutron.wsgi [req-9f9fe82e-7eed-48fa-a88d-d21e935f2d54 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:25] "GET /v2.0/ports.json?fields=id&tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 321 0.069662
2016-11-04 10:56:26.383 17166 INFO neutron.wsgi [req-3f0127fb-de89-4630-9d1f-c46ff20ea520 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:26] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 226 0.164523
2016-11-04 10:56:27.032 17167 INFO neutron.wsgi [req-80901aaa-2e1d-4b36-b05a-80adcce99caa b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:27] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 226 0.163671
2016-11-04 10:56:27.173 17166 INFO neutron.wsgi [req-dd7ea3ac-dd47-4525-8496-4298f8e4be34 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:27] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 226 0.129120
2016-11-04 10:56:27.261 17167 INFO neutron.wsgi [req-2bb7e1a2-9f80-435c-9922-3643e8f47b08 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:27] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 232 0.078782
2016-11-04 10:56:27.434 17166 INFO neutron.wsgi [req-e8f2d79a-827b-4989-aa4f-48e1fb61d419 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:27] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 1632 0.166086
2016-11-04 10:56:27.681 17167 INFO neutron.wsgi [req-be2b62d3-afad-4f76-8b3d-e73eec5cf8b4 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:27] "GET /v2.0/networks.json HTTP/1.1" 200 1303 0.235848
2016-11-04 10:56:27.866 17166 INFO neutron.wsgi [req-adf6364d-6cfe-498b-8e5f-3cd0683b7cc9 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:27] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.180830
2016-11-04 10:56:28.381 17167 INFO neutron.wsgi [req-ccf90198-e4c1-4aed-abf1-8012c43b5ee0 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.81 - - [04/Nov/2016 10:56:28] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.026398
2016-11-04 10:56:28.806 17166 INFO neutron.wsgi [req-ed228799-96a9-4e19-8c14-78a496719547 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:28] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.043847
2016-11-04 10:56:28.894 17167 INFO neutron.wsgi [req-ca65e166-0999-476f-b6f6-298615a5c5f0 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.81 - - [04/Nov/2016 10:56:28] "GET /v2.0/networks.json?id=e9a14ace-1fa6-427c-98d2-1174ec03507c HTTP/1.1" 200 758 0.500983
2016-11-04 10:56:29.040 17167 INFO neutron.wsgi [req-e597ad8c-355c-45e2-9b8a-659077f34217 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.81 - - [04/Nov/2016 10:56:29] "GET /v2.0/security-groups.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2187 0.134713
2016-11-04 10:56:31.001 17167 INFO neutron.wsgi [req-02321621-1a7c-4db2-abac-b59c2a9e39d2 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:31] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 226 0.113434
2016-11-04 10:56:31.156 17166 INFO neutron.wsgi [req-ebacd693-c5a0-48c4-888f-25131aa26245 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:31] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 226 0.085006
2016-11-04 10:56:31.231 17167 INFO neutron.wsgi [req-b2a876fa-d5ca-4afe-8aec-490820fc6222 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:31] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 232 0.040602
2016-11-04 10:56:31.360 17166 INFO neutron.wsgi [req-8bbeeb7b-94e1-45a5-9461-ada2a4a4ad31 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:31] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2344 0.108649
2016-11-04 10:56:31.524 17166 INFO neutron.wsgi [req-d19cf55f-c813-463f-9b35-14521c02bd6b b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:31] "GET /v2.0/networks.json HTTP/1.1" 200 1303 0.155943
2016-11-04 10:56:31.637 17167 INFO neutron.wsgi [req-957407a2-830a-4eb4-9cf9-eacbe42083be 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 10:56:31] "POST /v2.0/ports.json HTTP/1.1" 201 1091 1.646361
2016-11-04 10:56:31.803 17166 INFO neutron.wsgi [req-e8aee4d5-6a44-4904-8c33-26e541dd220d b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:31] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.272018
2016-11-04 10:56:31.958 17166 INFO neutron.wsgi [req-60172c06-daf8-4841-898b-192b76c848b4 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:31] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.023503
2016-11-04 10:56:31.966 17167 INFO neutron.wsgi [req-17e07a83-1cc8-47b3-8d8f-ea243cacc30e 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 10:56:31] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48&device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 1089 0.189272
2016-11-04 10:56:32.039 17167 INFO neutron.wsgi [req-cc166c86-8e57-4a11-a066-e6f00e407a12 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 10:56:32] "GET /v2.0/floatingips.json?fixed_ip_address=192.168.2.51&port_id=6f8508dc-a880-4c69-81d0-3a9f77d3082d HTTP/1.1" 200 232 0.027602
2016-11-04 10:56:32.260 17167 INFO neutron.wsgi [req-1d7f62a6-a3b3-4de6-8fbd-9a17cefb2ca1 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.81 - - [04/Nov/2016 10:56:32] "GET /v2.0/subnets.json?id=9aa3a9b4-9ee0-41ee-b778-b0e1f403b446 HTTP/1.1" 200 784 0.204346
2016-11-04 10:56:32.730 17167 INFO neutron.wsgi [req-809c1a4e-1be7-4a18-90f5-62d2aceaf515 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.81 - - [04/Nov/2016 10:56:32] "GET /v2.0/ports.json?network_id=e9a14ace-1fa6-427c-98d2-1174ec03507c&device_owner=network%3Adhcp HTTP/1.1" 200 943 0.461209
2016-11-04 10:56:35.752 17166 INFO neutron.wsgi [req-19c5aa92-03de-4be8-96e7-7aedfb7c065e b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:35] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 937 0.092978
2016-11-04 10:56:35.861 17166 INFO neutron.wsgi [req-260fe14d-dc9b-44a1-984b-d75409a6f27d b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:35] "GET /v2.0/security-groups.json?id=b8a9970e-4c41-4b35-a109-e45368a94a34 HTTP/1.1" 200 2187 0.094056
2016-11-04 10:56:36.005 17166 INFO neutron.wsgi [req-abdfd3ed-2a8b-4ccd-8fbf-24b001d7d30a b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:36] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 937 0.065175
2016-11-04 10:56:36.053 17167 INFO neutron.wsgi [req-cf0a4f30-5f59-45ee-9aec-9e43b9fbd45f b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:36] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48&port_id=6f8508dc-a880-4c69-81d0-3a9f77d3082d HTTP/1.1" 200 232 0.038691
2016-11-04 10:56:36.124 17166 INFO neutron.wsgi [req-93495bf6-6e77-4b0c-97df-ea36e95d33be b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:36] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2344 0.065154
2016-11-04 10:56:36.243 17166 INFO neutron.wsgi [req-9a60ff98-b74c-4401-aa0c-9a3eb65ef8e1 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:36] "GET /v2.0/networks.json?id=e9a14ace-1fa6-427c-98d2-1174ec03507c HTTP/1.1" 200 758 0.112606
2016-11-04 10:56:36.394 17167 INFO neutron.wsgi [req-1bcac796-bb9c-4bfc-8eaf-536aa8f46475 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:36] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.149414
2016-11-04 10:56:36.487 17167 INFO neutron.wsgi [req-efa012c2-c562-4832-9f69-9b72f4925cd0 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:36] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.005654
2016-11-04 10:56:44.108 17167 INFO neutron.wsgi [req-2a7f4ed4-bc58-4b12-93ac-6f5b2ee6510c b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:44] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 938 0.068198
2016-11-04 10:56:44.205 17167 INFO neutron.wsgi [req-71d8c160-7580-4ad2-af18-4ead5776174a b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:44] "GET /v2.0/security-groups.json?id=b8a9970e-4c41-4b35-a109-e45368a94a34 HTTP/1.1" 200 2187 0.086472
2016-11-04 10:56:44.519 17166 INFO neutron.wsgi [req-224f8915-16e3-400b-ac35-2fdb965adf67 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:44] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 938 0.230215
2016-11-04 10:56:44.559 17167 INFO neutron.wsgi [req-4636761b-0400-4a4a-a5b9-146478fb79d0 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:44] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48&port_id=6f8508dc-a880-4c69-81d0-3a9f77d3082d HTTP/1.1" 200 232 0.034862
2016-11-04 10:56:44.619 17166 INFO neutron.wsgi [req-52bff830-5387-4480-9099-1d5a372cb399 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:44] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2345 0.051364
2016-11-04 10:56:44.771 17167 INFO neutron.wsgi [req-39839a93-a87b-4b17-b775-547fe1e0837d b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:44] "GET /v2.0/networks.json?id=e9a14ace-1fa6-427c-98d2-1174ec03507c HTTP/1.1" 200 758 0.146935
2016-11-04 10:56:44.976 17166 INFO neutron.wsgi [req-376e960c-f5d9-4663-a884-b0502c634414 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:44] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.171004
2016-11-04 10:56:45.101 17167 INFO neutron.wsgi [req-4191f494-543b-4547-a920-7f1a3f17d6ac b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:45] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.012845
2016-11-04 10:56:52.503 17168 INFO neutron.notifiers.nova [-] Nova event response: {u'status': u'completed', u'tag': u'6f8508dc-a880-4c69-81d0-3a9f77d3082d', u'name': u'network-vif-plugged', u'server_uuid': u'35608eb1-bb21-4a02-9c75-01f16be58100', u'code': 200}
2016-11-04 10:56:53.809 17167 INFO neutron.wsgi [req-45158338-13ca-408f-968d-bf5fdbfbf3b7 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:53] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 939 0.325658
2016-11-04 10:56:53.938 17167 INFO neutron.wsgi [req-585024ad-3b42-43b0-9da7-b87d28d54e02 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:53] "GET /v2.0/security-groups.json?id=b8a9970e-4c41-4b35-a109-e45368a94a34 HTTP/1.1" 200 2187 0.119029
2016-11-04 10:56:54.117 17166 INFO neutron.wsgi [req-4ebe6ec6-9018-461c-adf8-31130d26ca42 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:54] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 939 0.084715
2016-11-04 10:56:54.173 17167 INFO neutron.wsgi [req-056b1df5-30f0-4f8b-9a7a-a84032df84b1 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:54] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48&port_id=6f8508dc-a880-4c69-81d0-3a9f77d3082d HTTP/1.1" 200 232 0.044694
2016-11-04 10:56:54.278 17166 INFO neutron.wsgi [req-78553499-ec81-4af8-9ca0-d890123a07ee b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:54] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2346 0.096862
2016-11-04 10:56:54.443 17167 INFO neutron.wsgi [req-05d7acbb-e5bf-4830-b407-ec84193f9501 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:54] "GET /v2.0/networks.json?id=e9a14ace-1fa6-427c-98d2-1174ec03507c HTTP/1.1" 200 758 0.140187
2016-11-04 10:56:54.556 17167 INFO neutron.wsgi [req-6bcd0139-bc01-4c71-bb8d-cb58b4cd189b b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:56:54] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.105862
2016-11-04 10:57:03.223 17166 INFO neutron.wsgi [req-844bde2b-6e22-4659-9cee-7fb3280ee6ce b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:57:03] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.008387
2016-11-04 10:57:09.666 17166 INFO neutron.wsgi [req-43302a08-61a0-43ac-9416-fcf3d63b9ab1 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.80 - - [04/Nov/2016 10:57:09] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 1091 0.923050
2016-11-04 10:57:09.806 17166 INFO neutron.wsgi [req-c8505e2c-a506-4ec8-8cb9-85eb2b4c41e5 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.80 - - [04/Nov/2016 10:57:09] "GET /v2.0/security-groups.json?id=b8a9970e-4c41-4b35-a109-e45368a94a34 HTTP/1.1" 200 2187 0.103110
2016-11-04 10:57:12.355 17167 INFO neutron.wsgi [req-b0b6527a-14d8-4fbc-9b8c-9c85522eccb9 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.80 - - [04/Nov/2016 10:57:12] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 1091 1.013557
2016-11-04 10:57:12.506 17167 INFO neutron.wsgi [req-3730dc8b-883d-4e13-9dea-8ee1ec484fa6 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.80 - - [04/Nov/2016 10:57:12] "GET /v2.0/security-groups.json?id=b8a9970e-4c41-4b35-a109-e45368a94a34 HTTP/1.1" 200 2187 0.140629
```
	- /var/log/neutron/dhcp-agent.log controller
```sh
2016-11-04 10:56:31.612 2218 INFO neutron.agent.dhcp.agent [req-957407a2-830a-4eb4-9cf9-eacbe42083be 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] Trigger reload_allocations for port admin_state_up=True, allowed_address_pairs=[], binding:host_id=compute1, binding:profile=, binding:vif_details=ovs_hybrid_plug=True, port_filter=True, binding:vif_type=ovs, binding:vnic_type=normal, created_at=2016-11-04T03:56:30, description=, device_id=35608eb1-bb21-4a02-9c75-01f16be58100, device_owner=compute:nova, dns_name=None, extra_dhcp_opts=[], fixed_ips=[{u'subnet_id': u'9aa3a9b4-9ee0-41ee-b778-b0e1f403b446', u'ip_address': u'192.168.2.51'}], id=6f8508dc-a880-4c69-81d0-3a9f77d3082d, mac_address=fa:16:3e:79:db:d3, name=, network_id=e9a14ace-1fa6-427c-98d2-1174ec03507c, port_security_enabled=True, security_groups=[u'b8a9970e-4c41-4b35-a109-e45368a94a34'], status=DOWN, tenant_id=bfb67b8eaead4f1684710db4608d1c48, updated_at=2016-11-04T03:56:31
```
	- /var/log/neutron/neutron-metadata-agent.log controller
```sh
2016-11-04 10:57:09.863 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:09] "GET /2009-04-04/meta-data/instance-id HTTP/1.1" 200 127 3.029244
2016-11-04 10:57:09.959 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:09] "GET /2009-04-04/meta-data/public-keys HTTP/1.1" 404 176 0.045171
2016-11-04 10:57:12.536 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:12] "GET /2009-04-04/meta-data/instance-id HTTP/1.1" 200 127 2.525567
2016-11-04 10:57:12.753 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:12] "GET /2009-04-04/meta-data/ami-launch-index HTTP/1.1" 200 117 0.166626
2016-11-04 10:57:12.781 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:12] "GET /2009-04-04/meta-data/instance-type HTTP/1.1" 200 123 0.007676
2016-11-04 10:57:12.810 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:12] "GET /2009-04-04/meta-data/local-ipv4 HTTP/1.1" 200 129 0.007228
2016-11-04 10:57:12.846 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:12] "GET /2009-04-04/meta-data/public-ipv4 HTTP/1.1" 200 116 0.006554
2016-11-04 10:57:12.875 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:12] "GET /2009-04-04/meta-data/hostname HTTP/1.1" 200 132 0.008162
2016-11-04 10:57:12.918 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:12] "GET /2009-04-04/meta-data/local-hostname HTTP/1.1" 200 132 0.017128
2016-11-04 10:57:12.966 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:12] "GET /2009-04-04/user-data HTTP/1.1" 404 176 0.007689
2016-11-04 10:57:13.000 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:13] "GET /2009-04-04/meta-data/block-device-mapping HTTP/1.1" 200 124 0.006561
2016-11-04 10:57:13.046 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:13] "GET /2009-04-04/meta-data/block-device-mapping/ami HTTP/1.1" 200 119 0.027849
2016-11-04 10:57:13.071 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:13] "GET /2009-04-04/meta-data/block-device-mapping/root HTTP/1.1" 200 124 0.008173
2016-11-04 10:57:13.098 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:13] "GET /2009-04-04/meta-data/public-hostname HTTP/1.1" 200 132 0.006084
2016-11-04 10:57:13.129 2124 INFO eventlet.wsgi.server [-] 192.168.2.51,<local> - - [04/Nov/2016 10:57:13] "GET /2009-04-04/meta-data/placement/availability-zone HTTP/1.1" 200 120 0.007582
```
	- /var/log/neutron/openvswitch-agent.log controller
```sh
2016-11-04 10:56:31.261 2214 INFO neutron.agent.securitygroups_rpc [req-957407a2-830a-4eb4-9cf9-eacbe42083be 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] Security group member updated [u'b8a9970e-4c41-4b35-a109-e45368a94a34']
```
	- /var/log/openvswitch/ovs-vswitchd.log controller
```sh
2016-11-04T03:56:48.381Z|00400|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-04T03:56:48.570Z|00401|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 modifications)
```
	- /var/log/neutron/openvswitch-agent.log compute
```sh
2016-11-04 10:56:31.302 1900 INFO neutron.agent.securitygroups_rpc [req-957407a2-830a-4eb4-9cf9-eacbe42083be 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] Security group member updated [u'b8a9970e-4c41-4b35-a109-e45368a94a34']
2016-11-04 10:56:39.972 1900 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Port 6f8508dc-a880-4c69-81d0-3a9f77d3082d updated. Details: {u'profile': {}, u'network_qos_policy_id': None, u'qos_policy_id': None, u'allowed_address_pairs': [], u'admin_state_up': True, u'network_id': u'e9a14ace-1fa6-427c-98d2-1174ec03507c', u'segmentation_id': 379, u'device_owner': u'compute:nova', u'physical_network': None, u'mac_address': u'fa:16:3e:79:db:d3', u'device': u'6f8508dc-a880-4c69-81d0-3a9f77d3082d', u'port_security_enabled': True, u'port_id': u'6f8508dc-a880-4c69-81d0-3a9f77d3082d', u'fixed_ips': [{u'subnet_id': u'9aa3a9b4-9ee0-41ee-b778-b0e1f403b446', u'ip_address': u'192.168.2.51'}], u'network_type': u'gre', u'security_groups': [u'b8a9970e-4c41-4b35-a109-e45368a94a34']}
2016-11-04 10:56:39.975 1900 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Assigning 4 as local vlan for net-id=e9a14ace-1fa6-427c-98d2-1174ec03507c
2016-11-04 10:56:41.690 1900 INFO neutron.agent.securitygroups_rpc [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Preparing filters for devices set([u'6f8508dc-a880-4c69-81d0-3a9f77d3082d'])
2016-11-04 10:56:48.241 1900 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Configuration for devices up [u'6f8508dc-a880-4c69-81d0-3a9f77d3082d'] and devices down [] completed.
```
	- /var/log/openvswitch/ovs-vswitchd.log compute
```sh
2016-11-04T03:56:37.390Z|00096|bridge|INFO|bridge br-int: added interface qvo6f8508dc-a8 on port 6
2016-11-04T03:56:40.202Z|00097|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 modifications)
2016-11-04T03:56:40.466Z|00098|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-04T03:56:45.634Z|00099|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:56:45.823Z|00100|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-04T03:56:46.368Z|00101|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-04T03:56:46.621Z|00102|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-04T03:56:46.784Z|00103|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-04T03:56:47.001Z|00104|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-04T03:56:47.230Z|00105|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 adds)
2016-11-04T03:56:48.361Z|00106|connmgr|INFO|br-tun<->unix: 2 flow_mods in the last 0 s (2 adds)
2016-11-04T03:56:48.551Z|00107|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 modifications)
```

- Log khi thêm floating IP cho instance vừa tạo:
	- Neutron-server
		- click associate IP
```sh
2016-11-04 11:03:07.053 17167 INFO neutron.wsgi [req-46cc3c40-3858-47c4-8c8c-6fa42c16ad49 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:03:07] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 1.244240
2016-11-04 11:03:07.124 17166 INFO neutron.wsgi [req-c78351ef-3633-4988-90c7-4e139bf33d28 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:03:07] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 232 0.057293
2016-11-04 11:03:07.282 17167 INFO neutron.wsgi [req-44c35184-79e3-473b-97a8-8333f12445d5 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:03:07] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2346 0.142701
2016-11-04 11:03:07.376 17166 INFO neutron.wsgi [req-540faf0e-8112-470f-a207-047addbc4c3b b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:03:07] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2346 0.082107
2016-11-04 11:03:07.711 17166 INFO neutron.wsgi [req-52d521e0-036e-4839-aeed-b4a206116bb1 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:03:07] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 939 0.121229
2016-11-04 11:03:07.885 17166 INFO neutron.wsgi [req-c6106ae1-295a-4563-9b46-954003f65f8f b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:03:07] "GET /v2.0/security-groups.json?id=b8a9970e-4c41-4b35-a109-e45368a94a34 HTTP/1.1" 200 2187 0.159413
2016-11-04 11:03:08.079 17167 INFO neutron.wsgi [req-a468342c-af03-44b6-ae81-2bed52c99006 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:03:08] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 772 0.159720
2016-11-04 11:03:08.280 17166 INFO neutron.wsgi [req-7288dc9b-ebc3-496f-959d-9f1f9af5a19f b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:03:08] "GET /v2.0/routers.json HTTP/1.1" 200 701 0.180383
2016-11-04 11:03:08.535 17167 INFO neutron.wsgi [req-2834e16b-6ba6-4760-83e1-cbbdbcafad34 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:03:08] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 772 0.240129
2016-11-04 11:03:08.743 17166 INFO neutron.wsgi [req-4b2d2218-25f6-447c-98ef-d13ced0a6168 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:03:08] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.182288
```
		- click button +
```sh
2016-11-04 11:04:52.001 17167 INFO neutron.wsgi [req-3c38567c-7fcb-4a06-97cf-8fe00f96f5e1 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:52] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.034749
2016-11-04 11:04:52.222 17166 INFO neutron.wsgi [req-b541ba91-36ca-46b9-b03f-bd59dbbffdab b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:52] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 772 0.206981
2016-11-04 11:04:52.359 17167 INFO neutron.wsgi [req-72b521fc-f4fb-4d4e-9e5a-ee148ba9ab81 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:52] "GET /v2.0/quotas/bfb67b8eaead4f1684710db4608d1c48.json HTTP/1.1" 200 385 0.023521
2016-11-04 11:04:52.790 17166 INFO neutron.wsgi [req-1e12ccc0-27eb-4618-8ab0-8e5d161295fe b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:52] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 939 0.102926
2016-11-04 11:04:52.999 17166 INFO neutron.wsgi [req-16caa7f9-7f97-4af3-86b1-b3844c416d40 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:52] "GET /v2.0/security-groups.json?id=b8a9970e-4c41-4b35-a109-e45368a94a34 HTTP/1.1" 200 2187 0.206359
2016-11-04 11:04:53.189 17166 INFO neutron.wsgi [req-6ba0f49e-3fc9-41f1-8c1f-e384aae02c10 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:53] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 232 0.054343
2016-11-04 11:04:53.338 17167 INFO neutron.wsgi [req-4513bf56-203d-477a-87ba-b9ef2def2aa0 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:53] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2346 0.134042
2016-11-04 11:04:53.497 17166 INFO neutron.wsgi [req-4b4268b0-5dac-4208-bb8c-23f4ec9cdf8f b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:53] "GET /v2.0/security-groups.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2187 0.140984
2016-11-04 11:04:53.923 17167 INFO neutron.wsgi [req-dcdbf559-3b4a-47e8-9d6c-ad765fb5a0f0 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:53] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 758 0.411465
2016-11-04 11:04:54.112 17166 INFO neutron.wsgi [req-3dbf8854-fead-4925-aab5-cfbb468595ea b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:54] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.165274
2016-11-04 11:04:54.328 17167 INFO neutron.wsgi [req-98c219f9-a248-4027-9e44-cc975c30b349 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:54] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 772 0.199107
2016-11-04 11:04:54.506 17166 INFO neutron.wsgi [req-89281c50-0433-41f7-95d4-e1f43f692003 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:54] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.165807
2016-11-04 11:04:54.709 17167 INFO neutron.wsgi [req-56faaa7f-f83c-4e89-94c5-93e9157409c0 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:54] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.188963
2016-11-04 11:04:54.892 17167 INFO neutron.wsgi [req-a8ad8d0b-e62a-4308-9c83-6ac4e5b2c76d b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:04:54] "GET /v2.0/routers.json HTTP/1.1" 200 701 0.171498
```
		- chọn provider và allocate IP
```sh
2016-11-04 11:05:39.963 17166 INFO neutron.wsgi [req-cec7cfb7-6307-492b-926f-a6a710fbee63 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:39] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.012190
2016-11-04 11:05:40.191 17167 INFO neutron.wsgi [req-884bc392-3673-40e4-8d1b-4532979eef36 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:40] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 772 0.210541
2016-11-04 11:05:40.334 17166 INFO neutron.wsgi [req-7a467bd6-9290-416f-9438-30799ca0fb4d b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:40] "GET /v2.0/quotas/bfb67b8eaead4f1684710db4608d1c48.json HTTP/1.1" 200 385 0.030674
2016-11-04 11:05:40.827 17166 INFO neutron.wsgi [req-c8347b5f-0916-41fa-8a9c-8e18a75bc8d7 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:40] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 939 0.195248
2016-11-04 11:05:40.952 17166 INFO neutron.wsgi [req-931e24df-e697-47b7-ad00-25113490c525 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:40] "GET /v2.0/security-groups.json?id=b8a9970e-4c41-4b35-a109-e45368a94a34 HTTP/1.1" 200 2187 0.122948
2016-11-04 11:05:41.065 17167 INFO neutron.wsgi [req-4aadda0c-f50e-4f06-b261-188c03259594 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:41] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 232 0.032503
2016-11-04 11:05:41.161 17166 INFO neutron.wsgi [req-12fa4e86-0981-459b-bed6-529decd568a5 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:41] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2346 0.094415
2016-11-04 11:05:41.320 17167 INFO neutron.wsgi [req-30fd4e9f-eeb5-4bb7-8248-79bf22aecc79 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:41] "GET /v2.0/security-groups.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2187 0.138011
2016-11-04 11:05:41.580 17166 INFO neutron.wsgi [req-3adddac2-7e55-4b11-865b-0eba74e45a9e b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:41] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 758 0.241712
2016-11-04 11:05:41.828 17167 INFO neutron.wsgi [req-0826b31e-5eec-4438-9e6b-ac874ca68e96 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:41] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.220238
2016-11-04 11:05:42.519 17166 INFO neutron.wsgi [req-49f8b6bf-3240-48c3-9779-238e9a16593f b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:42] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 772 0.659806
2016-11-04 11:05:42.773 17167 INFO neutron.wsgi [req-3e8a79b6-2701-4422-9962-fefa8d85582a b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:42] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.213484
2016-11-04 11:05:42.929 17166 INFO neutron.wsgi [req-727b7fc9-6995-4d79-b69a-a9179a39245e b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:42] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.151919
2016-11-04 11:05:43.045 17167 INFO neutron.wsgi [req-f5505011-7efc-4e1d-af5f-cedccab34c36 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:43] "GET /v2.0/routers.json HTTP/1.1" 200 701 0.124425
2016-11-04 11:05:44.284 17166 INFO neutron.wsgi [req-1173a05a-c4ca-4518-a20b-8922e6831daf b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:05:44] "POST /v2.0/floatingips.json HTTP/1.1" 201 566 1.219184
```
		- Sau khi có IP, chọn Associate:
```sh
2016-11-04 11:06:36.059 17166 INFO neutron.wsgi [req-68b3c644-add0-4f1a-84cc-6c4145056787 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:36] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.034098
2016-11-04 11:06:36.148 17167 INFO neutron.wsgi [req-8aaf864d-6577-448c-bc59-724351cfd2fa b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:36] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 564 0.069061
2016-11-04 11:06:36.395 17166 INFO neutron.wsgi [req-4f8ff3d1-bd20-44c9-aa64-93f778582fe3 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:36] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2346 0.214720
2016-11-04 11:06:36.816 17167 INFO neutron.wsgi [req-2083187d-c47a-4fc4-ae16-9b76649bdd21 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:36] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2346 0.392505
2016-11-04 11:06:37.146 17166 INFO neutron.wsgi [req-8cb7d837-864b-40fd-b453-ff34974de561 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:37] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 939 0.083892
2016-11-04 11:06:37.338 17166 INFO neutron.wsgi [req-0a318bca-54f8-468f-b9d2-b82a557d14b6 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:37] "GET /v2.0/security-groups.json?id=b8a9970e-4c41-4b35-a109-e45368a94a34 HTTP/1.1" 200 2187 0.171473
2016-11-04 11:06:37.657 17166 INFO neutron.wsgi [req-03dd659b-31c4-4942-a47d-926eeed7dccf b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:37] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 772 0.264890
2016-11-04 11:06:38.134 17167 INFO neutron.wsgi [req-171d3837-978f-4b22-8f41-5a17ba7f5850 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:38] "GET /v2.0/routers.json HTTP/1.1" 200 701 0.453103
2016-11-04 11:06:38.450 17166 INFO neutron.wsgi [req-65194fbd-1b54-4789-b937-7d8e8e1aed8f b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:38] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 772 0.309637
2016-11-04 11:06:38.885 17167 INFO neutron.wsgi [req-2d29d38c-f119-46cb-8990-211c44f377f2 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:38] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.419013
2016-11-04 11:06:40.918 17166 INFO neutron.wsgi [req-020cbf91-66a6-4fd4-916b-37fc3906cd1a b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:40] "PUT /v2.0/floatingips/a4fe5109-20d8-46b4-8d9d-f6e036fa82cc.json HTTP/1.1" 200 639 2.019817
2016-11-04 11:06:41.904 17167 INFO neutron.wsgi [req-0ebfdea9-47c2-4b8d-bbec-28e4fdb8e6c6 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:41] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 939 0.307505
2016-11-04 11:06:42.605 17167 INFO neutron.wsgi [req-557a553d-5bb7-4931-aa13-c7d060311da8 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:42] "GET /v2.0/security-groups.json?id=b8a9970e-4c41-4b35-a109-e45368a94a34 HTTP/1.1" 200 2187 0.389160
2016-11-04 11:06:42.964 17166 INFO neutron.wsgi [req-b5077060-1332-4fc5-a673-26178d0993ad b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:42] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 939 0.189300
2016-11-04 11:06:43.493 17167 INFO neutron.wsgi [req-b01cae2a-b033-4c70-8771-d0d0a9e8c21e b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:43] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48&port_id=6f8508dc-a880-4c69-81d0-3a9f77d3082d HTTP/1.1" 200 642 0.498149
2016-11-04 11:06:44.138 17166 INFO neutron.wsgi [req-583026a7-d98a-4862-bcd1-c888606c3356 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:44] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2346 0.592069
2016-11-04 11:06:45.143 17167 INFO neutron.wsgi [req-bf148996-5abf-468b-9255-d99b366d1650 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:45] "GET /v2.0/networks.json?id=e9a14ace-1fa6-427c-98d2-1174ec03507c HTTP/1.1" 200 758 0.905099
2016-11-04 11:06:46.247 17166 INFO neutron.wsgi [req-d703bd42-981f-4fbe-b217-988dc88d1443 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:46] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 1.058615
2016-11-04 11:06:46.833 17166 INFO neutron.wsgi [req-b2138ff2-a16f-4318-a399-be7ef58fb20b b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:46] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.065932
2016-11-04 11:06:47.100 17166 INFO neutron.notifiers.nova [-] Nova event response: {u'status': u'completed', u'code': 200, u'name': u'network-changed', u'server_uuid': u'35608eb1-bb21-4a02-9c75-01f16be58100'}
2016-11-04 11:06:47.337 17167 INFO neutron.wsgi [req-261dd43b-b7c8-404b-bec4-a21da277e5cc 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 11:06:47] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48&device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 1091 0.169771
2016-11-04 11:06:47.571 17167 INFO neutron.wsgi [req-75cef106-4519-46be-9802-8f28baca30ee 51358268cc894066a5876114769ac86e 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 11:06:47] "GET /v2.0/networks.json?id=e9a14ace-1fa6-427c-98d2-1174ec03507c HTTP/1.1" 200 858 0.181079
2016-11-04 11:06:47.697 17167 INFO neutron.wsgi [req-f8b2e109-d385-431e-8777-15814298adc8 3dc74eecdd0b42f0a6c3e37f95d1075b 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 11:06:47] "GET /v2.0/floatingips.json?fixed_ip_address=192.168.2.51&port_id=6f8508dc-a880-4c69-81d0-3a9f77d3082d HTTP/1.1" 200 642 0.058667
2016-11-04 11:06:48.318 17167 INFO neutron.wsgi [req-8a26c40b-a586-4a86-a6ce-cd9d9bd4b03e 51358268cc894066a5876114769ac86e 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 11:06:48] "GET /v2.0/subnets.json?id=9aa3a9b4-9ee0-41ee-b778-b0e1f403b446 HTTP/1.1" 200 784 0.568003
2016-11-04 11:06:48.878 17167 INFO neutron.wsgi [req-f7826a52-4087-4c28-897a-82da5ed6d7b7 51358268cc894066a5876114769ac86e 4a62dba1952f4d18a8b635aeaed655b3 - - -] 10.10.10.81 - - [04/Nov/2016 11:06:48] "GET /v2.0/ports.json?network_id=e9a14ace-1fa6-427c-98d2-1174ec03507c&device_owner=network%3Adhcp HTTP/1.1" 200 1098 0.516191
2016-11-04 11:06:49.860 17166 INFO neutron.wsgi [req-ba5365c4-ba46-4825-95bb-386b20e48a1f b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:49] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 939 0.159906
2016-11-04 11:06:50.053 17166 INFO neutron.wsgi [req-d27e51d5-f0bf-4837-a7e3-7a7e2eee7c23 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:50] "GET /v2.0/security-groups.json?id=b8a9970e-4c41-4b35-a109-e45368a94a34 HTTP/1.1" 200 2187 0.179178
2016-11-04 11:06:50.219 17167 INFO neutron.wsgi [req-a3feb419-4087-494e-a932-2e2450a5a4ea b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:50] "GET /v2.0/ports.json?device_id=35608eb1-bb21-4a02-9c75-01f16be58100 HTTP/1.1" 200 939 0.131007
2016-11-04 11:06:50.406 17166 INFO neutron.wsgi [req-aecb2f6a-e2b6-4703-93e9-fad1080d0423 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:50] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48&port_id=6f8508dc-a880-4c69-81d0-3a9f77d3082d HTTP/1.1" 200 642 0.149775
2016-11-04 11:06:50.582 17167 INFO neutron.wsgi [req-4c33c7cd-4190-4449-a693-25b5140311a2 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:50] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 2346 0.148202
2016-11-04 11:06:50.943 17166 INFO neutron.wsgi [req-77f7a566-6f7d-429e-80a5-30e2f13d86bb b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:50] "GET /v2.0/networks.json?id=e9a14ace-1fa6-427c-98d2-1174ec03507c HTTP/1.1" 200 758 0.316560
2016-11-04 11:06:51.202 17167 INFO neutron.wsgi [req-4f21fbe6-d44e-4e13-863b-af7d7233ebb5 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 11:06:51] "GET /v2.0/subnets.json HTTP/1.1" 200 1344 0.245128
```
===> Xong: tạo network, subnet, router, instance, floating IP đối với một tenant network của user tannt

- Xóa một tenant network đã có
Log tại neutron:
```sh
2016-11-04 10:27:37.869 17167 INFO neutron.wsgi [req-d456138c-bf4a-4dae-9c1e-242afce14874 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:37] "GET /v2.0/networks.json?shared=False&tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 752 0.153553
2016-11-04 10:27:38.060 17166 INFO neutron.wsgi [req-444308b4-fdd2-4295-9028-0f93647a3b2d b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:38] "GET /v2.0/subnets.json HTTP/1.1" 200 1337 0.175520
2016-11-04 10:27:38.319 17167 INFO neutron.wsgi [req-c698750a-cf63-4110-83bf-c0d341a458c5 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:38] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 772 0.253085
2016-11-04 10:27:38.513 17166 INFO neutron.wsgi [req-a472360e-f1c6-4297-9f7a-75eb949b5457 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:38] "GET /v2.0/subnets.json HTTP/1.1" 200 1337 0.158070
2016-11-04 10:27:38.733 17167 INFO neutron.wsgi [req-ae7255b2-2b89-40a0-acb7-dfcde139f71a b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:38] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 772 0.210105
2016-11-04 10:27:38.913 17166 INFO neutron.wsgi [req-d6b9dbfd-718a-4b0d-b8c3-6520d5e0ff8e b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:38] "GET /v2.0/subnets.json HTTP/1.1" 200 1337 0.170232
2016-11-04 10:27:39.099 17167 INFO neutron.wsgi [req-b924ab2b-8ed7-4374-bc53-e5740e5ed19d b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:39] "GET /v2.0/networks/02ccb907-2980-49e8-a45d-857973263a98.json HTTP/1.1" 200 749 0.171592
2016-11-04 10:27:41.349 17166 INFO neutron.wsgi [req-71a137af-e51d-4488-bf30-38ce21a5a869 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:41] "DELETE /v2.0/subnets/02a27717-a902-4830-9e09-eeefd6515bfd.json HTTP/1.1" 204 173 2.233861
2016-11-04 10:27:43.075 17167 INFO neutron.wsgi [req-1313b06d-4524-4c1a-9267-665463ad6df3 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:43] "DELETE /v2.0/networks/02ccb907-2980-49e8-a45d-857973263a98.json HTTP/1.1" 204 173 1.744097
2016-11-04 10:27:43.298 17167 INFO neutron.wsgi [req-2b2e49f0-255a-4ffa-8700-b6253f8af9c6 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:43] "GET /v2.0/networks.json?shared=False&tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 229 0.151712
2016-11-04 10:27:43.473 17166 INFO neutron.wsgi [req-37ee86c4-c218-4eb0-a885-c770bf4c63ce b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:43] "GET /v2.0/subnets.json HTTP/1.1" 200 786 0.158021
2016-11-04 10:27:43.601 17167 INFO neutron.wsgi [req-d8ae1863-2f6c-4e17-ba4a-2598e389bb2b b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:43] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 772 0.126595
2016-11-04 10:27:43.730 17166 INFO neutron.wsgi [req-a717686b-f1e7-4712-b90f-a4878a50b490 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:43] "GET /v2.0/subnets.json HTTP/1.1" 200 786 0.134496
2016-11-04 10:27:43.835 17167 INFO neutron.wsgi [req-ead343c5-c18c-43fc-8479-189f6bdfccbc b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:43] "GET /v2.0/networks.json?router%3Aexternal=True HTTP/1.1" 200 772 0.114313
2016-11-04 10:27:43.923 17166 INFO neutron.wsgi [req-0baf5274-4f82-484a-b5ee-338aa1b7501c b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:43] "GET /v2.0/subnets.json HTTP/1.1" 200 786 0.096411
2016-11-04 10:27:44.093 17167 INFO neutron.wsgi [req-00e552ab-bc8e-4244-8888-a38795fb7d9b b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:44] "GET /v2.0/extensions.json HTTP/1.1" 200 6007 0.011362
2016-11-04 10:27:44.322 17166 INFO neutron.wsgi [req-a0583c65-b2b1-475c-a5df-0159b560ab3f b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:44] "GET /v2.0/quotas/bfb67b8eaead4f1684710db4608d1c48.json HTTP/1.1" 200 385 0.025068
2016-11-04 10:27:45.810 17167 INFO neutron.wsgi [req-e6fb0311-80a1-4b00-a275-88aebbae77e7 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:45] "GET /v2.0/floatingips.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 232 0.062468
2016-11-04 10:27:45.920 17166 INFO neutron.wsgi [req-a124137e-f79d-452f-b879-823e62fbbdbd b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:45] "GET /v2.0/ports.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 226 0.106608
2016-11-04 10:27:46.022 17166 INFO neutron.wsgi [req-61a86320-1051-4510-8137-a1da988b2462 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:46] "GET /v2.0/security-groups.json?tenant_id=bfb67b8eaead4f1684710db4608d1c48 HTTP/1.1" 200 1841 0.098214
2016-11-04 10:27:46.282 17167 INFO neutron.wsgi [req-e456f8f9-4618-4e56-8d48-f7ff74acc831 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:46] "GET /v2.0/networks.json?shared=False HTTP/1.1" 200 229 0.259603
2016-11-04 10:27:46.505 17166 INFO neutron.wsgi [req-5b623720-25ec-4740-b05b-f094f224d8b0 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:46] "GET /v2.0/subnets.json HTTP/1.1" 200 786 0.209470
2016-11-04 10:27:46.643 17167 INFO neutron.wsgi [req-f526ed3d-12ee-4493-b40d-e1a28469c4cf b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:46] "GET /v2.0/networks.json?shared=True HTTP/1.1" 200 772 0.144331
2016-11-04 10:27:46.840 17166 INFO neutron.wsgi [req-7fbe1e60-d67c-43d3-814c-12e9d0b35145 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:46] "GET /v2.0/subnets.json HTTP/1.1" 200 786 0.159869
2016-11-04 10:27:47.073 17167 INFO neutron.wsgi [req-615445a4-bd64-40d0-a391-8c5eab4f791e b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:47] "GET /v2.0/subnets.json HTTP/1.1" 200 786 0.223172
2016-11-04 10:27:47.239 17166 INFO neutron.wsgi [req-ea24253f-858f-41f3-8c47-211680368d35 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] 10.10.10.80 - - [04/Nov/2016 10:27:47] "GET /v2.0/routers.json HTTP/1.1" 200 228 0.148854
```

Log tại /var/log/neutron/openvswitch-agent.log tren controller
```sh
2016-11-04 10:27:40.852 2214 INFO neutron.agent.securitygroups_rpc [req-71a137af-e51d-4488-bf30-38ce21a5a869 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] Provider rule updated
2016-11-04 10:27:42.558 2214 INFO neutron.agent.securitygroups_rpc [req-1313b06d-4524-4c1a-9267-665463ad6df3 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] Provider rule updated
2016-11-04 10:27:43.292 2214 INFO neutron.agent.common.ovs_lib [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Port 2ba12505-9b3c-4d88-b463-5676344a32ec not present in bridge br-int
2016-11-04 10:27:43.294 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Reclaiming vlan = 4 from net-id = 02ccb907-2980-49e8-a45d-857973263a98
2016-11-04 10:27:43.887 2214 INFO neutron.agent.securitygroups_rpc [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Remove device filter for [u'2ba12505-9b3c-4d88-b463-5676344a32ec']
2016-11-04 10:27:46.761 2214 INFO neutron.agent.securitygroups_rpc [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Refresh firewall rules
2016-11-04 10:27:48.083 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Configuration for devices up [] and devices down [] completed.
2016-11-04 10:27:50.169 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Configuration for devices up [] and devices down [] completed.
2016-11-04 10:27:50.172 2214 INFO neutron.agent.securitygroups_rpc [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Remove device filter for set([u'2ba12505-9b3c-4d88-b463-5676344a32ec'])
2016-11-04 10:27:50.548 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] Ports set([u'2ba12505-9b3c-4d88-b463-5676344a32ec']) removed
2016-11-04 10:27:50.580 2214 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-8d7a733f-a6b1-494d-9597-929855ef51a7 - - - - -] port_unbound(): net_uuid None not in local_vlan_map
```

Log tại /var/log/openvswitch/ovs-vswitchd.log trên controller
```sh
2016-11-04T03:27:43.447Z|00349|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:27:43.613Z|00350|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:27:43.743Z|00351|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:27:43.872Z|00352|connmgr|INFO|br-tun<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:27:44.949Z|00353|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:27:45.420Z|00354|ofp_util|INFO|normalization changed ofp_match, details:
2016-11-04T03:27:45.420Z|00355|ofp_util|INFO| pre: in_port=14,nw_proto=58,tp_src=136
2016-11-04T03:27:45.420Z|00356|ofp_util|INFO|post: in_port=14
2016-11-04T03:27:45.424Z|00357|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:27:45.816Z|00358|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:27:46.047Z|00359|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
2016-11-04T03:27:46.318Z|00360|connmgr|INFO|br-int<->unix: 1 flow_mods in the last 0 s (1 deletes)
```

Log tại /var/log/neutron/openvswitch-agent.log trên compute
```sh
2016-11-04 10:27:40.841 1900 INFO neutron.agent.securitygroups_rpc [req-71a137af-e51d-4488-bf30-38ce21a5a869 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] Provider rule updated
2016-11-04 10:27:42.556 1900 INFO neutron.agent.securitygroups_rpc [req-1313b06d-4524-4c1a-9267-665463ad6df3 b56c4988c2e9491b8c9364e3b0d00b5a bfb67b8eaead4f1684710db4608d1c48 - - -] Provider rule updated
2016-11-04 10:27:42.798 1900 INFO neutron.agent.securitygroups_rpc [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Refresh firewall rules
2016-11-04 10:27:43.559 1900 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Configuration for devices up [] and devices down [] completed.
2016-11-04 10:27:44.135 1900 INFO neutron.agent.common.ovs_lib [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Port 2ba12505-9b3c-4d88-b463-5676344a32ec not present in bridge br-int
2016-11-04 10:27:44.139 1900 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] port_unbound(): net_uuid None not in local_vlan_map
2016-11-04 10:27:44.140 1900 INFO neutron.agent.securitygroups_rpc [req-f244f227-08d8-4c86-b75d-495e3e8486ed - - - - -] Remove device filter for [u'2ba12505-9b3c-4d88-b463-5676344a32ec']
```

# Khi Compute node restart

- Log xuất hiện tại file **/var/log/neutron/openvswitch-agent.log**:
```sh
2016-10-27 16:23:42.986 65813 ERROR neutron.agent.linux.utils [req-c5be491a-0477-4a42-8394-000e80000334 - - - - -] Exit code: 143; Stdin: ; Stdout: ; Stderr: 
2016-10-27 16:23:44.276 65813 ERROR neutron.agent.common.ovs_lib [req-c5be491a-0477-4a42-8394-000e80000334 - - - - -] Unable to execute ['ovs-ofctl', 'dump-flows', 'br-int', 'table=23']. Exception: Exit code: 143; Stdin: ; Stdout: ; Stderr: 
2016-10-27 16:23:44.385 65813 WARNING neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-c5be491a-0477-4a42-8394-000e80000334 - - - - -] OVS is dead. OVSNeutronAgent will keep running and checking OVS status periodically.
2016-10-27 16:23:44.387 65813 INFO neutron.plugins.ml2.drivers.openvswitch.agent.ovs_neutron_agent [req-c5be491a-0477-4a42-8394-000e80000334 - - - - -] Agent caught SIGTERM, quitting daemon loop.
```

# Troubleshoot

- Khởi tạo VM không thành công

![error-create-instance](/Images/error-create-instance.png)

**Nguyên nhân**: Do khi tạo network Admin không chọn đúng tên đã khai báo trong cấu hình ml2. 
```sh
[ml2_type_flat]
flat_networks = external
```

![error-create-instance-fix](/Images/error-create-instance-fix.png)

Phần khung đỏ phải điền đúng với interface khai báo trong file config



# Tham khảo