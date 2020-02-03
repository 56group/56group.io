---
title: Nginx Tomcat负载均衡安装
date: 2019-07-05 17:00:00
tags: [NGINX, TOMCAT, 安装]
categories: Tomcat
---

# Nginx Tomcat负载均衡安装

### 环境

```
Role    | IP       
------- | --------------
Tomcat1 | 222.26.224.236
Tomcat2 | 222.26.224.237
Nginx   | 222.26.224.236
Memcached | 222.26.224.236
```

### 依赖安装

```
shell> yum install -y gcc pcre-devel zlib-devel libevent-devel
```

### tomcat JDK Nginx install (236)

```
shell> cd /usr/local/src
shell> wget https://nginx.org/download/nginx-1.11.3.tar.gz
shell> wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.4/bin/apache-tomcat-8.5.4.tar.gz

# Java Tomcat install
shell> tar zxvf jdk-8u65-linux-x64.gz -C /usr/local/
shell> ln -s /usr/local/jdk1.8.0_65/ /usr/local/jdk
shell> tar zxvf apache-tomcat-8.5.4.tar.gz -C /usr/local/
shell> ln -s /usr/local/apache-tomcat-8.5.4/ /usr/local/tomcat

# 配置环境变量
shell> vi /etc/profile.d/javadev.sh
# export JAVA_HOME=/usr/local/jdk
# export CATALINA_HOME=/usr/local/tomcat
# export PATH=$JAVA_HOME/bin:$CATALINA_HOME/bin:$PATH

shell> source /etc/profile.d/javadev.sh
# Nginx install
shell> tar zxvf nginx-1.11.3.tar.gz
shell> cd nginx-1.11.3
shell> ./configure --prefix=/usr/local/nginx
shell> make
shell> make install

# Memcached install
shell> wget http://www.memcached.org/files/memcached-1.4.29.tar.gz
shell> tar zxvf memcached-1.4.29.tar.gz
shell> cd memcached-1.4.29
shell> ./configure --prefix=/usr/local/memcached
shell> make
shell> make install
```

### memcached-session-manager

```
shell> mkdir jar
shell> cd jar
shell> wget http://repo1.maven.org/maven2/de/javakaffee/msm/memcached-session-manager/1.9.7/memcached-session-manager-1.9.7.jar
shell> wget http://repo1.maven.org/maven2/de/javakaffee/msm/memcached-session-manager-tc8/1.9.7/memcached-session-manager-tc8-1.9.7.jar
shell> wget http://repo1.maven.org/maven2/net/spy/spymemcached/2.11.1/spyme.mcached-2.11.1.jar
shell> wget http://repo1.maven.org/maven2/de/javakaffee/msm/msm-javolution-serializer/1.9.7/msm-javolution-serializer-1.9.7.jar
shell> wget http://repo1.maven.org/maven2/de/javakaffee/msm/msm-xstream-serializer/1.9.7/msm-xstream-serializer-1.9.7.jar
shell> wget http://repo1.maven.org/maven2/com/thoughtworks/xstream/xstream/1.4.9/xstream-1.4.9.jar
shell> wget https://repo.maven.apache.org/maven2/javolution/javolution/5.4.3/javolution-5.4.3.jar
shell> cp *.jar $CATALINA_HOME/lib

# 配置tomcat
shell> vi $CATALINA_HOME/conf/context.xml

<Context>
<Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
 memcachedNodes="n1:localhost:11211"
 failoverNodes="n1"
 requestUriIgnorePattern=".*\.(ico|png|gif|jpg|css|js)$"  transcoderFactoryClass="de.javakaffee.web.msm.serializer.javolution.Javolu tionTranscoderFactory"/>
 
 // 下面是注释failoverNodes的
 <Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
 memcachedNodes="n1:localhost:11211"
 #failoverNodes="n1"
 requestUriIgnorePattern=".*\.(ico|png|gif|jpg|css|js)$"  transcoderFactoryClass="de.javakaffee.web.msm.serializer.javolution.Javolu tionTranscoderFactory"/>
</Context>

# 配置log
shell> vi $CATALINA_HOME/conf/logging.properties
# de.javakaffee.web.msm.level = FINE
# net.spy.memcached.level = WARNING

shell> vi $CATALINA_HOME/bin/catalina.sh
# CATALINA_OPTS="-Dnet.spy.log.LoggerImpl=net.spy.memcached.compat.log.SunLogger"
```

### 配置另一个Tomcat (237)

```
# tomcat

# 236
shell> tar -cf tomcat.tar apache-tomcat-8.5.4/
shell> scp tomcat.tar root@222.26.224.237:/usr/local/

# 237
shell> cd /usr/local
shell> tar -xvf tomcat.tar
shell> ln -s /usr/local/apache-tomcat-8.5.4 /usr/local/tomcat
shell> rm -rf tomcat.tar

# jdk
shell> tar zxvf jdk-8u65-linux-x64.gz -C /usr/local/
shell> ln -s /usr/local/jdk1.8.0_65/ /usr/local/jdk

# 配置JDK和Tomcat的环境变量
shell> vi /etc/profile.d/javadev.sh
# export JAVA_HOME=/usr/local/jdk
# export CATALINA_HOME=/usr/local/tomcat
# export PATH=$JAVA_HOME/bin:$CATALINA_HOME/bin:$PATH
shell> source /etc/profile.d/javadev.sh`
```

### 配置LB

```
shell> cd /usr/local/nginx/conf/
shell> cp nginx.conf nginx.conf.bak
shell> vi nginx.conf # 配置见附加
```

### 启动服务

```
# 236
shell> /usr/local/memcached/bin/memcached -d -m 1024 -u root -c 1024 -p 11211 /tmp/memcached.pid

# 236
shell> /usr/local/tomcat/bin/startup.sh

# 237
shell> /usr/local/tomcat/bin/startup.sh

# 236
shell> /usr/local/nginx/sbin/nginx
```

### 访问测试

```
# 访问http://222.26.224.236,如果显示tomcat页面就OK
```

### 编写demo

```
# 项目tomcat-performance-test
# scp war to two server and test session id
```

> 在产品的开发中需要注意context.xml的配置,只需要对需要同步session的项目[[配置context](http://tomcat.apache.org/tomcat-7.0-doc/config/context.html#Defining_a_context)](http://tomcat.apache.org/tomcat-7.0-doc/config/context.html#Defining_a_context)

### 附加

##### nginx配置

```
worker_processes 1;

error_log logs/error.log;
error_log logs/error.log notice;
error_log logs/error.log info;

pid logs/nginx.pid;

events {
 worker_connections 1024;
}

http {
 include  mime.types;

 default_type application/octet-stream;

 log_format main '$remote_addr - $remote_user [$time_local] "$request" '
 '$status $body_bytes_sent "$http_referer" '
 '"$http_user_agent" "$http_x_forwarded_for"';
 access_log logs/access.log main;
 sendfile on;
 keepalive_timeout 65;
 upstream localhost {
   server 222.26.224.237:8080;
   server 222.26.224.236:8080;
 }

 server {
   listen  80;
   server_name localhost;
   charset utf-8;
   
   location / {
     proxy_pass http://localhost;
   }

   error_page  500 502 503 504 /50x.html;

   location = /50x.html {
     root  html;
   }
 }
}

```

### javadev.sh

```
export JAVA_HOME=/usr/local/jdk
export CATALINA_HOME=/usr/local/tomcat
export PATH=$JAVA_HOME/bin:$CATALINA_HOME/bin:$PATH
```

**## 总结

- 安装tomcat nginx

- 添加jar文件和配置context.xml

-  发布项目

> tomcat/lib:

> memcached-session-manager-tc8-1.9.7.jar memcached-session-manager-1.9.7.jar spymemcached-2.11.1.jar 这三个文件就可以,也可添加如上面描述的文件

