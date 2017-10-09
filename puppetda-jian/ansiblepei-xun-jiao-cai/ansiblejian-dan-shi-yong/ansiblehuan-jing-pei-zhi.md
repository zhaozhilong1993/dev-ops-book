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
127.0.0.1
192.168.20.2[5:6]
```

我们现在先来创建一个role任务：

```
# mkdir -p /etc/ansible/roles/nic/tasks/
```

之后创建一个main.yaml文件，做请求的分发：

```
- include: collect.yaml
  when: method == "collect"
```



实际生产中，我们有时候会想要单独给这\[ustack\]标签中的主机分别传送一些值，这个要怎么做呢？  
这就用到了host\_vars和role\_vars目录。  
我们先把这两个目录建立出来。

```
# mkdir /etc/ansible/hosts_var
# mkdir /etc/ansible/group_vars
```



