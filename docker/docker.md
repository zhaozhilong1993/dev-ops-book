# Docker基础知识

Docker作为新兴的技术，在互联网发展过程中有很大的推动作用，废话不多说，我们来用下docker
Docker的使用就像OpenStack一样，不过Docker没有那么复杂的网络模式，没有那么多的组件。

## 下载docker

```
# yum install docker -y
```

## 运行docker

```
# systemctl start docker
```

## 使用docker

```
docker pull centos:lastest
docker run -it centos bash
```

## Dockerfile
在进行容器化改造，或者说我们需要定制化我们的镜像的时候，我们就需要一个Dockerfile来告诉docker这个
镜像要怎么做，做什么。

### 定制化镜像
```
$ mkdir docker
$ cd docker

# 为了下面的演示用
$ cp /etc/hosts .
$ vim Dockerfile
FROM daocloud.io/centos:latest

# COPY是把你本机的文件拷贝到docker里面的命令
COPY /etc/hosts /etc/hosts

# RUN是在你构造镜像的过程中运行的命令
RUN cp /etc/hosts /etc/hosts.back

$ docker build . -t my_first_image
```
我们在我们的镜像构建好了之后，可以使用docker image这个命令查看
```
➜  docker git:(master) ✗ docker run -it my_first_imgae bash
[root@b750fafd441b /]# cat /etc/hosts
127.0.0.1    localhost
::1    localhost ip6-localhost ip6-loopback
fe00::0    ip6-localnet
ff00::0    ip6-mcastprefix
ff02::1    ip6-allnodes
ff02::2    ip6-allrouters
172.17.0.2    b750fafd441b
```

### 容器化改造
假设我有一个web程序，需要做容器化改造，我们要怎么做呢？
假设我有一个简单的web程序，地址是:
```
➜  docker git:(master) ✗ cat start.sh
python -m SimpleHTTPServer 8080

➜  docker git:(master) ✗ cat Dockerfile
FROM daocloud.io/centos:latest

# COPY是把你本机的文件拷贝到docker里面的命令
COPY hosts /etc/hosts
COPY start.sh /opt/start.sh

# RUN是在你构造镜像的过程中运行的命令
RUN cp /etc/hosts /etc/hosts.back
RUN chmod +x /opt/start.sh

# CMD 是你启动容器后执行的命令
CMD /opt/start.sh
```
启动容器验证服务
```
# 注意这里没有在命令后面加bash
# 如果你在后面加上base，如：docker run -it my_first_imgae bash
# 会把上面的CMD命令，也就是/opt/start.sh覆盖掉，这样/opt/start.sh命令就不能执行了
➜  docker git:(master) ✗ docker run -it my_first_imgae
Serving HTTP on 0.0.0.0 port 8080 ...

# 但是我们尝试去连接8080端口，会发现是不通的
➜  ~ curl 127.0.0.1:8080
curl: (7) Failed to connect to 127.0.0.1 port 8080: Connection refused
```
我们会发现这里我们的8080端口是不通的，因为我们没有指定端口映射:
```
➜  docker git:(master) ✗ docker run -it -p 8080:8080 my_first_imgae
Serving HTTP on 0.0.0.0 port 8080 ...
```
我们尝试连接
```
➜  ~ curl 127.0.0.1:8080
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 3.2 Final//EN"><html>
<title>Directory listing for /</title>
<body>
<h2>Directory listing for /</h2>
<hr>
<ul>
<li><a href=".dockerenv">.dockerenv</a>
<li><a href="anaconda-post.log">anaconda-post.log</a>
<li><a href="bin/">bin@</a>
<li><a href="dev/">dev/</a>
<li><a href="etc/">etc/</a>
<li><a href="home/">home/</a>
<li><a href="lib/">lib@</a>
<li><a href="lib64/">lib64@</a>
<li><a href="lost%2Bfound/">lost+found/</a>
<li><a href="media/">media/</a>
<li><a href="mnt/">mnt/</a>
<li><a href="opt/">opt/</a>
<li><a href="proc/">proc/</a>
<li><a href="root/">root/</a>
<li><a href="run/">run/</a>
<li><a href="sbin/">sbin@</a>
<li><a href="srv/">srv/</a>
<li><a href="sys/">sys/</a>
<li><a href="tmp/">tmp/</a>
<li><a href="usr/">usr/</a>
<li><a href="var/">var/</a>
</ul>
<hr>
</body>
</html>

# 可以看到可以连接上了
➜  docker git:(master) ✗ docker run -it -p 8080:8080 my_first_imgae
Serving HTTP on 0.0.0.0 port 8080 ...
172.17.0.1 - - [13/Sep/2017 14:38:38] "GET / HTTP/1.1" 200 -
```

## docker的网络模式
其实docker单机的网络模式很简单，就两种桥接和host模式。在集群使用中可能会有macvlan或者calical的网络模式.
```
# 不加--net host默认就使用了桥接的模式
$ docker run -it  my_first_imgae  bash

# 加了--net host就是使用host模式
$ docker run -it --net host my_first_image bash
```
宿主机的本地host解析在桥接模式下是无法传到docker里面的，除非你换成host模式.

## supervisor
docker是一个主进程，我们每启动的一个容器都是这个docker主进程的子进程。
我们知道在docker里面是没法使用systemctl去管理进程的，因为docker里面用不了dbus总线。那我们如何在docker里面管理进程呢？
supervisor就是一个很好的docker进程管理工具。

```
```

## docker的网络模式
