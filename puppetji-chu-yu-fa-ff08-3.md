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
│   ├── init.pp
│   └── vsftpd.pp # 新建的文件
├── metadata.json
├── Rakefile
├── README.md
├── spec
│   ├── classes
│   │   └── init_spec.rb
│   └── spec_helper.rb
├── templates
│   └── ustack.conf.erb
└── tests
    └── init.pp
```



