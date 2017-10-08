# Cinder的进程介绍

cinder主要有4个进程组成：

* cinder-api ：主要提供API服务
* cinder-volume：主要实现创建磁盘等操作的逻辑层
* cinder-backup：备份volume
* cinder-scheduler：调度一个后段节点创建volume

cinder其实只是一个抽象层，它本身并不是一种存储技术，它为其他真正的存储技术，如：SAN，Ceph，NAS等等，提供了统一化的接口。其实也是由cinder-volume这个进程来完成的。其他不同厂商要一 般都是以在cinder里面提供driver的形式来提供对应的支持的。所有具体的driver的提供形式都在driver这个目录下面。

## Cinder  Volume-type

我们看到当前的cinder的配置文件。

```
[DEFAULT]
default_volume_type = iscsi
enabled_backends = lvm

[lvm]
iscsi_helper=lioadm
iscsi_ip_address=192.168.20.27
volume_driver=cinder.volume.drivers.lvm.LVMVolumeDriver
```

会有一个volume_type的标签，volume\_type对应多个后段存储标签。我们用如下命令：_

```
# cinder type-create lvm-self
+--------------------------------------+------+-------------+-----------+
| ID                                   | Name | Description | Is_Public |
+--------------------------------------+------+-------------+-----------+
| 98f4fdea-ac88-4dbc-856c-c489cc4474a5 | lvm-self  | -           | True      |
+--------------------------------------+------+-------------+-----------+

# cinder type-list
# cinder type-key lvm-self set volume_backend_name=LVM_iSCSI
# cinder extra-specs-list
```

我们在上面创建了一个名为“lvm”的存储类型，并且指定他的名字为“LVM\_iSCSI”。

想要让这个存储类型生效，我们还需要在配置文件里面指定这个存储类型的一些信息：

```
# vim /etc/cinder/cinder.conf
...
# 我原来的环境里面有另一个backends叫lvm，我们就加了一个叫LVM_iSCSI的backends
enabled_backends = lvm,LVM_iSCSI
...
[LVM_iSCSI]
iscsi_helper=lioadm
iscsi_ip_address=192.168.20.27
volume_driver=cinder.volume.drivers.lvm.LVMVolumeDriver
volumes_dir=/var/lib/cinder/volumes
volume_backend_name=LVM_iSCSI
```

之后重启cinder的服务，并创建一个这个volume-type的存储卷：

```
# systemctl restart openstack-cinder*
# cinder create 1 --volume-type=lvm-self --name volume3
```

## Cinder Qos的设置原理

Cinder 的Qos是在OpenStack 的H版中加入进来的，主要是用于限制磁盘IO。cinder的Qos只能是针对一个volume-type而言的，而不是针对某一个磁盘，所以在我们定义好的Qos的规则需要和volume-type关联。

```
# cinder qos-create read_qos consumer="front-end" read_iops_sec=1000
# cinder qos-associate [qos-spec-id] [volume-type-id]
```

Cinder创建存储卷的过程

```
cinder-api(收到API请求) --> rpc调用 --> cinder-volume（建立卷）
```

Cinder挂载存储卷的过程

```
 nova --> cinder.volume.manager.VolumeManager.initialize_connection() 
 --> 创建iscsi的target -> nova根据target中的IP地址进行卷的挂载
```



