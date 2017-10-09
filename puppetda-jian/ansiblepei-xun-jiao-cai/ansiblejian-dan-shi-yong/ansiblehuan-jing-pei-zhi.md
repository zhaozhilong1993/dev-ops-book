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

我们可以启动一个，简单的脚本，就是输出一个字符串到指定的文件。首先我们需要定义主文件：

```
# vim /etc/ansible/main.yaml
- hosts: ustack
  gather_facts: no
  roles:
    - { role: nic,method: collect }
```

接下来，我们创建一个对应的role任务：

```
# mkdir -p /etc/ansible/roles/nic/tasks/
```

之后创建一个main.yaml文件，做请求的分发：

```
# vim /etc/ansible/roles/nic/tasks/main.yaml
- include: collect.yaml
  when: method == "collect"
```

之后创建一个collect.yaml文件，去实现这个具体逻辑：

```
# vim /etc/ansible/roles/nic/tasks/collect.yaml
- name: run this command and ignore the result
  shell: /usr/bin/echo "fa:16:3e:e2:21:5b" > /opt/nic
```

我们就先尝试把本机的mac地址输送到/opt/nic文件中去。

我们先尝试运行：

```
# ansible-playbook -vvv -i hosts main.yaml
```

最后会发现/opt下面就有对应的文件出现：

```
# cat /opt/nic
fa:16:3e:e2:21:5b
```

实际生产中，我们有时候会想要单独给这\[ustack\]标签中的主机分别传送一些值，这个要怎么做呢？  
这就用到了host\_vars和role\_vars目录。  
我们先把这两个目录建立出来。

```
# mkdir /etc/ansible/hosts_var
# mkdir /etc/ansible/group_vars
```

之后在hosts\_var下面建立对应的IP的文件，如：

```
# vim /etc/ansible/hosts_var/127.0.0.1
nic_mac: fa:16:3e:e2:21:5b

# vim /etc/ansible/hosts_var/192.168.20.25
nic_mac: fa:16:3e:09:7c:af

# vim /etc/ansible/hosts_var/192.168.20.26
nic_mac: fa:16:3e:0e:04:a0
```

之后修改我们的role文件：

```
# vim /etc/ansible/roles/nic/tasks/collect.yaml
- name: run this command and ignore the result
  shell: /usr/bin/echo {{ nic_mac }} > /opt/nic
```

这样我们能看到

