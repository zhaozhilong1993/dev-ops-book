## Puppet多环境配置

面向实际的生产，我们的puppet需要管理很多个不同的环境。我们需要我们的puppet可以做到环境独立，可配置性高。那么我们需要怎么做呢？

对多个环境进行隔离：

我们需要对每一个环境进行代码分离：配置master

    [root@puppet-master puppet]# cat puppet.conf
    [main]
        # The Puppet log directory.
        # The default value is '$vardir/log'.
        logdir = /var/log/puppet

        # Where Puppet PID files are kept.
        # The default value is '$vardir/run'.
        rundir = /var/run/puppet

        # Where SSL certificates are kept.
        # The default value is '$confdir/ssl'.
        ssldir = $vardir/ssl

    [agent]
        # The file in which puppetd stores a list of the classes
        # associated with the retrieved configuratiion.  Can be loaded in
        # the separate ``puppet`` executable using the ``--loadclasses``
        # option.
        # The default value is '$confdir/classes.txt'.
        classfile = $vardir/classes.txt

        # Where puppetd caches the local configuration.  An
        # extension indicating the cache format is added automatically.
        # The default value is '$confdir/localconfig'.
        localconfig = $vardir/localconfig


    [master]
        environmentpath = $confdir/environments

之后建立实际的environments环境的目录结构：

```
[root@puppet-master puppet]# tree -L 4 environments/
environments/
└── production
    ├── environments
    ├── manifests
    │   └── cluster
    │       └── ustack.pp # 这个就是我们的node.pp的文件
    └── modules
        └── ustack-openstack
            ├── Gemfile
            ├── manifests
            ├── metadata.json
            ├── Rakefile
            ├── README.md
            ├── spec
            ├── templates
            └── tests
```

我们需要告诉puppet这个环境的配置：

```
[root@puppet-master puppet]# cat environments/production/environments
modulepath=/etc/puppet/environments/production/modules
manifest=/etc/puppet/environments/production/manifests/cluster
```

其实就是指定了module和mainfest的路径。

之后把之前写好的module复制过来：

```
[root@puppet-master puppet]# cp -r /etc/puppet/modules/ustack-openstack/ /etc/puppet/environments/production/modules/
```

再定义我们的node文件，这里我把node文件改名成ustack.pp其实名字是什么都行，都可以读得到：

```
[root@puppet-master puppet]# cat environments/production/manifests/cluster/ustack.pp
node "puppet-agent.openstacklocal" {   
   # 注意这里，因为用了多模块的配置，你这里需要重新定义你的module的名字，如果用openstack这个名字是找不到这个module的
   include ustack-openstack::openstack 
}
```

之后修改我们的module的init.pp文件：

```
[root@puppet-master puppet]# cat environments/production/modules/ustack-openstack/manifests/init.pp 
class ustack-openstack::openstack(
  $test_hiera_domain = "test",
){

  notify { "$test_hiera_domain": }

}
```



