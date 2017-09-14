# zabbix

zabbix是现在很流行的监控组件。相比之前的cacti + ganglia + nagios这三个大套件，zabbix相对来说功能更全，操作，部署  
更为简单.

## 安装zabbix

```
# 安装zabbix的源
$ rpm -ivh http://repo.zabbix.com/zabbix/3.0/rhel/7/x86_64/zabbix-release-3.0-1.el7.noarch.rpm

# 更新环境
$ yum update -y

# 安装zabbix
$ yum install -y zabbix-server-mysql.x86_64 zabbix-web-mysql.noarch zabbix-get.x86_64 -y mariadb-server
```

初始化数据库

```
$ systemctl start mariadb
$ mysql
> create database zabbix;
> GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost' \
  IDENTIFIED BY 'zabbix';
> GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'%' \
  IDENTIFIED BY 'zabbix';

# 同步数据库
$ /usr/bin/zcat /usr/share/doc/zabbix-server-mysql-3.0.5／create.sql.gz | mysql -uzabbix -pzabbix zabbix
```

之后编辑配置文件

```
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

之后配置zabbix的httpd模块

```
$ vim /etc/httpd/conf.d/zabbix.conf

Alias /zabbix /usr/share/zabbix

<Directory “/usr/share/zabbix”>

    Options FollowSymLinks

    AllowOverride None

    Require all granted

    <IfModule mod_php5.c>

        php_value max_execution_time 300

        php_value memory_limit 128M

        php_value post_max_size 16M

        php_value upload_max_filesize 2M

        php_value max_input_time 300

        php_value always_populate_raw_post_data -1

       php_value date.timezone Asia/Chongqing

     </IfModule>

</Directory>
```

启动zabbix

```
systemctl enable zabbix-server

systemctl start zabbix-server
```

```
systemctl restart httpd
```

## zabbix的面板介绍

## 添加监控主机

## 配置你的第一个监控项

## 添加自定的监控脚本

## 配置zabbix-proxy

## 配置报警



