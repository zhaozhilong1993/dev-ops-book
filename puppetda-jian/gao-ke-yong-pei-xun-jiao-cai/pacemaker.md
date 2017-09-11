# Pacemaker

节点信息：

```
192.168.101.11 devstack-1 
192.168.101.12 devstack-2
```

安装包：

```
# yum install pcs pacemaker -y
```

启动pcs:

```
# systemctl start pcsd
# systemctl enable pcsd
```

做身份验证：

```
# ssh-keygen
# ssh-copy-id root@192.168.101.11
# ssh-copy-id root@192.168.101.12
```

pcs安装完之后默认会有一个hacluster用户，我们要用这个用户管理pcs集群，所以需要给他一个密码：

```
# passwd hacluster
```

之后验证节点：

```
# pcs cluster auth devstack-1
# pcs cluster auth devstack-2
```

之后初始化集群，并启动集群：

```
 # pcs cluster setup --start --name my_hacluster devstack-1 devstack-2 --force
```

最后查看集群是否正常启动：

```
# pcs cluster status
```

设置集群自启动：

```
# pcs cluster enable –all
```



