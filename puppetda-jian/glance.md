Glance基本介绍

Cinder的进程介绍

cinder主要有4个进程组成：

* cinder-api ：主要提供API服务
* cinder-volume：
* cinder-backup：
* cinder-scheduler：

cinder其实只是一个抽象层，它本身并不是一种存储技术，它为其他真正的存储技术，如：SAN，Ceph，NAS等等，提供了统一化的接口。

