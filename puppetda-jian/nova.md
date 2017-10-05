# Nova

Nova的各个进程的作用

* nova-api：提供nova的api服务
* nova-scheduler: 调度服务，选择一个合适的计算节点进行虚拟机的创建
* nova-conductor: 提供与数据库的交互服务
* nova-compute: 提供计算服务（计算节点上特有的）

## Nova的工作流

### 创建虚拟机

```
nova-api -> nova-scheduler -> nova-compute <--> nova-conductor
```

nova-api接收到API请求之后会把这个创建虚拟机的请求交给nova-scheduler去执行，最后nova-scheduler会给nova-compute发送一个rpc请求。在nova-compute接收到这个请求之后，会根据配置文件中指定的virt driver的值去调用底层的libvirtd的接口：

```
nova-compute -> virt driver -> libvirtd -> [管控虚拟机]
```

在这个过程中nova-compute会给nova-conductor发送rpc请求，去更新数据库里面的信息。

### 冷迁移虚拟机



### 热迁移虚拟机



