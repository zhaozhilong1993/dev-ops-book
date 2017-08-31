# Puppet的目录树 {#puppet基本结构-puppet的目录树}

我们在了解怎么使用puppet的时候，一定要了解puppet的目录结构，因为puppet是一种管理工具，我们就必须知道它什么文件写什么东西，什么目录做什么事情。

我们知道puppet可以在多节点部署上面有很好的管理效用，既然是多节点，那肯定需要有一个文件专门定义了你所有的node节点的地址的位置，接着我们需要在一个node节点上面安装很多的组件，那么对组件的管理又应该在一个文件里面表示出来。

我先给一个文件目录的大概结构，刚刚安装完puppet的是没有这么多文件的，我为了更好的讲解puppet所以添加了一些文件

```
├── auth.conf # 认证文件
├── fileserver.conf 
├── manifests # 主要的模块文件
│   ├── modules.pp
│   ├── node.pp
│   └── site.pp
├── modules # 模块集合
│   └── ustack-openstack
│   
└── puppet.conf # 配置文件
```

首先是manifests目录

这个是puppet最重要的目录之一，这个相当于你的puppet的入口，我们需要在这里面定义，你的node节点的位置，你的node需要跑什么组件等等

我们来看看下面的3个文件

最先执行的是site.pp文件:

它引入了其他的两个文件，一个是modules.pp文件（定义了所有的模块），另一个是node.pp文件（定义了所有的节点）

**site.pp**

```
import "modules.pp"
import "node.pp"

node default {
}
```

再看看modules.pp文件

这个里面定义的import的模块都是在modules下面定义好的，我们可以在上面的我列出的puppet的目录结构中看到:

**modules.pp**

```
import  "ustack-openstack"
```

再看看node.pp文件

这个很直观，我在packstack-storage-2.novalocal节点上安装了heat和mymodule模块

**node.pp**

```
node "packstack-storage-2.novalocal" {
    include openstack
}
```

我们已经看完manifests模块下面的内容,接下来modules的内容

**modules**

在下面的我除去了大部分的内容,剩下的就是我们的manifests文件，这个也是modules文件的入口,当然，主程序的入口是init.pp，通过控制init.pp文件，来控制其他的小的模块的安装.puppet内置了一些命令可以帮助我们生成我们的新的模块：

```
# 注意模块的名字一定要是<name>-<name>的形式，中间一定要有"-"
[root@puppet-master modules]# puppet module generate ustack-openstack

#之后我们就可以在/etc/puppet/modules/下面看到我们的文件
[root@puppet-master modules]# ls /etc/puppet/modules/
 ustack-openstack

# 查看基本的目录结构
[root@puppet-master modules]# tree -L 3 /etc/puppet/modules/ustack-openstack/
/etc/puppet/modules/ustack-openstack/
├── Gemfile
├── manifests
│   └── init.pp
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

到这里，你应该对整个的puppet的模块的目录结构有基本的认识。对于puppet种一些函数的写法，下节将会讲到.

