Glance基本介绍

Cinder的进程介绍

cinder主要有4个进程组成：

* cinder-api ：主要提供API服务
* cinder-volume：
* cinder-backup：
* cinder-scheduler：

cinder其实只是一个抽象层，它本身并不是一种存储技术，它为其他真正的存储技术，如：SAN，Ceph，NAS等等，提供了统一化的接口。其实也是由cinder-volume这个进程来完成的。其他不同厂商要一 般都是以在cinder里面提供driver的形式来提供对应的支持的。



