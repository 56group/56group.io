---
title:  JAVA WAR包部署
date: 2017-07-27 20:06:00
tags: [JAVA, 部署]
categories:  JAVA
---

### Java web project test deploy

> 项目发布描述:当本地开发完成项目以后,项目需要以测试的方式发布或者是产品发布,需要在已经安装好的数据库,tomcat,jdk,nginx的发布环境中发布项目.

##### 上传war和sql

```
 #通过lrzsz上传资源,如果没有安装 使用yum install lrzsz -y安装
 shell> mkdir /tmp/java
 shell> cd /tmp/java
 shell> rz # 选择需要上传的文件
```

##### 数据库准备

```
# 配置数据库的访问用户,需要确定需要发布的项目的数据库字符集的配置
shell> mysql-uroot -ppass
mysql> CREATE SCHEMA `new_schema` DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
mysql> CREATE USER 'monty'@'localhost' IDENTIFIED BY 'some_pass';
mysql> GRANT ALL PRIVILEGES ON new_schema.* TO 'monty'@'localhost' WITH GRANT OPTION;
mysql> \q
shell> source YOUR_SQL.sql
```

##### tomcat发布项目

```
# 如果原来项目发布过,需要先删除原来的项目文件
shell> cd /usr/local/tomcat/webapps
shell> /usr/local/tomcat/bin/shutdown.sh
shell> mv /tmp/java/YOUR.war .
shell> /usr/local/tomcat/bin/startup.sh
```

##### 服务域名配置

```
登陆带域名系统配置子域名
```

##### nginx代理

```
# 配置nginx代理刚刚发布的tomcat项目到域名
shell> vi /usr/local/nginx/conf/nginx.conf # 在最后面的一个server下,平行加入新的代理配置
```

##### nginx配置样板

```
server
{
    listen 80;
    server_name project.example.com;

    location / {
        proxy_pass http://localhost:8080;
       rewrite ^/$ /projectcontext last;
    }
}
```

> 需要注意的是项目中数据库的配置和发布服务上服务器的数据库配置的用户名密码和连接数据库名一致.
