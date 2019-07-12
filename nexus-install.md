---
title: NEXUS服务安装
date: 2019-07-09 13:08:00
tags: [NEXUS, INSTALL]
categories: NEXUS
---

# NEXUS服务安装

##### 准备环境

> JDK 1.8

```shell
# 下载nexus3 https://help.sonatype.com/repomanager3/download
# 安装
shell> tar zxvf oss.tar.gz -C /usr/local/
shell> ln -s /usr/local/nexus-3.17.0-01/ /usr/local/nexus
shell> cd /usr/local
shell> cd nexus
shell> vi bin/nexus.rc
shell> vi /etc/init.d/nexus
shell> chmod u+x /etc/init.d/nexus
shell> chkconfig nexus on
shell> service nexus start
# 配置nginx
shell> cd /usr/local/nginx
shell> vi nginx/nginx.conf
shell> sbin/nginx -s reload
# 访问配置域名
```

##### nexus.rc

```shell
#run_as_user=""
run_as_user="root"
```

##### nexus

```shell
#!/bin/bash
#chkconfig:2345 20 90
#description:nexus
#processname:nexus
case $1 in
        start) su root /usr/local/nexus/bin/nexus start;;
        stop) su root /usr/local/nexus/bin/nexus stop;;
        status) su root /usr/local/nexus/bin/nexus status;;
        restart) su root /usr/local/nexus/bin/nexus restart;;
	dump ) su root /usr/local/nexus/bin/nexus dump ;;
        console ) su root /usr/local/nexus/bin/nexus console ;;
        *) echo "require console | start | stop | restart | status | dump " ;;
esac
```

##### nginx.conf

```shell
server
{
    listen 80;
    server_name YOUR_DOMAIN;

    location / {
        proxy_pass http://localhost:8081;
        proxy_redirect  off;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For
        $proxy_add_x_forwarded_for;
    }
}
```




