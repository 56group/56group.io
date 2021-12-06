---
title:  Nginx安装
date: 2021-12-04 16:37:00
tags: [INSTALL, NGINX]
categories:  NGINX
---

##### Nginx安装

```
shell> tar -xvf nginx-1.21.3.tar.gz 
shell> cd nginx-1.21.3
shell> yum install zlib pcre zlib-devel pcre-devel openssl openssl-devel gcc -y
shell> ./configure --prefix=/usr/local/nginx --with-http_ssl_module
shell> make
shell> make install
shell> cd /usr/local/nginx/
shell> mkdir /usr/local/www
shell> groupadd www
shell> useradd -r -g www -s /bin/false www
shell> chown -R www.www /usr/local/www/
shell> cd /usr/local/www/
```

