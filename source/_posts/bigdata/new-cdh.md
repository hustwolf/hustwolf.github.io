---
title: cdh6.3.2搭建说明
date: 2020-07-09 11:56:03
tags: cdh
---
## > 说明

cdh自6.3.3开始就需要授权了，所以这里选择使用6.3.2


## > 规划

cdh集群中的机器，除了要运行hadoop中各种service(hdfs\yarn\hbase\hive\spark等)的进程，还需要运行为管理这些服务cloudera公司开发的一些程序，比如cm server、cm agent、host monitor、activity monitor等；
各个机器的配置与稳定性要求不同


|host role|desc|数量|配置与稳定性|存储规划|
|-|-|-|-|-|
|hadoop master|hadoop服务的master，包括hdfs namenode、yarn resource manager、|2|配置中，稳定要求高|主要是一些运行log，注意修改log的目录，保证有50G以上的空间|
|hadoop slave|hadoop服务的slave，比如hdfs datanode、yarn node manager、 |3~N|配置高，稳定性要求中|主要是hdfs data dir，需要TB级别以上的存储空间|
|cm server|cm服务的master，比如cm server、host monitor等|1-2|配置中国，稳定要求中|主要是一些运行log，注意修改log的目录，保证有100G以上的空间|

cm在管理各个hadoop service时，并非通过直接fork这些service的进程来管理，而是在每台机器上，都通过supervisord来管理，cm server通过给supervisord发送命令，来控制每个serivce的生命周期，所以cm的死活并不影响集群的死活，只是很多cm提供的监控没法看或者丢失

后文会通过引用cm server\hadoop master\hadoop slave来应用不同类型的host

更加的详细不同规模的cdh集群中各机器的role分配可见 [cm-roles](https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/cm_ig_host_allocations.html#concept_f43_j4y_dw)


## > cdh中概念说明

|term|desc|example|
|-|-|-|
|cloudera manager(cm)|cdh集群的主控程序，一个典型的java web项目，启动后默认监听在7180端口，第一次进入会要求初始化管理员账户密码等，需要依赖关系型数据库(mysql)||
|cloudera manager(cm) agent|运行在每个受cm管理的host上的进程，接受cm中传送过来的命令，对当前host进行各种处理，通过监控各个host的运行状态||
|host|接受cm管理的一个个机器，在每个机器上都运行有cm agent程序||
|cluster|一个hadoop集群的统称，理论上一个cm server可以管理多个cluster，通过不同的名字区分|cluster 1、cluster 2|
|parcel|包，其实就是一个装有所有lib、script、conf的zip包，cm通过它来发布各种service|比如cdh6.3.2 parcel、spark2 parcel|
|service|属于某个cluster的提供单一功能的进程组|hdfs service、yarn service|
|role|service中一类进程的统称|hdfs namenode、hdfs datanode|
|role group|对role进行逻辑上分组|hdfs datanode high、hdfs datanode storage，分别用来区分高配机与存储机上的instance|
|instance|role在某个host上一个进程，某个instance一定是属于某个role group|比如hdfs nodedata在机器host1中的那个进程|
|service gateway|一种role，几乎每个service都有gateway这种role，比如hdfs gatway，当把这个role赋给host1时，就可以在host1上使用hdfs的各种命令了。cm会自动帮你配置好必要的配置文件与程序||


通过cm的web console可以很方便的以上各种粒度来管理、分配、发布配置文件到不同的host上
同时也控制service、instance的生命周期

## > 安装

## 0 pre install
确保各个机器满足以下要求
- 可以免密登录各个机器
配置好正确的ssh登录权限
- 配置好机器正确的hostname
保证其他机器可以通过hostname访问到正确的机器，设置好dns
- 关闭防火墙 
避免机器之间因为防火墙导致不通 
> systemctl disable firewalld
> systemctl stop firewalld
- [SElinux](https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/install_cdh_disable_selinux.html)
在安装系统时，需要设置为permissive mode
- ntp 
> yum install ntp
- python2.7
cdh cm agent是python程序，需要python2.7启动
如果是centos7，系统自带，不用额外安装
- root权限
各个机器


## 1 [搭建私有包服务器](https://docs.cloudera.com/documentation/enterprise/6/latest/topics/cm_ig_create_local_package_repo.html#internal_package_repo)
找一台机器，我们这里选择cm server host
* 安装apache服务器
> yum install httpd
* 修改httpd配置文件
默认是/etc/httpd/conf/httpd.conf，其中的<IfModule mime_module> section,
增加 parcel的mime；

>     AddType application/x-gzip .gz .tgz .parcel

其实parcel就是一个zip文件，cloudera加了个自己的后缀，表示自己的文件，就跟java里用jar做后缀，其实也是一个zip文件一样，结果如下：
```xml
<IfModule mime_module>
    #
    # TypesConfig points to the file containing the list of mappings from
    # filename extension to MIME-type.
    #
    TypesConfig /etc/mime.types

    #
    # AddType allows you to add to or override the MIME configuration
    # file specified in TypesConfig for specific file types.
    #
    #AddType application/x-gzip .tgz
    #
    # AddEncoding allows you to have certain browsers uncompress
    # information on the fly. Note: Not all browsers support this.
    #
    #AddEncoding x-compress .Z
    #AddEncoding x-gzip .gz .tgz
    #
    # If the AddEncoding directives above are commented-out, then you
    # probably should define those extensions to indicate media types:
    #
    AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz .parcel

    #
    # AddHandler allows you to map certain file extensions to "handlers":
    # actions unrelated to filetype. These can be either built into the server
    # or added with the Action directive (see below)
    #
    # To use CGI scripts outside of ScriptAliased directories:
    # (You will also need to add "ExecCGI" to the "Options" directive.)
    #
    #AddHandler cgi-script .cgi

    # For type maps (negotiated resources):
    #AddHandler type-map var

    #
    # Filters allow you to process content before it is sent to the client.
    #
    # To parse .shtml files for server-side includes (SSI):
    # (You will also need to add "Includes" to the "Options" directive.)
    #
    AddType text/html .shtml
    AddOutputFilter INCLUDES .shtml
</IfModule>
```
- 重启服务
> systemctl start httpd

* 下载并发布[parcel repository](https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/cm_ig_create_local_parcel_repo.html)

parcel是被cm使用，用来发布给各个服务的载体，所有我们配置好parcel的私服后，还需要去cm中配置好去那里使用该私服

>sudo mkdir -p /var/www/html/cloudera-repos
sudo wget --recursive --no-parent --no-host-directories https://archive.cloudera.com/cdh6/6.3.2/parcels/CDH-6.3.2-1.cdh6.3.2.p0.1605554-el7.parcel -P /var/www/html/cloudera-repos
sudo wget --recursive --no-parent --no-host-directories https://archive.cloudera.com/cdh6/6.3.2/parcels/CDH-6.3.2-1.cdh6.3.2.p0.1605554-el7.parcel.sha1 -P /var/www/html/cloudera-repos
sudo wget --recursive --no-parent --no-host-directories https://archive.cloudera.com/cdh6/6.3.2/parcels/CDH-6.3.2-1.cdh6.3.2.p0.1605554-el7.parcel.sha256 -P /var/www/html/cloudera-repos
sudo wget --recursive --no-parent --no-host-directories https://archive.cloudera.com/cdh6/6.3.2/parcels/manifest.json -P /var/www/html/cloudera-repos

>sudo wget --recursive --no-parent --no-host-directories https://archive.cloudera.com/gplextras6/6.3.2/parcels/GPLEXTRAS-6.3.2-1.gplextras6.3.2.p0.1605554-el7.parcel -P /var/www/html/cloudera-repos
sudo wget --recursive --no-parent --no-host-directories https://archive.cloudera.com/gplextras6/6.3.2/parcels/GPLEXTRAS-6.3.2-1.gplextras6.3.2.p0.1605554-el7.parcel.sha1 -P /var/www/html/cloudera-repos
sudo wget --recursive --no-parent --no-host-directories https://archive.cloudera.com/gplextras6/6.3.2/parcels/GPLEXTRAS-6.3.2-1.gplextras6.3.2.p0.1605554-el7.parcel.sha256 -P /var/www/html/cloudera-repos
sudo wget --recursive --no-parent --no-host-directories https://archive.cloudera.com/gplextras6/6.3.2/parcels/manifest.json -P /var/www/html/cloudera-repos


>sudo chmod -R ugo+rX /var/www/html/cloudera-repos/cdh6
sudo chmod -R ugo+rX /var/www/html/cloudera-repos/gplextras6

* 配置cm使用parcel私服
在后面启动cm server之后，在web console里，
切换到 Parcels 页面，然后在Configuration里，设置为私服的地址即可，比如
> http://\<cm-server-host\>/cloudera-repos/cdh6/6.3.2/parcels/


* 下载并发布[package repository](https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/cm_ig_create_local_package_repo.html#download_publish_package_repo)

在centos中，这里是搭建yum私服，所以最后还需要修改系统的yum.repo.d，增加



wget  https://archive.cloudera.com/cm6/6.3.1/repo-as-tarball/cm6.3.1-redhat7.tar.gz




## 2 [安装jdk](https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/cdh_ig_jdk_installation.html)
推荐使用jdk1.8,如果系统已经有，可跳过

* 去[oracle jdk下载官网](https://www.oracle.com/java/technologies/javase/javase8-archive-downloads.html)
假如当前的最新发新版为 jdk8u202，有更新版替换版本号即可
我们这里选择 Java SE Development Kit 8u202 - linux x64版本

* 把jdk解压缩到/usr/java/
> tar xvfz /path/to/jdk-8u202-linux-x64.tar.gz -C /usr/java/

* 设置正确的环境变量

可以新加文件 /etc/profile.d/java.sh
```bash
export JAVA_HOME="/usr/java/jdk-8u202"
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH="$JAVA_HOME/bin:$JRE_HOME/bin:$PATH"
```
登录时自动加载

## 3 [安装cm server](https://docs.cloudera.com/documentation/enterprise/6/latest/topics/install_cm_server.html)

如果之前的package repo私服搭建正确的话，这里非常简单，登录cm server host，执行

>yum install cloudera-manager-daemons cloudera-manager-agent cloudera-manager-server 

即可在cm server


## 4 [配置数据库](https://docs.cloudera.com/documentation/enterprise/6/latest/topics/cm_ig_installing_configuring_dbs.html)

cdh的运行需要很多数据库，主要有

|cdh service| desc| recommand name||
|-|-|-|-|
|cloudera manager server| cm管理 |||

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

## 5 [安装各个service](https://docs.cloudera.com/documentation/enterprise/6/latest/topics/cm_ig_installing_configuring_dbs.html)

以下操作都可以通过cm webserver进行

- 启动各个roles



## 5 after install
cdh系统安装之后，需要修改下很多配置
- 各个service的log dir
cdh系统中





