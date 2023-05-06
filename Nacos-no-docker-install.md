---
title: Nacos非Docker安装
date: 2022-07-16 15:07:00
tags: [NACOS, 安装]
categories: NACOS
---

### Nacos非Docker安装

##### MySQL数据库准备

```
shell> mysql -uroot -pYOUR_PASSWORD
mysql> CREATE SCHEMA `nacos_config` DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
mysql> CREATE USER 'nacos'@'%' IDENTIFIED BY 'YOUR_PASS'
mysql> GRANT ALL PRIVILEGES ON nacos_config.* TO 'nacos'@'%' WITH GRANT OPTION;
```

##### JDK

```
shell> tar -xvf jdk-8u311-linux-x64.tar.gz -C /usr/local/
shell> ln -s /usr/local/jdk1.8.0_311/ /usr/local/jdk
shell> vi /etc/profile.d/env.sh
```

##### env.sh

```
export JAVA_HOME/usr/local/jdk
export PATH=$JAVA_HOME/bin:$PATH
```

##### Nacos安装

```
shell> tar -xvf nacos-server-2.1.0.tar.gz -C /usr/local
shell> cd /usr/local/nacos/conf
shell> mysql -unacos -pYOUR_PASS
mysql> use nacos_config
mysql> source nacos-mysql.sql
shell> sh /usr/local/nacos/bin/startup.sh -m standalone
```

##### application.properties

```
# db.url.0=jdbc:mysql://127.0.0.1:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
# db.user.0=nacos
db.user.0=nacos
# db.password.0=nacos
db.password.0=YOUR_PASS
```

##### Nacos开启认证(必须开启)[[链接](https://nacos.io/zh-cn/docs/auth.html)]

```shell
nacos.core.auth.enabled=false
```

#### SpringBoot Cloud配置

```yml
spring:
  cloud:
    nacos:
      username: nacos
      password: 57$[jVnxO=
```

##### Nacos安全配置更改可自动刷新,不需重启

```shell
# conf/application.properties
nacos.core.auth.server.identity.key=YOUR_KEY
nacos.core.auth.server.identity.value=YOUR_VALUE
```

##### Nacos安全配置,需要重启

###### 先把token刷新时间更换了,这样服务重启后token不会过长时间不更新,导致服务链接不了

```shell
# conf/application.properties
nacos.core.auth.plugin.nacos.token.expire.seconds=60
```

###### 重启服务,待服务运行一段时间,时间为更改token刷新值之前的token的刷新时间+加上现在的时间后,更改secret.key,key的要求50字符串,base64加密

```shell
# conf/application.properties
nacos.core.auth.plugin.nacos.token.secret.key=YOUR_SECRET_KEY
```

##### ##### 重启Nacos

```shell
shell> /usr/local/nacos/bin/shutdown.sh
shell> sh /usr/local/nacos/bin/startup.sh -m standalone
```

##### 参考链接

[安全项更改]([Authorization](https://nacos.io/zh-cn/docs/v2/guide/user/auth.html))

[secret.key更换](https://nacos.io/zh-cn/blog/announcement-token-secret-key.html)
