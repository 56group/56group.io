---
title:  Nginx日志分析
date: 2018-07-02 17:05:00
tags: [NGINX, LOG, GOACCESS]
categories:  NGINX
---
### Nginx日志分析

#####安装goaccess

```
# tar.gz安装
```

##### 配置goaccess

```
shell> vi ~/.goaccessrc # 添加如下内容
```
##### .goaccessrc

```
time-format %T
date-format %d/%b/%Y
log-format %h %^[%d:%t %^] "%r" %s %b "%R" "%u"
```
### Nginx日志格式

```
log_format access '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"';
log_format main '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"';
```
##### 日志文件分析

```
shell> goaccess -f FILE.log > FILE.html
```