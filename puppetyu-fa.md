# Puppet基础语法

在上节中，我们介绍了Puppet的基本文件结构，结下来我们就要对，我们在上面自定义的ustack-openstack这个Puppet模块写一些简单的例子，来加深我们对Puppet的理解。

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



