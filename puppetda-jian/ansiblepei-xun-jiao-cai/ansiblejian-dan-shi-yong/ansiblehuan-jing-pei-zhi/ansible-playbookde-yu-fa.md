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

tasks:
  - name: Print phone records
    debug: msg="User {{ item.key }} is {{ item.value.name }} ({{ item.value.telephone }})"
    with_dict: "{{users}}"
```

## 注册变量

```
     - shell: /usr/bin/foo
       register: foo_result
       ignore_errors: True

     - shell: /usr/bin/bar
       when: foo_result.rc == 5
```



