# Puppet基础语法

在上节中，我们介绍了Puppet的基本文件结构，结下来我们就要对，我们在上节自定义的ustack-openstack这个Puppet模块写一些简单的例子，来加深我们对Puppet的理解。

我们现在先来看看ustack-openstack这个目录的结构：

```
[root@puppet-master modules]# tree -L 3 /etc/puppet/modules/ustack-openstack/
/etc/puppet/modules/ustack-openstack/
├── Gemfile
├── manifests
│   └── init.pp # 我们的主逻辑文件
├── metadata.json
├── Rakefile
├── README.md
├── spec
│   ├── classes
│   │   └── init_spec.rb
│   └── spec_helper.rb
└── tests
    └── init.pp
```

我们接下来就在manifests的init.pp文件里面书写我们的代码。

### 创建系统用户 -- user类

我们在puppey-master中定义了这个类：

```
[root@puppet-master manifests]# cat init.pp  |grep -v ^#
class openstack {
   user { 'ustack':
     ensure => present,
     uid => '1002',
     gid => '01',
     shell => '/bin/bash',
     home => '/home/gary',
   }

}
```

接下来在puppet-agent端运行我们的测试

```
 [root@puppet-agent ~]# puppet agent -t --server puppet-master.openstacklocal
 [root@puppet-agent ~]# id ustack
 uid=1002(ustack) gid=1(bin) groups=1(bin)
```

## 安装软件包 --  package类

```
[root@puppet-master manifests]# cat init.pp  |grep -v ^#
class openstack {
   package { 'httpd':}
}
```

接下来在puppet-agent端运行我们的测试

```
 [root@puppet-agent ~]# puppet agent -t --server puppet-master.openstacklocal
 [root@puppet-agent ~]# rpm -qa | grep httpd
 httpd-2.4.6-45.el7.centos.4.x86_64
```

## 服务的启动与停止 -- service类

```
[root@puppet-master manifests]# cat init.pp  |grep -v ^#
    class openstack {
        package { 'httpd':
    }

    service { 'httpd':
        ensure => running,
        enable => true,
    }
}
```

接下来在puppet-agent端运行我们的测试

```
 [root@puppet-agent ~]# puppet agent -t --server puppet-master.openstacklocal
 [root@puppet-agent ~]# systemctl status vsftpd
● vsftpd.service - Vsftpd ftp daemon
   Loaded: loaded (/usr/lib/systemd/system/vsftpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2017-08-31 13:39:32 CST; 2min 38s ago
```

## 文件管理 -- file类

文件的管理相对来说比较复杂

```

```



