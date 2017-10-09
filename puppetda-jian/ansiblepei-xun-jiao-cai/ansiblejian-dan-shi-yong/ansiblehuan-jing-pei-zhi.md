# Ansible环境配置

我们在上一章中对ansible进行了简单的介绍，我们接下来讲讲ansible的目录结构以及多环境的配置。

我们先看看目录结构：

```
# tree /etc/ansible
.
├── ansible.cfg
├── hosts
└── roles
```

默认的目录下面就有3个配置文件：

* ansible.cfg是主要的配置文件
* hosts定义了主机列表
* role是每一个task的角色列表。

我们在上一章中，知道在hosts中可以配置主机的列表，类似：
```
[ustack]
172.16.0.36
172.16.0.37
172.16.0.38
```





