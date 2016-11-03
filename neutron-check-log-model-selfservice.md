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





# Tham khảo