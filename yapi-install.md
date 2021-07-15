---
title: YAPI安装
date: 2020-10-17 16:08:00
tags: [YAPI, INSTALL]
categories:  安装
---

### YAPI安装

- 1. 安装node
- 2.安装MongoDB

##### 安装Yapi

```shell
shell> npm install -g yapi-cli --registry https://registry.npm.taobao.org
shell> yapi server
```

##### Yapi服务管理

```shell
shell> npm install pm2 -g  //安装pm2
shell> cd  /usr/local/yapi/my-api
shell> pm2 start "vendors/server/app.js" --name yapi //pm2管理yapi服务
shell> pm2 info yapi //查看服务信息
shell> pm2 stop yapi //停止服务
shell> pm2 restart yapi //重启服务
```

#### Yapi升级

```shell
shell> cd /usr/local/yapi/my-yapi
shell> yapi ls //查看版本号列表
shell> yapi update //更新到最新版本
shell> yapi update -v {Version} //更新到指定版本
```

##### Yapi启动

```shell
shell> nohup node vendors/server/app.js > yapi.log 2>&1 &
```

##### Yapi管理用户

- admin@admin.com/ymfe.org