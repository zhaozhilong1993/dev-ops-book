Puppet环境准备

节点

Master: 172.16.0.33

Agent: 172.16.0.36

环境准备：

配置hosts解析：

172.16.0.36 puppet-agent.openstacklocal

172.16.0.33 puppet-master.openstacklocal

这步一定要做，因为puppet的ca证书的索引都是以主机名为标示的。

