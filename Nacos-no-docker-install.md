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

##### Nacos开启认证(必须开启)[链接](https://nacos.io/zh-cn/docs/auth.html)]

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


