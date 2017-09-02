# Puppet配置hiera数据源

## 1.简介和历史

Hiera是基于键值查询的数据配置工具，Hiera是一个可选工具，它的目标是：Hiera makes Puppet better by keeping site-specific data out of your manifests.  
它的出现使得代码逻辑和数据可以分离管理。  
在Puppet 2.x版本时代，Hiera作为一个独立的组件出现，若要使用则需要单独安装。在3.x版本之后，Hiera被整合到Puppet的代码中去。Hiera是Hierarchal单词的缩写，表明了其层次化数据格式的特点。

### 1.1实例说明

在使用hiera前，一个常见的manifests的init.pp文件是这么编写的：

```
class openstack(
  $enable_httpd = false,
){

   if $enable_httpd {
     package { 'httpd':}
   }
}
```

如果有多个Puppet环境都调用了这个基础函数，有些环境希望打开httpd，有些则不希望打开httpd，我们怎么办呢？如果我们建立多套代码库，那么不利于后期维护，所以Puppet社区就想了一个方法，把这些参数的输入，全部单独独立出来，代码逻辑不变，但是你在外部输入了不同的变量，就可以走不通的代码逻辑。就是说，我希望有一个文件，可以根据不同的环境设定我们的enable\_httpd的值。这就是Puppet的hiera的数据配置的基础功能。

所有的数据设置移到了hiera中:

```
vsftpd::enable: true
```

这里就定义了hiera的变量的值。

## 2. hiera.yaml配置文件

hiera.yaml是Hiera唯一的配置文件，它其中只有少数几个配置参数，但决定了Hiera不同的使用方式。

### 2.1文件路径

在puppet.conf中通过设置hiera\_config参数来设置hiera.yaml文件的路径，默认值为：$confdir/hiera.yaml  
\(注意Puppet 4.x以上时，默认值变更为$codedir/hiera.yaml\)，查看本地的hiera的路径：

```
[root@puppet-master puppet]# puppet master --configprint hiera_config
/etc/puppet/hiera.yaml
```

接下来我们就自己建立一个hiera.yaml文件：

```
[root@puppet-master puppet]# mdkir -p /etc/puppet/hieradata
[root@puppet-master puppet]# cat hiera.yaml
---
:backends:
  - yaml
:hierarchy:
  - "global/base"
:yaml:
   :datadir: /etc/puppet/hieradata
```

上面的hierarchy部分定义了,我们将会在/etc/puppet/hieradata这个目录下面找哪个文件。我们接下来就需要去建立这个base文件，注意是，global／base.yaml这个文件，因为在backends中定义的文件后缀是yaml。

新建base.yaml文件：

```
[root@puppet-master puppet]# cat /etc/puppet/hieradata/openstacklocal/base.yaml
enable_httpd: true
```

之后在module中设置：

```
class openstack(
  $enable_httpd = false,
){

$enable_httpd = hiera('enable_httpd')
notify { "$enable_httpd": }

}
```

之后在agent中运行测试：

```
[root@puppet-agent ~]# puppet agent   -t --server puppet-master.openstacklocal
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppet-agent.openstacklocal
Info: Applying configuration version '1504337788'
Notice: true
Notice: /Stage[main]/Openstack/Notify[true]/message: defined 'message' as 'true'
Notice: Finished catalog run in 0.03 seconds
```

我们发现已经可以通过

```
$text = hiera('enable_httpd')
```



参考资料：

[http://docs.puppetlabs.com/hiera/latest/](http://docs.puppetlabs.com/hiera/latest/)

