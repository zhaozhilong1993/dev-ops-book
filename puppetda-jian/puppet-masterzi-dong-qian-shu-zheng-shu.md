# puppet-master自动签署证书

在自动化的流程中我们并不希望某一个步骤需要人工干预，所以说像上一篇文章中的签署puppet-agent的证书的操作，我们希望由master自己完成，所以我们需要配置puppet-master开启自动签署证书的功能。

修改puppet-master的配置文件

```
[root@puppet-master puppet]# vim /etc/puppet/puppet.conf
[main]
...    
    ## 添加这行
    autosign = $confdir/autosign.conf { mode = 664 }
```

创建签署证书的文件

```
[root@puppet-master puppet]# echo "*" /etc/puppet/autosign.conf
[root@puppet-master puppet]# cat /etc/puppet/autosign.conf
*
```

### 测试puppet-master自动签署的功能

删除master上的认证证书

```
[root@puppet-master puppet]# rm -f /var/lib/puppet/ssl/*
[root@puppet-master puppet]# systemctl restart puppetmaster

# 查看证书
[root@puppet-master puppet]# puppet cert list -a
```

删除客户段的证书，然后用新的证书注册

```
# 删除证书
[root@puppet-agent ~]# rm -f /var/lib/puppet/ssl

# 新生成一个证书
[root@puppet-agent ~]# puppet agent -t --server puppet-master.openstacklocal
Info: Caching certificate for puppet-agent.openstacklocal
Info: Caching certificate_revocation_list for ca
Info: Caching certificate for puppet-agent.openstacklocal
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for puppet-agent.openstacklocal
Info: Applying configuration version '1503996686'
Notice: Finished catalog run in 0.02 seconds
```

在master上查看证书，发现自己注册了

```
[root@puppet-master ~]# puppet cert list -a
+ "puppet-agent.openstacklocal"  (SHA256) 29:20:85:63:E0:B7:1A:80:01:99:2F:31:F3:C4:07:65:4F:74:C2:A8:11:7B:31:FF:F5:FC:01:E8:8F:12:A6:B2
```



