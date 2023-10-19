---
title: centos挂载iso文件作为yum安装源
date: 2023-02-11 19:37:25.000 +0800
type: book
category:
  - linux
keywords:
  - centos7
  - yum
weight: 3
---
## 简介
centos挂载iso文件作为yum安装源
## 上传系统iso文件
例如系统文件名：CentOS-7-x86_64-DVD-2207-02.iso
## 创建挂载目录
~~~bash
mkdir -p /media/CentOS7
~~~
## 挂载镜像文件
~~~bash
mount -t iso9660 -o loop /path/to/CentOS-7-x86_64-DVD-2207-02.iso /media/CentOS7
~~~
## 持久化挂载文件
~~~bash
vi /etc/fstab
~~~
~~~vm
/path/to/CentOS-7-x86_64-DVD-2207-02.iso /media/CentOS7 iso9660 defaults,ro,loop 0 0
~~~
## 新增文件/etc/yum.repos.d/CentOS7-Local.repo
~~~bash
vi /etc/yum.repos.d/CentOS7-Local.repo
[CentOS7-Local]
name=CentOS7
baseurl=file:///media/CentOS7
enabled=1
gpgcheck=0
gpgkey=file:///media/CentOS7/RPM-GPG-KEY-CentOS-7
~~~
## 备份/etc/yum.repos.d/CentOS-Base.repo
~~~bash
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
~~~
这里CentOS-Base.repo文件主要是去网络上找相应的包，修改为从本地源读取
## 清除yum缓存
~~~bash
yum clean all
~~~
