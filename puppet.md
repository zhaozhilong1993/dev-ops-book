# 1.简介和历史

Hiera是基于键值查询的数据配置工具，Hiera是一个可选工具，它的目标是：Hiera makes Puppet better by keeping site-specific data out of your manifests.  
它的出现使得代码逻辑和数据可以分离管理。  
在Puppet 2.x版本时代，Hiera作为一个独立的组件出现，若要使用则需要单独安装。在3.x版本之后，Hiera被整合到Puppet的代码中去。Hiera是Hierarchal单词的缩写，表明了其层次化数据格式的特点。

## 1.1实例说明

在使用hiera前，一个常见的manifests文件是这么编写的：

```
node "kermit.example.com" {
  class { "ntp":
    servers    => [ '0.us.pool.ntp.org iburst','1.us.pool.ntp.org iburst','2.us.pool.ntp.org iburst','3.us.pool.ntp.org iburst'],
    autoupdate => false,
    restrict   => [],
    enable     => true,
  }
}

node "grover.example.com" {
  class { "ntp":
    servers    => [ 'kermit.example.com','0.us.pool.ntp.org iburst','1.us.pool.ntp.org iburst','2.us.pool.ntp.org iburst'],
    autoupdate => true,
    restrict   => [],
    enable     => true,
  }
}

node "snuffie.example.com", "bigbird.example.com", "hooper.example.com" {
  class { "ntp":
    servers    => [ 'grover.example.com', 'kermit.example.com'],
    autoupdate => true,
    enable     => true,
  }
}
```

在使用了Hiera之后，manifests文件发生了如下变化：

```
node "kermit.example.com", "grover.example.com", "snuffie.example.com" {
  include ntp
  # or:
  # class { "ntp": }
}
```

所有的数据设置移到了hiera中:

```
---
ntp::restrict:
 -
ntp::autoupdate: false
ntp::enable: true
ntp::servers:
  - 0.us.pool.ntp.org iburst
  - 1.us.pool.ntp.org iburst
  - 2.us.pool.ntp.org iburst
  - 3.us.pool.ntp.org iburst
```

# 2. hiera.yaml配置文件

hiera.yaml是Hiera唯一的配置文件，它其中只有少数几个配置参数，但决定了Hiera不同的使用方式。

## 2.1文件路径

在puppet.conf中通过设置hiera\_config参数来设置hiera.yaml文件的路径，默认值为：$confdir/hiera.yaml  
\(注意Puppet 4.x以上时，默认值变更为$codedir/hiera.yaml\)

## 2.2参数详解

以下为hiera.yaml配置文件的默认值:

```
---
:backends: yaml
:yaml:
  # on *nix:
  :datadir: "/etc/puppetlabs/code/environments/%{environment}/hieradata"
  # on Windows:
  :datadir: "C:\ProgramData\PuppetLabs\code\environments\%{environment}\hieradata"
:hierarchy:
  - "nodes/%{::trusted.certname}"
  - "common"
:logger: console
:merge_behavior: native
:deep_merge_options: {}
```

对于这个路径 ： /etc/puppetlabs/code/environments/%{environment}/hieradata 它其实是一个目录，这个目录下面就需要的数据文件符合下面的格式:  
nodes/%{::trusted.certname}.yaml \[这个是动态路径，所以我就不全部写出来了\]  
common.yaml \[对应是嗯面的hierarchy里面的common\]  
上面的%{environment}其实是puppet的环境变量，一般都有默认值，可以使用下面的命令进程查看:

```
# puppet master --configprint environment
production
```

# 3.自动查找hiera数据源

Hiera是用来存储数据的地方，那么当Puppet代码中需要从hiera中读取某个数据时，我们可以在代码中使用hiera\(\)函数的方式，在hieradata中查找某个键值存储的数据，例如在hieradata文件test.yaml中定义了：

```
 foo: bar
```

那么，我可以在Puppet代码中获取foo这个键对应的值：

```
$text = hiera('foo')
notify { "$text": }
```

foo 这个键对应的值通过 hiera 函数获取到之后被保存在了 $text 变量中。  
除了使用 hiera\(\), hiera\_include\(\), hiera\_array\(\), hiera\_hash\(\) 等函数去 hieradata 中读取之外，Puppet 还会自动从 Hiera 中查找类参数，查找键为 myclass:parameter\_one（即 类名::参数名）。  
在定义一个 Puppet 类时，可以定义默认的参数值，例如下面的 myclass 类的参数 $parameter\_one 使用了默认值 "default text"：

```
class myclass ($parameter_one = "default text") {
  file {'/tmp/foo':
    ensure  => file,
    content => $parameter_one,
  }
}
```

当我们调用 myclass 这个类时，Puppet 遵循如下方式来设定 $parameter\_one 这个参数的值：  
如果在调用这个类的时候，显式的向其传递了参数值，那么 Puppet 使用显式传递的值作为参数的值。  
如果调用类时没有传递参数的值，那么 Puppet 会自动从 Hiera 中查询参数的值，查找时使用 :: 做为查找的键（例如上面的 myclass 类的 prarameter\_one 参数，查找键为 myclass::parameter\_one）  
如果方法 1 和 2 都没有获取到值，那么 Puppet 会使用类定义中参数的默认值作为参数的值（例如 myclass 中 prameter\_one 参数的默认值为 "default text"）  
如果 1 至 3 都没有获取到值，那么 Puppet 将会直接报错，代码的编译将被中断。  
上 面的方法 2 是 Puppet 最有趣的地方，因为 Puppet 会自动从 Hiera 中查找参数的值，我们可以在代码中使用 include 语句来调用一个类，不需要对其传递任何参数值，所有的参数传递都可以将参数值写到 Hiera 中，Puppet 会自动从 Hiera 中读取类的参数。例如，我想调用上面定义的 myclass 类，并且 $parameter\_one 的参数值为 "ustack"，参数的传递使用 Hiera 来完成。那么我需要在 Hiera 中写入下面的值：

```
myclass::parameter_one: 'ustack'
```

在代码中，调用myclass类：

```
include myclass
```

这里不用对myclass传递参数，myclass会自动读取Hiera中对parameter\_one定义的值，即$parameter\_one的值在调用时为'ustack'

[http://docs.puppetlabs.com/hiera/latest/](http://docs.puppetlabs.com/hiera/latest/)

