---
layout:     post                            # 使用的布局（不需要改）
title:      阿里云centos7服务器部署java后台                   # 标题
subtitle:            #副标题
date:       2017-06-01                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Java
---

# 阿里云centos7服务器部署java后台
> spring-boot项目已经打成war包了，本文讲解如何部署到服务器上

## 安装jdk
> 正式环境最好不要用open-jdk，如果安装了请先卸载。

```
wget --no-cookies --header "Cookies:oraclelicense=accept-securebackup-cookie;" http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.tar.gz
```
此为已经编译好的包，解压直接可以使用，不过需要配置一下**JAVA_HOME环境变量**。

## 安装tomcate

```
wget http://apache.fayea.com/tomcat/tomcat-8/v8.5.15/bin/apache-tomcat-8.5.15.zip
```
此压缩包也是解压即可使用，同样需要配置**CATALINA_HOME环境变量**，这样就能在任何地方使用命令行了，不必定位到文件夹；没有配置JAVA_HOME时，可以在bin/setclasspath.sh中添加以下两行代码：
```
export  JAVA_HOME=/usr/java/jdk1.7.0_71
export  JRE_HOME=/usr/java/jdk1.7.0_71/jre
```


## 部署war
这一步是最简单的，直接将war包放到webapps目录下，当Tomcat服务启动时会加载所有这个目录下的应用，服务器会自动解开war包，并在这个目录下生成一个同名的文件夹。

## 启动和停止tomcat服务

tomcat解压文件的bin文件夹中有**startup.sh**是用来**启动**服务的，**shutdown.sh**是用来**停止**服务的。

## 后记
> 学习中。。。 持续更新

* 防火墙需要放开8080端口
* 开机启动的实现
* [tomcat启动速度慢原因](http://www.tuicool.com/articles/uaiURzF)：
	tomcat生成sessionId时间过长，解决方法：
	* 通过修改Tomcat启动文件 -Djava.security.egd=file:/dev/urandom
	* 通过修改JRE中的java.security文件 securerandom.source=file:/dev/urandom
* 自动部署脚本：思路是通过ssh复制war到服务器，然后停止tomcat,然后在启动tomcat