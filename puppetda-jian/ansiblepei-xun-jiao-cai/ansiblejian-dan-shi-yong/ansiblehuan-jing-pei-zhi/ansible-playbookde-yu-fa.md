# Ansible Playbook的语法

我们在上面一节内容中已经把playbook的环境给配置好了。那么我们在这里将会简单的介绍ansible的基础语法。

## 循环

为了保持简洁,重复的任务可以用以下简写的方式:

```
- name: add several users
  user: name={{ item }} state=present groups={{ item }}
  with_items:
     - testuser1
     - testuser2
```

我们也支持字典类型的循环：

```
- name: add several users
  user: name={{ item.name }} state=present groups={{ item.groups }}
  with_items:
    - { name: 'testuser1', groups: 'wheel' }
    - { name: 'testuser2', groups: 'root' }
```

我们有时候也会声明一个变量，我们也可以对一个变量进行循环.

```
# vim /etc/ansible/group_vars/ustack
domain: ustack.com
users:
  alice:
      name: Alice Appleworth
      telephone: 123-456-7890
  bob:
  name: Bob Bananarama
    telephone: 987-654-3210

# vim /etc/ansible/roles/nic/tasks/main.yaml
tasks:
  - name: Print phone records
    debug: msg="User {{ item.key }} is {{ item.value.name }} ({{ item.value.telephone }})"
    with_dict: "{{users}}"
```

## 注册变量

```
# vi /etc/ansible/roles/nic/tasks/main.yaml

- name: register values
  shell: /usr/bin/cat /opt/domain
  register: domain
  ignore_errors: True

- name: Print register values
  debug: msg="register domain is {{ domain.stdout }}"
```

我们在这里把/opt/domain的值纪录到我们的变量domain中。

## 获取主机的基本信息--fact

有时我们需要获取目标主机的一些信息，如固定的IP地址，磁盘状况等等。我们当然可以自己写一些脚本来获取我们自己想要的值。我们用下面的命令可以看到，ansible内部自带的一些脚本给我们收集到的机器信息：

```
# ansible <hostname> -m setup
```

hostname要换成你主机标签的名字。

然后我们在我们的roles文件里面，可以这样写：

```
- name: test
  debug: msg="ansible factor {{ ansible_hostname }}"
```

## 自定义fact值

[http://ju.outofmemory.cn/entry/104885](http://ju.outofmemory.cn/entry/104885)

```
# vim /etc/ansible/facts.d/test.fact
[testdir]
test_mem_size=1024
test_cpu=8
```

这样

## 模版控制

我们有时候需要根据当前的环境，生成一个我们定义的配置文件。那么这部分怎么写呢？

```
template: src=foo.cfg.j2 dest={{ remote_install_path }}/foo.cfg
```



