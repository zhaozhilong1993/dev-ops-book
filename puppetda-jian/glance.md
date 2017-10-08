# Glance基本介绍

## 介绍

Glance是OpenStack Image Storage服务，提供虚拟机镜像的存储、查询和检索的系统。

Glance 支持不同的存储后端 \(Filesystem Swift S3 Ceph Sheepdog etc.\)。

Glance提供REST API来支持镜像服务：查询、注册、上传、获取、删除和访问权限管理。

## 镜像格式

Glance支持的镜像格式包括raw，qcow2，vmdk、iso等等。

Glance支持的container format（包含metadata的镜像文件）包括bare、ovf、ami、docker等等，注意目前OpenStack没有使用，可以都设为bare。

Glance支持的后端包括Filesystem、Swift、Ceph RBD、Sheepdog等等，注意没有商业存储。

## 系统架构

API v1有glance-api\(default port 9292\)和glance-registry\(default port 9191\)两个WSGI service，提供REST API。glance-api对外提供REST API，glance-registry的API只由glance-api使用。

API v2实现上，将glance-registry的功能合并到了glance-api，减少了一个中间环节。

注意Glance服务是最简单的OpenStack服务之一，不依赖RabbitMQ。



