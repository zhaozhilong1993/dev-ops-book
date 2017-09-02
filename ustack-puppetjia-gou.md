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

之后建立实际的environments环境：

```
[root@puppet-master puppet]# tree -L 4 environments/
environments/
└── production
    ├── environments
    ├── manifests
    │   └── cluster
    │       └── hf.pp
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



