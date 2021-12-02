---
title: SkyWalking 8.3.0安装
date: 2020-12-26 15:55:00
tags: [安装, SKYWLKING]
categories:  MS
---

### [SkyWalking 8.3.0安装](https://skyapm.github.io/document-cn-translation-of-skywalking/zh/8.0.0/setup/backend/backend-ui-setup.html)

- [下载](https://skywalking.apache.org/downloads/)解压安装
- 配置MySQL数据库和用户密码,不需要导入任何数据表
- 更改config/application.yml配置文件(更改前备份下),更改storage下的mysql链接信息
- 配置config/application.yml中recordDataTTL和metricsDataTTL参数节省存储空间
- 上传mysql-connect-java.jar
- bin/oapService.sh启动backend
- 更改webapp下webapp.yml端口为9004
- bin/webappService.sh启动UI
- 访问http://IP:9004

##### 复制扩展包

```
cp optional-plugins/apm-spring-webflux-5.x-plugin-8.8.0.jar plugins/
```

##### MySQL数据库

```
mysql> CREATE SCHEMA `skywalking` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
mysql> CREATE USER 'skywalking'@'%' IDENTIFIED BY 'skywalking@zhulin.xin';
mysql> GRANT ALL PRIVILEGES ON skywalking.* TO 'skywalking'@'%' WITH GRANT OPTION;
```

