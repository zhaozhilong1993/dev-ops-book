# Puppet基础语法（2）

我们在上节使用了Puppet的去创建用户，安装包，启动服务，还有创建文件。但是，这些步骤，在实际生产中是有先后顺序，并且有依赖关系的。比如，你要启动一个服务之前，你肯定需要先安装包，在创建完一个文件之后，你可能需要重启服务。这些都是有对应的逻辑操作的。所以，这节就是需要我们来讲解Puppet的逻辑操作：

首先回顾一下，我们刚刚写的init.pp的文件：

```
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

正常的流程应该是：

```
[package的安装] -> [file的创建] -> [service的启动]
```

这个在Puppet里面就叫做关系链。

### Puppet的关系链

我们在这里就需要使用puppet去创建我们关系链。

```
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

   # puppet运行的关系链
   Package['httpd'] -> File['/etc/httpd/conf.d/ustack.conf'] -> Service['httpd']
}
```

接下来在puppet-agent端运行我们的测试

```
 [root@puppet-agent ~]# puppet agent -t --server puppet-master.openstacklocal
 [root@puppet-agent ~]# echo "hello world" > /var/www/html/index.html
 [root@puppet-agent ~]# curl 127.0.0.1:8888
 hello world
```

## Puppet的逻辑顺序控制\(before,require\)

### before

某个操作在某个操作之前

### require

某个操作依赖于某个操作

我们可以通过一个实用案例来体验，如：

```
class openstack {

   package { 'httpd':}

   service { 'httpd':
     ensure => running,
     enable => true,
   }

   file { '/etc/httpd/conf.d/ustack.conf':
     ensure  => present,
     owner   => 'apache',
     group   => 'apache',
     mode    => '0777',
     replace => true,
     content => template("ustack-openstack/ustack.conf.erb"),
   }

   exec { 'add-index':
     cwd => "/var/www/html",
     command => "/usr/bin/echo 'hello puppet world' > index.html",
     before => Exec['check-port'], # 这个add-index操作要在下面的check-port之前
   }

   exec { 'check-port':
     command => "/usr/bin/curl 127.0.0.1:8888 > /mnt/web_log",
     require => Service['httpd'], # 这个check-port操作需要依赖于httpd的服务
   }

   Package['httpd'] -> File['/etc/httpd/conf.d/ustack.conf'] -> Service['httpd']
}
```

注意在Puppet中的关系链的声明或者requirt／before之类的属性调用的时候，对应的资源的首字母要大写，如：

```
Service['httpd']就对应的 service {'httpd':}这个类
```

## Puppet的触发操作属性 （subscribe，notify）

### notify

通知某个资源进行更新。

```
notify => Type1[‘title1’]，表示notify所在资源执行后通知’title1’，经常用于配置文件更新后通知服务重启。
```

### subscribe

资源有更新时，通知另一个资源执行相应的动作。

```
subscribe => Type1[‘title1’]，表示subscribe所在资源关心资源’title1’，当’title1’发生变化了会通知subscribe所在资源。
```

目前支持subscribe只有exec、service、mount。

notify和subscribe是对应的，在一个资源里使用了notify，就相当于在另一个资源中使用了subscribe。

