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
...
# 确定密码
CONFIG_DEFAULT_PASSWORD=ustack

# 选择要安装哪些组件
CONFIG_MARIADB_INSTALL=y
CONFIG_GLANCE_INSTALL=y
CONFIG_CINDER_INSTALL=y
CONFIG_MANILA_INSTALL=n
CONFIG_NOVA_INSTALL=y
CONFIG_NEUTRON_INSTALL=y
CONFIG_HORIZON_INSTALL=y
CONFIG_SWIFT_INSTALL=n
CONFIG_CEILOMETER_INSTALL=y
CONFIG_AODH_INSTALL=y
CONFIG_GNOCCHI_INSTALL=y
CONFIG_SAHARA_INSTALL=n
CONFIG_HEAT_INSTALL=y
CONFIG_TROVE_INSTALL=n
CONFIG_IRONIC_INSTALL=n
CONFIG_CLIENT_INSTALL=y

# NTP服务器的地址
CONFIG_NTP_SERVERS=202.120.2.101

# 测试环境就不安装ngios监控了
CONFIG_NAGIOS_INSTALL=n

# 定义某些节点的位置
CONFIG_CONTROLLER_HOST=192.168.20.27
CONFIG_COMPUTE_HOSTS=192.168.20.26
CONFIG_NETWORK_HOSTS=192.168.20.25
```

之后运行packstack

```
# packstack --answer-file=/root/ans.txt
```



