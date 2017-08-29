让Puppet运行在httpd之下

官方推介，在正式的生产环境中，把puppet-master运行在httpd的代理之下。所以，我们在这边也尝试把puppet-master运行在httpd之下。

```
yum install -y httpd httpd-devel mod_ssl \
ruby-devel rubygems gcc-c++ curl-devel zlib-devel make automake  openssl-devel
```



