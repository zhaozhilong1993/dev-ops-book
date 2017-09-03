# Ansible简单使用

ansible实验环境

| 服务器角色 | 服务器地址 |
| :--- | :--- |
| ansible-master | 172.16.0.33 |
| ansible-agent | 172.16.0.36 |

在ansible-maste上安装ansible

```
yum install ansible
```

之后在ansible的hosts文件里面创建一个组，我们这里创建了一个叫做ustack的组：

```
[root@puppet-master ansible]# cat /etc/ansible/hosts  |grep -v ^# | grep -v ^$
[ustack]
172.16.0.36
```



参考资料：

[http://www.ansible.com.cn/docs/intro.html](http://www.ansible.com.cn/docs/intro.html)

