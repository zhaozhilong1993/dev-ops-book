Puppet环境的搭建

| 服务器角色 | 服务器地址 |
| :--- | :--- |
| puppet-master | 172.16.0.33 |
| puppet-agent | 172.16.0.36 |

# 1.环境准备

## 1.1 配置hosts解析

```
172.16.0.36 puppet-agent.openstacklocal

172.16.0.33 puppet-master.openstacklocal
```

这步一定要做，因为puppet的ca证书的索引都是以主机名为标示的。

# 2. 安装puppet服务

### 2.1 puppet-master节点安装puppet

我们需要在这里配置yum源,并安装puppet-server。

```
 # 安装epel源
 rpm -ivh https://yum.puppetlabs.com/el/7/products/x86_64/puppetlabs-release-7-11.noarch.rpm
 
 # 安装puppet-server
 yum install -y puppet-server
 
 # 启动puppetmaster
 systemctl start puppetmaster
```

### 2.2 puppet-agent节点安装puppet

```
# 安装puppet
yum install -y puppet
```

### 2.3 puppet-agent节点注册证书

```
# 生成证书并提交给puppet-master测试
puppet agent -t --server puppet-master.openstacklocal 
```

### 2.4 puppet-master查看注册证书

```
# 查看当前需要master签署的证书
puppet cert list


# 签署某一个证书
Puppet cert sign 
```

Agent 节点测试:

1. 运行puppet agent

Puppet agent -t —server puppet-master.openstacklocal

配置master自动签署认证：

