# zabbix

zabbix是现在很流行的监控组件。相比之前的cacti + ganglia + nagios这三个大套件，zabbix相对来说功能更全，操作，部署  
更为简单.

## 安装zabbix

```
$ yum install -y zabbix mariadb-server

$ systemctl start mariadb
$ mysql
> create database zabbix;
> GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost' \
  IDENTIFIED BY 'zabbix';
> GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'%' \
  IDENTIFIED BY 'zabbix';
  
$ 

$ vim /etc/zabbix/zabbix_server.conf
...
LogFile=/var/log/zabbix/zabbix_server.log
LogFileSize=0
PidFile=/var/run/zabbix/zabbix_server.pid
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=5fce1b3e34b520afeffb37ce08c7cd66
DBPort=3306
StartPollers=50
SNMPTrapperFile=/var/log/snmptrap/snmptrap.log
ListenIP=0.0.0.0
CacheSize=1024M
Timeout=4
AlertScriptsPath=/usr/lib/zabbix/alertscripts
ExternalScripts=/usr/lib/zabbix/externalscripts
LogSlowQueries=3000
...
```

## zabbix的面板介绍

## 添加监控主机

## 配置你的第一个监控项

## 添加自定的监控脚本

## 配置zabbix-proxy

## 配置报警



