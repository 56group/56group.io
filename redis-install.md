---
title:  Redis安装+远程访问
date: 2017-12-02 14:36:00
tags: [REDIS, 安装]
categories:  Linux
---
### Redis Install

##### Redis Install

```
# 上传redis安装包
shell> tar zxvf redis-4.0.5.tar.gz -C /usr/local/
shell> cd /usr/local
shell> ln -s /usr/local/redis-4.0.5/ /usr/local/redis
shell> cd redis
shell> make # 执行后添加环境变量
shell> vi redis.conf # 更改 136行为daemonize yes,允许守护进程运行redis
shell> mkdir /etc/redis
shell> cp redis.conf /etc/redis/6379.conf
shell> cp utils/redis_init_script /etc/init.d/redis
shell> chmod u+x /etc/init.d/redis
shell> vi /etc/init.d/redis # 更改命令执行路径
shell> chkconfig redis on
shell> service redis start
```
##### redis启动脚本更改

```
# 添加chkconfig在第一行下面添加下面两行
# chkconfig: 2345 10 90
# description: Start and Stop redis
# 更改脚本路径
EXEC=/usr/local/redis/src/redis-server # 更改脚本路径
CLIEXEC=/usr/local/redirs/src/redis-cli # 更改脚本路径
```
##### 设置允许远程访问

```
shell> vi /etc/redis/6379.conf
# 注释bind 127.0.0.1
# 更改protected-mode为no
shell> service redis stop
shell> service redis start
```