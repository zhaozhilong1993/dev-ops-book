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

Ansible 可同时操作属于一个组的多台主机,组和主机之间的关系通过 inventory 文件配置. 默认的文件路径为 /etc/ansible/hosts

除默认文件外,你还可以同时使用多个 inventory 文件\(后面会讲到\)，我们这里创建了一个叫做ustack的组：

```
[root@puppet-master ansible]# cat /etc/ansible/hosts  |grep -v ^# | grep -v ^$
[ustack]
172.16.0.36
```

之后尝试执行我们的第一条命令,我们会发现有ssh连接不上的error.

```
#ansible ustack -m ping
ansible ustack -m ping
The authenticity of host '172.16.0.36 (172.16.0.36)' can't be established.
ECDSA key fingerprint is ab:c4:10:e7:6d:8d:ce:03:37:a4:bf:bc:01:b9:61:f4.
Are you sure you want to continue connecting (yes/no)? yes
172.16.0.36 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Warning: Permanently added '172.16.0.36' (ECDSA) to the list of known hosts.\r\nPermission denied (publickey,password).\r\n",
    "unreachable": true
}
```

因为ansible是一个封装了ssh的脚步工具，在连接每一台ansible的slave主机的时候都需要配置ssh的免秘钥登陆。

```
#ssh-keygen
#ssh-copy-id 172.16.0.36
```

配置好免秘钥登陆之后，就可以正常执行我们的ansible命令了。

```
# ansible ustack -m ping
172.16.0.36 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

参考资料：

【强烈推介】这下面的链接是ansible的官方文档，很详细。

[http://www.ansible.com.cn/docs/intro.html](http://www.ansible.com.cn/docs/intro.html)

