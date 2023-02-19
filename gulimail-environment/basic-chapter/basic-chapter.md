---
title: basic-chapter
tags: 
date: 2023-02-18 19:20:44
id: 1676719245007892100
---
# 摘要

# 本机环境

# jdk1.8

jdk 1.8 差不多得了

```sh
java version "1.8.0_281"
Java(TM) SE Runtime Environment (build 1.8.0_281-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.281-b09, mixed mode)
```

# maven

版本 3.6+ 应该都可以

```sh
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
```



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

这里虚拟机内存一定要大！不然启动不起来！

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





# 执行数据库脚本

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

2.  gulimall_oms：[gulimall_pms.sql](assets\data\gulimall_pms.sql) [pms_catelog.sql](assets\data\pms_catelog.sql) 
3.  gulimall_sms：[gulimall_sms.sql](assets\data\gulimall_sms.sql) 
4.  gulimall_ums：[gulimall_ums.sql](assets\data\gulimall_ums.sql) 
5.  gulimall_wms：[gulimall_wms.sql](assets\data\gulimall_wms.sql) 
6.  sys_menus：[sys_menus.sql](assets\data\sys_menus.sql) 

# 初始化项目

# renren

## [renren-fast](https://gitee.com/renrenio/renren-fast) 

### 资源

 [renren-fast.zip](assets\data\renren-fast.zip) 

### 环境搭建

renren-fast 数据库，解压  [renren-fast.zip](assets\data\renren-fast.zip) ，解压目录下有 **db/mysql.sql** ，建表 `gulimall_admin` 并执行  **db/mysql.sql** 

```sql
CREATE DATABASE IF NOT EXISTS `gulimall_admin` CHARACTER SET utf8 COLLATE utf8_general_ci;
use gulimall_admin;
```

### 启动与访问

http://localhost:8080/renren-fast 

有下面信息表示成功：

```
{"msg":"invalid token","code":401}
```

## [renren-fast-vue](https://gitee.com/renrenio/renren-fast-vue) 

### 资源

 [renren-fast-vue.zip](assets\data\renren-fast-vue.zip) 

### 环境搭建

#### node.js

[全部版本下载页面](https://nodejs.org/zh-cn/download/releases/) | [Node.js 10.16.3](https://nodejs.org/download/release/v10.16.3/) |[node-v10.16.3-x64.msi](https://nodejs.org/download/release/v10.16.3/node-v10.16.3-x64.msi) 

#### 淘宝镜像源

```
npm config set registry http://registry.npm.taobao.org/
```

### 运行

所有问题的根源都在 **node_modules**，npm install之前，应该将这个文件夹删除，然后再进行安装和运行。

```
npm install
```

```
npm run dev
```

## [renren-generator](https://gitee.com/renrenio/renren-generator) 

 [renren-generator.zip](assets\data\renren-generator.zip) 

启动后进入：http://localhost/  ，使用代码生成器生成代码

# nacos

## 安装

### 本机

[下载页面](https://github.com/alibaba/nacos/releases/tag/1.1.3) | [nacos-server-1.1.3.zip](https://github.com/alibaba/nacos/releases/download/1.1.3/nacos-server-1.1.3.zip) 

### 虚拟机

建议装在虚拟机上，改一下 nacos 服务地址就行了

```
docker pull nacos-server:1.4.1
```

```
docker run --restart=always -e JVM_XMS=256m -e JVM_XMX=256m --env MODE=standalone --name nacos-server -d -p 8848:8848 nacos/nacos-server:1.4.1
```

## 配置

新建命名空间：

-  [Nacos_coupon.html](assets\data\Nacos_coupon.html) 并导入 [coupon.zip](assets\data\coupon.zip) 
-  [Nacos_gateway.html](assets\data\Nacos_gateway.html) 













