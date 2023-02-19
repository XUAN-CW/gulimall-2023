---
title: basic-chapter
tags: 
date: 2023-02-18 19:20:44
id: 1676719245007892100
---
# 摘要

# 本机环境

## jdk1.8

jdk 1.8 差不多得了

```sh
java version "1.8.0_281"
Java(TM) SE Runtime Environment (build 1.8.0_281-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.281-b09, mixed mode)
```

## maven

版本 3.6+ 应该都可以，我选择 [apache-maven-3.6.3-bin.zip](https://archive.apache.org/dist/maven/maven-3/3.6.3/binaries/)

```sh
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
```

## node.js

[全部版本下载页面](https://nodejs.org/zh-cn/download/releases/) | [Node.js 10.16.3](https://nodejs.org/download/release/v10.16.3/) |[node-v10.16.3-x64.msi](https://nodejs.org/download/release/v10.16.3/node-v10.16.3-x64.msi) 

# 虚拟机环境

## 安装虚拟机

使用 virtual box 安装 centOS-7.8 虚拟机， [Vagrantfile](Vagrantfile) 中有两个重要的点：

1. 网络地址设置为 192.168.56.10

```
  config.vm.network "private_network", ip: "192.168.56.10"
```

2. 分配更多内存，以防内存不足，后面应用启动不起来

```
 config.vm.provider "virtualbox" do |vb|
   # Display the VirtualBox GUI when booting the machine
   # vb.gui = true
 
   vb.cpus = 2
   # Customize the amount of memory on the VM:
   vb.memory = "8192"
 end
```

## 安装docker

不废话，参考 [CentOS Docker 安装](https://www.runoob.com/docker/centos-docker-install.html) 

## 安装 mysql

```sh
docker pull mysql:5.7.30
```

```sh
sudo docker run -d \
  -p 3306:3306 \
  --restart=always \
  --privileged=true \
  -v /mydata/mysql/log:/var/log/mysql \
  -v /mydata/mysql/data:/var/lib/mysql \
  -v /mydata/mysql/conf:/etc/mysql \
  -e MYSQL_ROOT_PASSWORD=root \
  --name mysql \
  mysql:5.7.30
```

配置文件没有也无所谓， mysql:5.7.30 起来就行了

### 谷粒商城数据库

创建数据库后逐个执行：

```sql
CREATE DATABASE IF NOT EXISTS `gulimall_oms` CHARACTER SET utf8 COLLATE utf8_general_ci;
CREATE DATABASE IF NOT EXISTS `gulimall_pms` CHARACTER SET utf8 COLLATE utf8_general_ci;
CREATE DATABASE IF NOT EXISTS `gulimall_sms` CHARACTER SET utf8 COLLATE utf8_general_ci;
CREATE DATABASE IF NOT EXISTS `gulimall_ums` CHARACTER SET utf8 COLLATE utf8_general_ci;
CREATE DATABASE IF NOT EXISTS `gulimall_wms` CHARACTER SET utf8 COLLATE utf8_general_ci;
CREATE DATABASE IF NOT EXISTS `sys_menus` CHARACTER SET utf8 COLLATE utf8_general_ci;
```

1.  gulimall_oms：[gulimall_oms.sql](assets\data\gulimall_oms.sql) 

2.  gulimall_pms：[gulimall_pms.sql](assets\data\gulimall_pms.sql) [pms_catelog.sql](assets\data\pms_catelog.sql) 
3.  gulimall_sms：[gulimall_sms.sql](assets\data\gulimall_sms.sql) 
4.  gulimall_ums：[gulimall_ums.sql](assets\data\gulimall_ums.sql) 
5.  gulimall_wms：[gulimall_wms.sql](assets\data\gulimall_wms.sql) 
6.  ~~sys_menus：[sys_menus.sql](assets\data\sys_menus.sql)~~ 

### [renren](https://gitee.com/renrenio) 数据库

本项目管理后台基于人人开源，为了能够使用 [renren-fast.zip](assets\data\renren-fast.zip) 、 [renren-fast-vue.zip](assets\data\renren-fast-vue.zip) 、 [renren-generator.zip](assets\data\renren-generator.zip) 等项目，因此需要导入数据库：解压  [renren-fast.zip](assets\data\renren-fast.zip) ，解压目录下有 **db/mysql.sql** ，建表 `gulimall_admin` 并执行  **db/mysql.sql** 

```sql
CREATE DATABASE IF NOT EXISTS `gulimall_admin` CHARACTER SET utf8 COLLATE utf8_general_ci;
use gulimall_admin;
```

### 前端左侧目录

在数据库 **gulimall_dmin** 中执行  [sys_menu.sql](assets\data\sys_menu.sql) 

## 安装 redis

```sh
docker pull redis:5.0.8
```

```sh
mkdir -p /mydata/redis/conf
touch /mydata/redis/conf/redis.conf
echo "appendonly yes"  >> /mydata/redis/conf/redis.conf

docker run -d \
  -p 6379:6379 \
  --name redis \
  --restart=always \
  -v /mydata/redis/data:/data \
  -v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
  redis:5.0.8 redis-server /etc/redis/redis.conf
```

## 安装nacos

建议装在虚拟机上，在代码改一下 nacos 服务地址就行了。这里虚拟机内存一定要大！不然启动不起来！

```sh
docker run -d \
  --restart=always \
  -p 8848:8848 \
  --name nacos \
  -e MODE=standalone \
  nacos/nacos-server:1.1.3
```

如果实在内存不够，那就使用下面这个：

```sh
docker run -d \
  --restart=always \
  -p 8848:8848 \
  --name nacos \
  -e MODE=standalone \
  -e JVM_XMS=256m \
  -e JVM_XMX=256m \
  nacos/nacos-server:1.1.3
```

### 导入配置

新建命名空间：

-  [Nacos_coupon.html](assets\data\Nacos_coupon.html) 并导入 [coupon.zip](assets\data\coupon.zip) 
-  [Nacos_gateway.html](assets\data\Nacos_gateway.html) 

# 阿里云OSS

在 `gulimall-third-party` 中进行模块文件上传，需要在 nacos 中配置

1.  AccessKey ：access-key 、secret-key
2.  bucket ：endpoint、bucket

## 跨域

 [OSS管理控制台-设置跨域.html](assets\data\OSS管理控制台-设置跨域.html) 

## 公共读

在 [读写权限](assets\data\OSS管理控制台-读写权限.html) 中设置为**公共读** 

## nacos

 [Nacos_third-party](assets\data\Nacos_third-party.html) 导入  [Nacos_oss.yml.html](assets\data\Nacos_oss.yml.html) ，里面的值要换成自己的

 [只有我能使用的私有阿里云配置](assets\private\nacos_config_export_20210729225748.zip) 

## 前端访问重置

完成后需要搜索 `gulimall-hello` ，将路径替换成你的路径





