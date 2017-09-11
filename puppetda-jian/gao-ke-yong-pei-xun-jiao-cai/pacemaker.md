# Pacemaker

## PCS安装

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

## PCS高可用配置

```
在node1和node2上安装httpd ，确认httpd开机被禁用

# systemctl status httpd.service；
配置httpd监控页面（貌似不配置也可以通过systemd监控），分别在node1和node2上执行

# cat > /etc/httpd/conf.d/status.conf << EOF
SetHandler server-status
Order deny,allow
Deny from all
Allow from localhost
EOF
首先我们为Apache创建一个主页。在centos上面默认的Apache docroot是/var/www/html,所以我们在这个目录下面建立一个主页。

node1节点修改如下：

[root@node1 ~]# cat <<-END >/var/www/html/index.html
<html>
<body>Hello node1</body>
</html>
 
END
node2节点修改如下：

[root@node2 ~]# cat <<-END >/var/www/html/index.html
<html>
<body>Hello node2</body>
</html>
 
END
下面语句是将httpd作为资源添加到集群中：

#pcs resource create WEB apache configfile="/etc/httpd/conf/httpd.conf" statusurl="http://127.0.0.1/server-status"
```





