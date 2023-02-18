---
title: basic-chapter
tags: 
date: 2023-02-18 19:20:44
id: 1676719245007892100
---
# 摘要

# 步骤

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
sudo docker run -p 3306:3306 --restart=always --name mysql \
 --privileged=true -v /mydata/mysql/log:/var/log/mysql \
-v /mydata/mysql/data:/var/lib/mysql \
-v /mydata/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:5.7.30
```

## 安装 redis

```sh
docker pull redis:5.0.8
```

```sh
mkdir -p /mydata/redis/conf
touch /mydata/redis/conf/redis.conf
echo "appendonly yes"  >> /mydata/redis/conf/redis.conf

docker run -p 6379:6379 --name redis --restart=always \
 -v /mydata/redis/data:/data \
 -v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
 -d redis:5.0.8 redis-server /etc/redis/redis.conf
```













