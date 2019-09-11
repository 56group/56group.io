---
title:  新安装Tomcat
date: 2019-04-17 10:08:00
tags: [TOMCAT, 安装]
categories:  Tomcat
---

### 新安装Tomcat

> 一个服务器上安装多个tomcat需要更改的配置文件

##### 更改server端口

```
# 更改为新的端口,原端口为8005
<Server port="8006" shutdown="SHUTDOWN">
```

##### 更改connector端口

```
# 更改为新的端口,原端口为8000
<Connector port="8000" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```

##### 更改AJP端口

```
# 更改为新的端口,原端口为8009
<Connector port="8010" protocol="AJP/1.3" redirectPort="8443" />
```

##### 更改/etc/profile.d/tomcat.sh

```
########## tomcat3 ###########
CATALINA3_BASE=/usr/local/tomcat_leader
CATALINA3_HOME=/usr/local/tomcat_leader
TOMCAT3_HOME=/usr/local/tomcat_leader
export CATALINA3_BASE CATALINA3_HOME TOMCAT3_HOME
########## tomcat3 ############
```

##### 更改catalina.sh

```
# 在#!/bin/bash下面加
export CATALINA_BASE=$CATALINA3_BASE
export CATALINA_HOME=$CATALINA3_HOME
```
