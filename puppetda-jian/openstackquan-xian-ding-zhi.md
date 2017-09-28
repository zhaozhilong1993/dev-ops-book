# OpenStack权限定制

在keystone中，是通过调用API的权限来控制一个用户的权限。

一个user针对一个project有什么样的role。就有什么样的权限。所以说权限对应的是role,也就是我们说的角色。

基本操作：

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







token 去验证一个用户的身份

当你的一个user 对一个project 有对应的role的时候，你就有对应的

role的角色

查询对应用户的身份信息：

openstack user list

openstack project list

openstack role list



每个项目项目下面的policy.json：

控制了所有的role 对OpenStack的API的调用权限。

假设:

```
"volume:create": "", \# 表示所有用户都可以使用这个API

"volume:delete": "rule:admin\_or\_owner", \# 表示只有符合admin\_or\_owner 这个规则的用户，才能调这个API




\# rule对应上面的rule
```

"admin\_or\_owner":  "is\_admin:True or project\_id:%\(project\_id\)s", \# 表示的是这个用户是admin或者他提供的project\_id等于他所要操作的资源所在的project\_id

添加某个role的对应的权限：

```
"memeber": "role:test",

"volume:create": "rule:memeber or rule:admin\_or\_owner",
```



