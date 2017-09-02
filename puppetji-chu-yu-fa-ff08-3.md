# Puppet基础语法（3）

## 参数定义

我们在一个类里面可以定义入参：

```
class openstack(
  $enable_httpd = true,
){

   if $enable_httpd {
     package { 'httpd':}
   }

}
```

这个和其他语言的写法类似。

## 类定义

我们可以在除了init.pp文件之外再新建一个文件，单独分一个类给它，如下：

```
[root@puppet-master modules]# tree  -L 3 ustack-openstack/
ustack-openstack/
├── Gemfile
├── manifests
│   ├── init.pp
│   └── vsftpd.pp # 新建的文件
├── metadata.json
├── Rakefile
├── README.md
├── spec
│   ├── classes
│   │   └── init_spec.rb
│   └── spec_helper.rb
├── templates
│   └── ustack.conf.erb
└── tests
    └── init.pp


[root@puppet-master modules]# cat ustack-openstack/manifests/init.pp |grep -v ^#
class openstack(
  $enable_httpd = true,
){

   if $enable_httpd {
     package { 'httpd':}
   }

   ## 使用include的时候是不能传递参数的
   # include ustack-openstack::vsftpd

   ## 但是这样声明一个类的时候是可以的
   class ustack-openstack::vsftpd {
     $enable_vsftpd = false
   }

}


[root@puppet-master modules]# cat ustack-openstack/manifests/vsftpd.pp |grep -v ^#

## 注意这里的类名称一定要以<模块名>::类名来作区分
class ustacl-openstack::vsftpd(
  $enable_vsftpd = true,
){

   if $enable_vsftpd {
     package { 'vsftpd':}
   }

}
```

值得注意的一点是，类的名字一定要以&lt;模块名&gt;::类名来作区分，不然，如果你在有很多个模块的时候，如果你写了一个类似class vstfpd这样的类，Puppet就不知道怎么去找这个类了。





