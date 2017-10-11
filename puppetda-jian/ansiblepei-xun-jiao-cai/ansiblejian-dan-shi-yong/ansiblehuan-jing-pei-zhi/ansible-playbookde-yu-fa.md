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

## 注册变量



