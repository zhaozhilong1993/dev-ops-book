Cinder的进程介绍

cinder主要有4个进程组成：

* cinder-api ：主要提供API服务
* cinder-volume：
* cinder-backup：
* cinder-scheduler：

cinder其实只是一个抽象层，它本身并不是一种存储技术，它为其他真正的存储技术，如：SAN，Ceph，NAS等等，提供了统一化的接口。其实也是由cinder-volume这个进程来完成的。其他不同厂商要一 般都是以在cinder里面提供driver的形式来提供对应的支持的。所有具体的driver的提供形式都在driver这个目录下面。如，默认我当前使用的driver:

```
default_volume_type = iscsi
enabled_backends = lvm

[lvm]
iscsi_helper=lioadm
iscsi_ip_address=192.168.20.27
volume_driver=cinder.volume.drivers.lvm.LVMVolumeDriver
```

Cinder  Volume-type的原理

Cinder Qos的设置原理

Cinder创建存储卷的过程

Cinder挂载存储卷的过程

