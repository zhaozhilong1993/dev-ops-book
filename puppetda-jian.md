Puppet环境的搭建

| 服务器角色 | 服务器地址 |
| :--- | :--- |
| puppet-master | 172.16.0.33 |
| puppet-agent | 172.16.0.36 |



# 1.环境准备

## 1.1 配置hosts解析

172.16.0.36 puppet-agent.openstacklocal

172.16.0.33 puppet-master.openstacklocal

这步一定要做，因为puppet的ca证书的索引都是以主机名为标示的。





master节点：

安装puppet master:

1. 配置yum源

rpm -ivh

https:

/

/yum.

[puppetlabs.com/el](http://puppetlabs.com/el)

/7/products

/x86\_64/puppetlabs

-release-

7

-

11

.noarch.rpm

1. 安装puppet-server

yum

install

-y puppet-

server

1. 启动puppetmaster

systemctl start puppetmaster

Agent节点：

安装puppet agent:

1. 安装puppet

Yum install -y puppet

1. 注册puppet agent

Puppet agent -t --server puppet-master.openstacklocal

Master 节点认证：

1. 查看需要认证的文件

Puppet cert list

1. 认证文件

Puppet cert sign

&lt;

cert\_name

&gt;

Agent 节点测试:

1. 运行puppet agent

Puppet agent -t —server puppet-master.openstacklocal

配置master自动签署认证：

