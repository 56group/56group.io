---
title:  JAVA WEB DEV ENV INSTALL
date: 2017-07-27 20:35:00
tags: [java, dev]
categories:  JAVA
---

### Java Web开发环境搭建

> Java Web开发环境是多个工具配合完成的.用于构建的maven,服务器的tomcat,代码管理的git,数据库MySQL,数据库管理的MySQL Workbench和开发ide的idea.

##### 下载

- 下载idea,通常下载的是Ultimate版本 [下载地址](https://www.jetbrains.com/idea/)
- 下载tomcat,通常下载8版本 [下载地址](http://tomcat.apache.org/)
- 下载maven,需要下载3版本 [下载地址](http://maven.apache.org/)
- 下载git,需要下载最新版本 [下载地址](https://git-scm.com/downloads/)
- 下载MySQL, 需要下载5.6+ zip包版本 [下载地址](http://dev.mysql.com/downloads/mysql/)
- 下载MySQL Workbench, 需要下载最新压缩包版本 [下载地址](http://dev.mysql.com/downloads/workbench/)

##### 安装 (规定我们的安装目录为HOME)

- 安装tomcat, tomcat的安装为解压安装,把tomcat的压缩包解压到安装目录HOME
- 安装maven, maven的安装亦为解压安装,把maven的压缩包解压到安装目录HOME
- 安装git, git的安装和windows软件的安装方式一样,单击exe安装文件,根据引导安装
- 安装idea, idea的安装合windows软件的安装方式一样,单击exe安装文件,根据引导安装
- 安装MySQL, MySQL的安装方式比较特殊,首先解压压缩包到安装目录HOME,然后进入mysql目录,在这个mysql的bin目录下,按住shift右键,打开命令行(需要注意的是命令行必须是以administrator用户运行的), 然后再命令行中写入: mysqld.exe install, 然后安装成功后启动MySQL: net start mysql
- 安装MySQL Workbench,解压安装包到安装目录HOME,然后直接在workbench目录用运行exe

##### 通用配置

- 配置tomcat和maven(在这个之前必须确保机器已经安装JDK1.7+),在个人环境变量中添加CATALINA_HOME,M2E_HOME和PATH

> CATALINA_HOME=HOME/tomcat... 和M2E_HOME=HOME/maven... PATH=%JAVA_HOME%\bin;%CALINA_HOME%\bin;%M2E_HOME%\bin

- 配置Maven本地库的位置, 第一步是更改maven安装文件下的conf目录下的setting.xml

> 更改localRepository目录: 填写自己的本地库路径 把setting.xml文件复制到自己的本地酷路径的目录下 更改server配置

```
<server>
    <id>tomcat7</id>
    <username>admin</username>
    <password>admin</password>
</server>
```

##### 配置Tomcat

> 更改tomcat的用户,可以使maven中配置的server启动tomcat服务

```
<role rolename="manager" />
<user username="admin" password="admin" roles="manager" />
```

#####  配置idea, 打开idea,在file中打开Settings,开始配置相关支持

> 配置maven, 搜索maven,然后配置maven的选项卡中的maven_home(本地maven的安装目录),配置用户配置文件(选择刚刚配置的自己本地酷下的setting.xml)