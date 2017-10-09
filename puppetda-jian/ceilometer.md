Ceilometer

# **简介** {#Ceilometer基本介绍-简介}

ceilometer是openstack社区的一个监控组件，项目开始的时间是2012年，在2013年的G版中完成孵化，正式加入openstack的家族。ceilometer在发展的过程中经历了很大的变化，这些变化我们或许需要了解一下：

* 初期：
  ceilometer刚刚提出的时候只是为了获取保存计量的信息来支持用户 收费的问题

* 中期：
  随着项目的发展，openstack社区里面发现ceilometer需要保存很多种不同的测量值，所以在发展的过程中，ceilometer有了第二个目标，成为openstack里一个标准的获取保存测量值得框架，就是说在openstack的计量中ceilometer建立了一个标准，再后来由于编排项目Heat的提出，ceilometer又增加了利用现有的计量数据进行报警的功能,这个阶段报警功能还是集成在ceilometer里面的，由ceilometer-alarm进程进行管理
* 后期：
  发展到现在，openstack社区发现报警功能的需求渐渐的上升，为了更好的为openstack服务，ceilometer-alarm这个管理报警的服务就独立成一个openstack项目了，名为:Aodh。同时在这个阶段中，openstack社区工作者发现ceilometer保存的计量数据越来越大，原本的mongo或者mysql作为存储后端已经不太合适，有很多性能上的问题，所以社区又把另一个专门做计量数据存储的第三方组件gnocchi集成到ceilometer中了，作为ceilometer一个可选的存储计量数据的方案。

# **架构体系** {#Ceilometer基本介绍-架构体系}

## **配置文件介绍** {#Ceilometer基本介绍-配置文件介绍}

这里只针对主要用到的配置文件的功能进行介绍，具体的配置，我需要放到其他地方进行讲解，敬请期待。

* ceilometer.conf ：
  主配置文件，你可以在配置文件里面指定使用的存储计量数据的数据库后端，keystone服务器地址，进程启动数等等
* pipeline.yaml ：
  监控资源的配置文件，这个很好理解，你肯定不想什么资源都监控，所以你想监控什么资源，多久监控一次都是在这个文件里面指定的。
* event\_pipeline.yaml
  notification对event的监控策略文件，在ceilometer监控的数据中可以分为sample数据和event数据

## **相关概念介绍** {#Ceilometer基本介绍-相关概念介绍}

* ### **sample数据** {#Ceilometer基本介绍-sample数据}

  * sample数据指的是一些监控数据，包括内存使用率，cpu使用率等等东西，这类数据并不是很重要，即使丢掉一两个也并不是很糟糕
* ### **event数据** {#Ceilometer基本介绍-event数据}

  * event数据指的是一些其他组件\(包括ceilometer\)的动作消息，比如创建了一台虚拟主机，创建了一个公网IP等等，这类数据的价值较高，往往不能遗失

## **主进程介绍** {#Ceilometer基本介绍-主进程介绍}

ceilometer在现在一共有ceilometer-polling，ceilometer-agent-notification，ceilometer-collector这3个主要进程，而ceilometer-polling进程下面又会有3种启动类型:

可以想象，在做计量的时候一般可以分为主动获取计量数据和被动获取计量数据的情况：

被动获取计量数据的情况:

* ceilometer-agent-notification
  被动获取数据是指，其他组件把一些相应的动作以通知消息\(Notification Message\)的形式发送到消息总线\(Notification Bus\)，而ceilometer-agent-notification进程就会获取这些组件发送的信息，根据pipeline.yaml里面的配置项对提取相应的监控数据。

主动获取计量数据的情况:

* ceilometer-polling
  首先需要讲解一下这个进程，这个进程有3种启动类型: compute ，central，ipmi。这3个进程启动的节点是有规定的，比如compute应该启动在nova的compute节点（就是虚拟主机启动的节点）。
  ceilometer-polling会定期主动的通过各种API的调用去获取每一台虚拟主机\(比如cpu，内存的使用情况\)。
  * compute类型

    ```

    ```

  compute的类型必须启动在nova的计算节点上，因为ceilometer会调用虚拟化的虚拟监控器\(Hypervisor\)，API获取虚拟主机的运行状态，不然ceilometer-polling是监控不到虚拟主机的计量数据的。

  * central类型

    ```

    ```

  central可以被部署在任何节点上，它主要用来和远程的各种不同的虚拟主机和服务进行通信，获取不同的测量值

最后接收计量数据和存储数据：

* ceilometer-collector
  ceilometer通过上面的方法获取计量数据之后，之后会对数据进行一些转换之后存储在数据库中。

## **基本架构介绍** {#Ceilometer基本介绍-基本架构介绍}

![](/assets/ceilometer.png)

* Collector启动时，启动一个事件监听器，contral类型的polling进程去其他组件上获取数据后放入ceilometer event bus里面，collector监听到之后就存入数据库
* 运行在nova节点上的compute类型的polling进程获取到虚拟主机的数据后也会放入ceilometer event bus里面，collector监听到之后就存入数据库
* 其他组件可以通过对应的API调用数据库里面的计量数据
 



