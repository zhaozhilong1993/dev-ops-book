# Neutron

neutron的进程:

* neutron-server: neutron的唯一一个任务进程,管理所有的API的响应和处理
* neutron-dhcp-agent.service：复制做dhcp的namespace的分发
* neutron-l3-agent.service：Neutron的L3层的代理
* neutron-metadata-agent.service：
* neutron-metering-agent.service    
* neutron-openvswitch-agent.service

网桥原理

路由器／负载均衡器／dhcp服务器在网络节点上的分布

怎么定位一个端口在哪个网络节点上

东西向网络走向

南北向网络走向

