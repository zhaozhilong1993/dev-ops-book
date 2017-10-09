# Playbooks的基础语法

1\). ansible目录结构

  


.

├── ansible.cfg

├── group\_vars

│   ├── all

│   └── webserver

├── hosts

├── host\_vars

├── playbook

│   ├── main.retry

│   └── main.yml

└── roles

    └── dns

        ├── files

        ├── README.md

        ├── tasks

        │   └── main.yml

        └── templates

            └── foo.cfg.j2

  


默认的目录下面hosts定义了主机列表,role是每一个task的角色列表。

  


2\). hosts的主机定义

  


\[webserver\]

127.0.0.1

  


定义了webserver的group,这样的话，默认情况下，在hosts当前目录下面的group\_vars和host\_vars下面名为webserver的值都会被读到。

  


  


2.1\) 拓展

所以说针对上面的目录结构，我们可以做个拓展，针对大规模集群的角色定义：

  


\[webserver\]

10.0.0.1

  


\[databaserver\]

10.0.0.2

  


\[region\_name:children\]

webserver

databaserver

  


这样一个集群就是一个group，你只要在group\_vars下面定义一个名为region\_name的文件：

  


remote\_install\_path: /mnt

  


这样就可以区分出不同的region读取不用的变量了。



## 生成playbook项目

我们在开始一个playbook项目的时候，可以参考这个[【ansible模版】](https://github.com/ansible/ansible-examples.git)

怎么用shell脚本

条件判断

循环

