---
title: new cdh
date: 2020-07-09 11:56:03
tags: cdh
---

## prepare

- 搭建私有服务器
https://docs.cloudera.com/documentation/enterprise/6/latest/topics/cm_ig_create_local_package_repo.html#internal_package_repo

* 安装apache服务器
> yum install httpd
> systemctl start httpd

* 下载cm文件 
wget  https://archive.cloudera.com/cm6/6.3.1/repo-as-tarball/cm6.3.1-redhat7.tar.gz

* 下载parcels



- 创建并配置mysql服务器
https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/cm_ig_mysql.html

- 创建数据库

Service	Database	User
Cloudera Manager Server	scm	scm
Activity Monitor	amon	amon
Reports Manager	rman	rman
Hue	hue	hue
Hive Metastore Server	metastore	hive
Sentry Server	sentry	sentry
Cloudera Navigator Audit Server	nav	nav
Cloudera Navigator Metadata Server	navms	navms
Oozie	oozie	oozie

* 注意使用mysql driver，放到 /usr/share/java中去

- 安装并启动cloudera manager


问题1、 hostname不正确

问题2、做好机器的规划

问题3、做好存储路径的规划 


- 启动各个roles





