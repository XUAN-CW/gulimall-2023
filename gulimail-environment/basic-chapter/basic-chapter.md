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













