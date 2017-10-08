# Neutron

neutron的进程:

* neutron-server: neutron的唯一一个任务进程,管理所有的API的响应和处理
* neutron-dhcp-agent.service：复制做dhcp的namespace的分发
* neutron-l3-agent.service：Neutron的L3层的代理
* neutron-metadata-agent.service：让运行在租户网络上的虚拟机实例能够访问 OpenStack 计算服务 API 元数据
* neutron-metering-agent.service ：OpenStack的网络监控服务  
* neutron-openvswitch-agent.service：OVS服务

网桥原理

路由器／负载均衡器／dhcp服务器在网络节点上的分布

怎么定位一个端口在哪个网络节点上



## Neutron metadata

Neutron metadata 代理程序的作用是让运行在租户网络上的虚拟机实例能够访问 OpenStack 计算服务 API 元数据。流程图如下

![](/assets/neutron-metadata.png)

大体的流程是：

1. 虚拟机通过访问169.254.169.254的80端口来获取本机的元数据；
2. 改请求到达网络节点的路由器（namespace）后，被iptables重定向到9697端口；
3. 该路由器中开启了metadata-proxy代理，该服务通过unix socket链接到网络节点宿主机的metadata-agent服务（通过netstat -lxp\|grep metadata\_proxy）找到agent的进程；
4. 该metadata agent和nova通信完成虚拟机元数据的获取

东西向网络走向

南北向网络走向

