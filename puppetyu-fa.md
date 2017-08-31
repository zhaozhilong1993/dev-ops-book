# Puppet基础语法（1）

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
    package { 'httpd':}

    service { 'httpd':
        ensure => running,
        enable => true,
    }
}
```

接下来在puppet-agent端运行我们的测试

```
[root@puppet-agent ~]# puppet agent -t --server puppet-master.openstacklocal
[root@puppet-agent conf.d]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2017-08-31 13:50:51 CST; 1min 29s ago
     Docs: man:httpd(8)
           man:apachectl(8)
```

## 运行shell命令 -- exec类

我们有时候希望运行一些shell命令，可以这样写:

```
[root@puppet-master manifests]# cat init.pp  |grep -v ^#
class openstack {
   exec { 'add-local-hosts':
     cwd => "/mnt/", # 执行命令的位置
     command => "/usr/bin/touch file", # 执行的命令
   }
}
```

接下来在puppet-agent端运行我们的测试

```
[root@puppet-agent ~]# puppet agent -t --server puppet-master.openstacklocal
[root@puppet-agent conf.d]# ls /mnt
file
```



## 管理定时任务 -- cron类

```
[root@puppet-master manifests]# cat init.pp  |grep -v ^#
class openstack {

    cron { "clean-log":
        command => "/usr/sbin/echo > /var/log/http/access.log",
        user => root,
        hour => 2,
        minute => 0
    }
}
```

除了用户和command两个参数以外,其他的参数都是可选项.参见：[http://puppet.wikidot.com/cron](http://puppet.wikidot.com/cron)

接下来在puppet-agent端运行我们的测试

```
[root@puppet-agent ~]# puppet agent -t --server puppet-master.openstacklocal
[root@puppet-agent conf.d]# crontab -l
0 2 * * * /usr/sbin/echo > /var/log/http/access.log
```



## 文件管理 -- file类

文件的管理相对来说比较复杂.

```
# 首先先写主逻辑的文件
[root@puppet-master manifests]# cat init.pp  |grep -v ^#
class openstack {
   package { 'httpd':}

   service { 'httpd':
     ensure => running,
     enable => true,
   }

   file { '/etc/httpd/conf.d/ustack.conf':
     ensure  => present, # 是否创建
     owner   => 'apache', # 文件的用户名
     group   => 'apache', # 文件的所属组
     mode    => '0777', # 权限
     replace => true, # 如果文件已存在，是否替换
     content => template("ustack-openstack/ustack.conf.erb"), # 文件内容的模版的位置
   }
}
```

到这一步并没有全部结束，我们还需要创建一个名为ustack.conf.erb的文件模版。默认puppet会去下面这个路径去找对应的模版：

&lt;模块名&gt;／templates/&lt;模版文件名&gt;

所以，我们需要创建这个模版：

```
# 首先建立当前模块的模版文件的目录
[root@puppet-master ustack-openstack]# mkdir /etc/puppet/modules/ustack-openstack/templates

# 在这个目录下面建立模版文件
[root@puppet-master ustack-openstack]# cat /etc/puppet/modules/ustack-openstack/templates/ustack.conf.erb
Listen 8888
<VirtualHost *:8888>
        DocumentRoot /var/www/html
        <Directory /var/www/html>
                Options None
                AllowOverride None
                Order allow,deny
                allow from all
        </Directory>
        ErrorLog logs/ustack-error_log
        CustomLog logs/ustack-access_log combined
</VirtualHost>
```

接下来在puppet-agent端运行我们的测试

```
[root@puppet-agent ~]# puppet agent -t --server puppet-master.openstacklocal

# 查看文件是否建立
[root@puppet-agent conf.d]# cat /etc/httpd/conf.d/ustack.conf
Listen 8888
<VirtualHost *:8888>
        DocumentRoot /var/www/html
        <Directory /var/www/html>
                Options None
                AllowOverride None
                Order allow,deny
                allow from all
        </Directory>
        ErrorLog logs/ustack-error_log
        CustomLog logs/ustack-access_log combined
</VirtualHost>

[root@puppet-agent conf.d]# ll  /etc/httpd/conf.d/ustack.conf
-rwxrwxrwx 1 apache apache 355 Aug 31 14:00 /etc/httpd/conf.d/ustack.conf
```

到这一步，我们只是创建了一个httpd的conf.d的文件，但是我们并没有重启服务，所以，希望这个文件生效，我们现在需要手动重启服务：

```
[root@puppet-agent ~]# systemctl restart httpd
[root@puppet-agent ~]# echo "hello puppet world" > /var/www/html/index.html
[root@puppet-agent ~]# curl 127.0.0.1:8888
hello puppet world
```

在实际使用中，我们肯定不希望我们每加一个文件都需要重启httpd,所以，我们需要在建立conf.d文件的时候，去通知httpd，让他重启。怎么做呢？puppet内部的核心函数里面就有这个方法notify.

