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
 [root@puppet-master ssl]# rpm -ivh https://yum.puppetlabs.com/el/7/products/x86_64/puppetlabs-release-7-11.noarch.rpm

 # 安装puppet-server
 [root@puppet-master ssl]# yum install -y puppet-server

 # 启动puppetmaster
 [root@puppet-master ssl]# systemctl start puppetmaster
```

### 2.2 puppet-agent节点安装puppet

```
# 安装puppet
[root@puppet-agent ssl]# yum install -y puppet
```

### 2.3 puppet-agent节点注册证书

```
# 生成证书并提交给puppet-master测试
[root@puppet-agent ssl]# puppet agent -t --server puppet-master.openstacklocal
```

### 2.4 puppet-master查看注册证书

```
# 查看当前需要master签署的证书
[root@puppet-master ssl]# puppet cert list
  "puppet-agent.openstacklocal" (SHA256) 9C:B2:C1:4A:D5:A6:8B:26:B1:D2:C8:43:F5:7E:C1:00:61:D7:B5:60:D2:7C:A2:2B:3E:0D:6B:4E:94:F5:BB:44

# 签署某一个证书
[root@puppet-master ssl]# puppet cert sign puppet-agent.openstacklocal
Notice: Signed certificate request for puppet-agent.openstacklocal
Notice: Removing file Puppet::SSL::CertificateRequest puppet-agent.openstacklocal at '/var/lib/puppet/ssl/ca/requests/puppet-agent.openstacklocal.pem'
```

PS：如果我们希望删除某一个证书，我们可以运行这个命令：

```
# 删除某一个证书
[root@puppet-master ssl]# puppet cert clean puppet-agent.openstacklocal
```

### 2.5 puppet-agent 节点再次运行puppet

```
[root@puppet-agent ssl]# puppet agent -t --server puppet-master.openstacklocal
```



