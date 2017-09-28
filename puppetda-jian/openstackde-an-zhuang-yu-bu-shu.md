# OpenStack的安装与部署【N版】

## Packstack安装

节点规划

| 角色 | IP |
| :--- | :--- |
| controller | 192.168.20.27 |
| network | 192.168.20.27 |
| compute | 192.168.20.27 |

安装yum源

```
# yum install centos-release-openstack-newton
```

之后安装packstack

```
# yum install openstack-packstack -y
```

之后生成配置文件

```
# packstack --gen-answer-file=/root/ans.txt
```

之后修改配置文件，指定安装的模块与用户密码等等

```
# vim /root/ans.txt
```

之后运行packstack

```
# packstack --answer-file=/root/ans.txt
```



