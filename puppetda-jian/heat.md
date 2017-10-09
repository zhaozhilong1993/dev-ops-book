# Heat

heat是OpenStack用作编排的一个组件。代码中的实现，主要还是通过调用各个组件的SDK来实现对应的资源的创建。在面向用户使用端，还是通过使用yaml文件来创建对应的资源。

heat的初体验：

```
# vim start_vm.yaml
heat_template_version: 2015-10-15
description: An example for use repeat to create resource
             Notice:repeat is available after the version of 2015-10-15
parameters:
  ports:
    type: comma_delimited_list
    label: ports
    default: "80,443,8080,22"
  protocols:
    type: comma_delimited_list
    label: protocols
    default: "tcp,udp"
resources:
  security_group:
    type: OS::Neutron::SecurityGroup
    properties:
      name: web_server_security_group
      description: a SecurityGroup create by repeat
      rules:
        repeat:
          template:
             protocol: protocol_type
             port_range_min: open_port
             port_range_max: open_port
          for_each:
             open_port: { get_param: ports }
             protocol_type: { get_param: protocols }

# heat stack-create -f start_vm.yaml my_first_stack
```

# 简单的Hot模板使用分析 {#HOT之HelloWorld-最简单的Hot模板}

```
heat_template_version: 2015-04-30
description: >
  Hello world HOT template that just defines a single server.
  Contains just base features to verify base HOT support.

resources:
  my_instance:
    type: OS::Nova::Server
    properties:
      image: cirros
      flavor: m1.tiny
      networks: [{network: private }]
```

# 最简单的Hot模板解析 {#HOT之HelloWorld-最简单的Hot模板解析}

## heat\_template\_version: {#HOT之HelloWorld-heat_template_version:}

每一个Hot模板都需要一个heat\_template\_version关键字来定义使用Hot文件的版本，我们可以在[_Heat template version_](http://docs.openstack.org/developer/heat/template_guide/hot_spec.html#hot-spec-template-version)中找到所有的Hot版本

## description: {#HOT之HelloWorld-description:}

对你模板的描述，这个选项是可选的，但是多写description是一个很好的习惯，这有助于其他读者阅读你的Hot模板，如果你的描述特别长，一行装不下啦，你可以这样写:

```
description: >
  Hello world HOT template that just defines a single server.
  Contains just base features to verify base HOT support.
```

## resources: {#HOT之HelloWorld-resources:}

resources选项是必须填的，而且必须包含1个预定义的资源，上面给出的例子中，一个nova节点的虚拟主机将会被建立。这个主机的镜像，偏好都会引用你的Hot模板中指定的。

* ### my\_instance {#HOT之HelloWorld-my_instance}

  这个是你的主机名，这个关键字也是必须的

* ### type {#HOT之HelloWorld-type}

  你需要引用的Heat的管理插件的类型，上面使用的是  
  OS::Nova::Server

  所有的type列表可以在[_Heat type list_](http://docs.openstack.org/developer/heat/template_guide/openstack.html)中查看

* ### properties {#HOT之HelloWorld-properties}

  每一个properties都对应一个type，每一个type的properties都是不一样的

# Hot的参数输入入口-parameters {#HOT之HelloWorld-Hot的参数输入入口-parameters}

输入参数可以定义在parameters中，在parameters中，用户可以输入诸如image\_id，flavor\_id，public\_network\_id等等不是固定的值，parameters可以让你的Hot模板更加容易复用。

## 使用parameters来修改HelloWorld模板 {#HOT之HelloWorld-使用parameters来修改HelloWorld模板}

现在我们使用parameters来重新定义上面的模板:

```
heat_template_version: 2013-05-23

description: >
  Hello world HOT template that just defines a single server.
  Contains just base features to verify base HOT support.
parameters:
  flavor:
    type: string
    description: Flavor for the server to be created
    default: m1.tiny
    constraints:
      - custom_constraint: nova.flavor
  image:
    type: string
    default: cirros
    description: Image ID or image name to use for the server
    constraints:
  network:
    type: string
    default:  private
    description: Image ID or image name to use for the server

resources:
  server:
    type: OS::Nova::Server
    properties:
      image: { get_param: image }
      flavor: { get_param: flavor }
      networks: [{network: {get_param: network} }]
```

这样一看我们的模板的复用性大大的增强了.

## Hot输出信息定义-outputs {#HOT之HelloWorld-在parameter输入中设定校验}

有时候我们希望在运行完我们的Hot之后给用户一些outputs，这时候我们就可以使用Hot里面的outputs选项。

