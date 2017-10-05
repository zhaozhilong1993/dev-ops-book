Nova

Nova的各个进程的作用

* nova-api：提供nova的api服务
* nova-scheduler: 调度服务，选择一个合适的计算节点进行虚拟机的创建
* nova-container: 提供与数据库的交互服务
* nova-compute: 提供计算服务（计算节点上特有的）

Nova的工作流

```
nova-api -> nova-scheduler -> nova-compute <--> nova-container
```

启动虚拟机

冷迁移虚拟机

热迁移虚拟机

