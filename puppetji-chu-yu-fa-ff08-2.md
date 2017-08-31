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

### Puppet的关系链

在上节中，我们需要

```

```



