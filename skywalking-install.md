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
- bin/oapService.sh启动backend
- 更改webapp下webapp.yml端口为9004
- bin/webappService.sh启动UI
- 访问http://IP:9004

