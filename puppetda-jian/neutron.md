# Neutron

## neutron的进程

* neutron-server: neutron的唯一一个任务进程,管理所有的API的响应和处理
* neutron-dhcp-agent.service：复制做dhcp的namespace的分发
* neutron-l3-agent.service：Neutron的L3层的代理
* neutron-metadata-agent.service：让运行在租户网络上的虚拟机实例能够访问 OpenStack 计算服务 API 元数据
* neutron-metering-agent.service ：OpenStack的网络监控服务  
* neutron-openvswitch-agent.service：OVS服务

## 网络资源定位

我们的【路由器／负载均衡器／dhcp服务器】都是以单独的一个namespace在在网络节点上的分布。所以我们只要知道【路由器／负载均衡器／dhcp服务器】这三个资源的UUID就可以找到对应的namespace。如：

```
# neutron router-list
+--------------------------------------+---------+-----------------------------------------------------------------------------------------------------------------------------------+-------------+-------+
| id                                   | name    | external_gateway_info                                                                                                             | distributed | ha    |
+--------------------------------------+---------+-----------------------------------------------------------------------------------------------------------------------------------+-------------+-------+
| 24ab8049-7e70-45ed-9d0b-1bacc4789a53 | router1 | {"network_id": "eada98ee-3cb8-46dd-8088-07d2dfb93032", "enable_snat": true, "external_fixed_ips": [{"subnet_id": "0449fae4-3fed-  | False       | False |
|                                      |         | 448f-9e79-7c848d615d1c", "ip_address": "172.24.4.231"}]}                                                                          |             |       |
+--------------------------------------+---------+-----------------------------------------------------------------------------------------------------------------------------------+-------------+-------+
```

找到UUID之后，在对应的网络节点：

```
[root@packstack-3 ~]# ip netns | grep 24ab8049-7e70-45ed-9d0b-1bacc4789a53
qrouter-24ab8049-7e70-45ed-9d0b-1bacc4789a53
```

你就能找到对应的namespace。

## 怎么定位一个端口在哪个网络节点上

## Neutron metadata

Neutron metadata 代理程序的作用是让运行在租户网络上的虚拟机实例能够访问 OpenStack 计算服务 API 元数据。流程图如下

![](/assets/neutron-metadata.png)

大体的流程是：

1. 虚拟机通过访问169.254.169.254的80端口来获取本机的元数据；
2. 改请求到达网络节点的路由器（namespace）后，被iptables重定向到9697端口；
3. 该路由器中开启了metadata-proxy代理，该服务通过unix socket链接到网络节点宿主机的metadata-agent服务（通过netstat -lxp\|grep metadata\_proxy）找到agent的进程；
4. 该metadata agent和nova通信完成虚拟机元数据的获取

## Linux网桥

简单来说，桥接就是把一台机器上的若干个网络接口“连接”起来。其结果是，其中一个网口收到的报文会被复制给其他网口并发送出去。以使得网口之间的报文能够互相转发。在linux系统中，我们可以用下面的命令查看／添加／删除网桥：

```
查看网桥
# brctl show

添加网桥
# brctl addbr virtbr0

添加网桥接口
# brctl addif virtbr0 eth0

删除网桥接口
# brctl delif virtbr0 eth0
```

## 网络走向\(公网流量\)

![](/assets/neutron-net2.png)  
1\)流量经由虚拟机IP内核交给虚拟网卡处理，虚拟网卡由TAP软件实现，TAP允许用户态程序向内核协议栈注入数据，它可以运行于虚拟机操作系统之上，能够提供与硬件以太网卡完全相同的功能。

2\)TAP设备并不是直接连接到OVS上的，而是通过linux bridge中继到ovs br-int上，其原因在于ovs无法实现linux bridge中一些带状态的iptables规则，而这些规则往往用于以虚拟机为单位的安全组（security group）功能的实现。qbr是quantum bridge的缩写，Neutron中沿用了Quantum的叫法。

3\)linux bridge与ovs br int间的连接通过veth-pair技术实现，qvb代表quantum veth bridge，qvo代表quantum veth ovs。veth-pair用于连接两个虚拟网络设备，总是成对出现以模拟虚拟设备间的数据收发，其原理是反转通讯数据的方向，需要发送的数据会被转换成需要收到的数据重新送入内核网络层进行处理。veth-pair与tap的区别可以简单理解为veth-pair是软件模拟的网线，而tap是软件模拟的网卡。

4\)ovs br-int是计算节点本地的虚拟交换设备，根据neutron-server中OVS Plugin的指导，完成流量在本地的处理：本地虚拟机送入的流量被标记本地VLAN tag，送到本地虚拟机的流量被去掉本地VLAN tag，本地虚拟机间的2层流量直接在本地转发，本地虚拟机到远端虚拟机、网关的流量由int-br-eth1送到ovs br-eth1上（在Overlay模型中送到ovs br-tun上）。无论是VLAN模型还是Overlay模型，由于br-int上VLAN数量的限制，计算节点本地最多支持4K的租户。

5\)ovs br-int与ovs br-eth1间的连接通过veth-pair技术实现。

6\)ovs br-eth1将该计算节点与其他计算节点、网络节点连接起来，根据neutron-server中OVS Plugin的指导，完成流量送出、送入本地前的处理：根据底层物理网络租户VLAN与本地租户VLAN间的映射关系进行VLAN ID的转换（Overlay模型中此处进行隧道封装，并进行VNI与本地租户VLAN ID间的映射）。由于底层物理网络中VLAN数量的限制，VLAN模型最多支持4K的租户，而Overlay模型中，24位的VNI最多支持16million的租户。

7\)ovs br-eth1直接关联物理宿主机的硬件网卡eth1，通过eth1将数据包送到物理网络中。Overlay模型中ovs br-tun通过TUN设备对数据包进行外层隧道封装并送到HyperVisor内核中，内核根据外层IP地址进行选路，从硬件网卡eth1将数据包送到物理网络中。TUN与TAP的实现机制类似，区别在于TAP工作在二层，而TUN工作在三层。

