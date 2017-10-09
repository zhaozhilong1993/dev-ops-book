# Gnocchi {#Gnocchi基本介绍-基本介绍}

## 项目背景 {#Gnocchi基本介绍-项目背景}

在ceilometer项目开始时，社区的宗旨是保存ceilometer收到的时序性的监控数据。起初，社区并不非常清楚这些时序的数据要怎么处理，怎么调用，以至于ceilometer在设计之初就对数据处理这部分就非常具有可拓展性。然而在长久的使用的过程中，社区发现在没有使用对应的时序数据处理的后台数据库时，ceilometer在存储了大量的之后，性能会变得非常差（检索和排序的时候的性能都非常差，太慢了）。虽然，这个问题在香港的summit上呼声也很高，虽然大家讨论了很久，但是社区也没有一个很强有力的解决方案，最后，考虑到ceilometer日趋增长的数据量，原来Gnocchi的开发团队就希望可以整合Gnocchi到openstack中，尝试去解决这个问题。

## 项目介绍 {#Gnocchi基本介绍-项目介绍}

Gnocchi是一个时序数据库，官网地址:[http://gnocchi.xyz/](http://gnocchi.xyz/)，项目开始于2014年，是作为ceilometer的一个子项目加入到openstack社区的。旨在处理ceilometer存储的大量的监控数据，作为一个时序数据库，Gnocchi在计费，监控报警方面的项目都有大量的采用。



# 基本认识 {#Gnocchi基本介绍-基本认识}

## Gnocchi数据怎么存储 {#Gnocchi基本介绍-Gnocchi数据怎么存储}

Gnocchi是把索引存在了其他数据库里面，然后针对实际的操作数据存储在自己的数据库中，自己的数据库中又以:

resource -&gt; metric -&gt; measure的数据分离形式来存储实际的监控数据，一般提取数据的时候只需要从metric层开始提取。

## What is resource? {#Gnocchi基本介绍-Whatisresource?}

resource是gnocchi对openstack监控数据的一个大体的划分,有磁盘的数据，镜像的数据，网络的数据，接口的数据等等.通过下面的命令可以看到实际使用的resource的名字。

```

```

## What is metric? {#Gnocchi基本介绍-Whatismetric?}

metric是gnocchi对数据的第二层划分，归属与resource，如resource为image的项目下面，就可能有image.download,image.serve,image.size等等，我们可以通过下面的命令看到一个resource下面的所有的metric

```

```

## What is measures? {#Gnocchi基本介绍-Whatismeasures?}

measures是gnocchi对数据的第三层划分也是最后一层，归属于metric，保存了实际的监控数据，可以通过下面的命令去查看某一个metric的所有数据:

```

```

## Gnocchi的数据聚合规则 {#Gnocchi基本介绍-Gnocchi的数据聚合规则}

还记得 ceilometer的pipeline.yaml文件里面有一个设置采样时间的选项么？gnocchi的policy里面也有一个采集粒度的选项，默认是这样算的：

```

```



## Gnocchi的存储引擎 {#Gnocchi基本介绍-Gnocchi的存储引擎}

就像mysql一样，Gnocchi也有自己的存储引擎：

* File
* Swift
* Ceph \(preferred\)
* InfluxDB \(experimental\)

前面3种都不是专业的的处理时序序列的存储引擎，而且都依赖一个名为_Carbonara_的中间件来实现存储。InfluxDB虽然是一个专业的处理时序数据的数据库，但是由于InfluxDB本身就是一个开源软件，到现在虽然集成上Gnocchi了，但是还是有很多bug。

### So,我们要怎么选存储引擎呢? {#Gnocchi基本介绍-So,我们要怎么选存储引擎呢?}

下面是官方的解释,长话短说，Gnocchi在存储数据的时候采用了压缩存储的技术，所以计算方式可以大致参照下面的计算方法:

```

```

 所以说，如果没有什么特殊的需求，正常的file引擎就可以满足你的需求。注意噢，上面只是一个监控项的监控数据，如果你要监控N个metrics（就是对应ceilometer的meter）,就要对应乘上N。但是无论你监控多少个监控项，挂个1T的数据盘也够保存你一整年的监控数据了。

