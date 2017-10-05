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

### 迁移虚拟机

* 冷迁移：虚拟机处于关闭状态
* 热迁移：虚拟机处于开启状态

OpenStack迁移虚拟机需要用到共享存储（如：Ceph），就是虚拟机的系统盘在每个计算节点上都是可共享的。（简单点说）冷迁移其实就是把虚拟机的xml文件拷贝一份到另一个计算节点上面，然后把系统盘挂载到这个计算节点上，并启动这个虚拟机的过程。一般来说，冷迁移成功的概率高。

热迁移的过程中，会有一种情况就是，虚拟机不断的写盘，然后写盘的速率大于物理机用网卡往另一台物理机拷贝数据的速率。这样就会造成你的热迁移的操作会一直超时。



