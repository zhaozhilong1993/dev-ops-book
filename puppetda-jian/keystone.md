# Keystone

## OpenStack权限定制

在keystone中，是通过调用API的权限来控制一个用户的权限。

一个user针对一个project有什么样的role。就有什么样的权限。所以说权限对应的是role,也就是我们说的角色。

```
我们可以看到我们一个项目(project下面会有多个user)

ustack1(admin) ------|
                     |-- ustack (project)
ustack2(member) -----|
```

Keystone基本操作：

```
创建用户
# openstack user create <user_name>

创建项目
# openstack project create <project_name>

创建角色
# openstack role create <role_name>

绑定用户角色
# openstack user role add <user_name> <role_name>

列出某个项目下面某个用户的角色列表
openstack role assignment list --projetc <project_name> --user <user_name>
```

Keystone的token

Keystone中使用token 去验证一个用户的身份，就是一个token就是一个用户的一个身份证明，就跟古代的令牌一个样。

我们可以看到，我们在使用每一个API请求的时候都会大带上对应的token。如：

```
nova --debug list
```

我们会看到最后调用nova的API的时候都会带上: X-Auth-Token的httpd的头部标志。我们可以使用curl命令来尝试使用这个token。

```
获取token
# openstack token issue


# curl -H "X-Auth-Token: " http://xxx:xxx
```

到目前为止，我们知道了，使用API的时候需要加上用户的token,然后token对应一个用户的用户身份，一个用户的用户身份又对应了：user -&gt; role -&gt; project的这样的关系。所以说对一个API有没有调用的权限，要看的还是用户的角色（role）。 那么，我们肯定需要有一个文件来记录对应的role能不能调用对应的API。这个文件就是每一个OpenStack项目里面的policy.json文件。policy.json控制了所有的role 对OpenStack的API的调用权限。

以cinder项目的policy.json为例：

```
# 表示所有用户都可以使用这个API
"volume:create": "", 

#表示只有符合admin_or_owner这个规则的，才能调这个API
"volume:delete": "rule:admin_or_owner",
```

我们在每一个项目里面，我们经常会去自定义一个我们自己的一个用户角色，那我们怎么去定义这个用户有什么样的用户权限呢？当然是修改policy.json文件咯。

```
# openstack user create ustack
# openstack user set  --password openstack ustack
# openstack project create ustack
# openstack role create visitor
# openstack role add --user ustack --project ustack visitor
```

接着我们为这个用户生成一个keystone的环境变量文件：

```
# vim /root/keystonerc_ustack
unset OS_SERVICE_TOKEN
    export OS_USERNAME=ustack
    export OS_PASSWORD=openstack
    export OS_AUTH_URL=http://192.168.20.27:5000/v2.0
    export PS1='[\u@\h \W(keystone_ustack)]\$ '

export OS_TENANT_NAME=ustack
export OS_REGION_NAME=RegionOne
```

后面尝试这个keystone\_ustack文件：

```
# source keystonerc_ustack
# openstack token issue
+------------+----------------------------------+
| Field      | Value                            |
+------------+----------------------------------+
| expires    | 2017-10-08 05:31:19+00:00        |
| id         | b801f63b5a0e44f4b3d2b06fa367eb13 |
| project_id | 0994660fae9d4f7d8debe826666afc70 |
| user_id    | 8dd2237de85e4922aa5e720fb0d42cac |
+------------+----------------------------------+
```

我们接下来用这个用户创建一个磁盘：

```
# cinder create 1 --name volume
+------------------------------+--------------------------------------+
| Property                     | Value                                |
+------------------------------+--------------------------------------+
| attachments                  | []                                   |
| availability_zone            | nova                                 |
| bootable                     | false                                |
| consistencygroup_id          | None                                 |
| created_at                   | 2017-10-08T04:32:25.000000           |
| description                  | None                                 |
| encrypted                    | False                                |
| id                           | 93d6ab49-3683-4980-999b-fbbd7d65f23c |
| metadata                     | {}                                   |
| multiattach                  | False                                |
| name                         | volume                               |
| os-vol-tenant-attr:tenant_id | 0994660fae9d4f7d8debe826666afc70     |
| replication_status           | disabled                             |
| size                         | 1                                    |
| snapshot_id                  | None                                 |
| source_volid                 | None                                 |
| status                       | creating                             |
| updated_at                   | None                                 |
| user_id                      | 8dd2237de85e4922aa5e720fb0d42cac     |
| volume_type                  | iscsi                                |
+------------------------------+--------------------------------------+
```

我们会发现

之后修改policy.json文件。

```
# vim /etc/cinder/policy.json
```



