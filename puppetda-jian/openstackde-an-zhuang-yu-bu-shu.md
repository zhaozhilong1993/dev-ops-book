# OpenStack的安装与部署【L版】

安装前准备

节点规划

| 角色 | IP |
| :--- | :--- |
| controller |  |
| network |  |
| compute |  |

安装yum源

```
# yum install centos-release-openstack-liberty
```

之后更新机器

```
# yum update
```

接着把openstack的命令行工具和openstack-selinux这个对selinux的管理工具也装上

```
# yum install python-openstackclient
# yum install openstack-selinux
```

Controller节点：

安装数据库：

```

```



