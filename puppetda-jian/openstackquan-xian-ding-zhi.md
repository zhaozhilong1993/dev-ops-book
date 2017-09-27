# OpenStack权限定制

user / tenant or project / role

token 去验证一个用户的身份

当你的一个user 对一个project 有对应的role的时候，你就有对应的

role的角色

查询对应用户的身份信息：

openstack user list

openstack project list

openstack role list

openstack role assignment list --projetc

&lt;

project

&gt;

--user

&lt;

user

&gt;

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



