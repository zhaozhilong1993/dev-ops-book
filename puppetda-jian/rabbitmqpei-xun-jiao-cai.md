# RabbitMQ培训教材

[http://www.cnblogs.com/edward2013/p/5061511.html](http://www.cnblogs.com/edward2013/p/5061511.html)

节点定义

```
devstack-1 192.168.101.11
devstack-2 192.168.101.12
```

安装yum源

```
# yum install -y centos-release-openstack-ocata
```

安装rabbitmq-server

```
# yum install -y rabbitmq-server
```

# RabbitMQ集群配置

集群配置在单机配置完成的基础上进行

**设置每个节点Cookie**

Rabbitmq的集群是依赖于erlang的集群来工作的，所以必须先构建起erlang的集群环境。Erlang的集群中各节点是通过一个magic cookie来实现的，这个cookie存放在 /var/lib/rabbitmq/.erlang.cookie 中，文件是400的权限。所以必须保证各节点cookie保持一致，否则节点之间就无法通信

```
# chmod 700 /var/lib/rabbitmq/.erlang.cookie
# echo -n "AZVOCZYZZBVFLBPTBXU" > /var/lib/rabbitmq/.erlang.cookie
# chmod 400 /var/lib/rabbitmq/.erlang.cookie
```

**加入集群**

将devstack-1、devstack-2 组成集群：

默认是磁盘节点，如果是内存节点的话，需要加--ram参数

在devstack-1、devstack-2 上分别运行：

```
# rabbitmqctl stop_app
# rabbitmqctl join_cluster rabbit@server1
# rabbitmqctl start_app
```

**设置镜像策略**

```
# rabbitmqctl set_policy ha-all 
"
^
"
'
{"ha-mode":"all","ha-sync-mode":"automatic"}
'
```



