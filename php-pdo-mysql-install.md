---
title:  PHP pdo_mysql安装
date: 2020-05-19 17:58:00
tags: [PHP, PDO, 扩展]
categories:  PHP
---

# PHP pdo_mysql安装

### 环境准备

```shell
shell> yum install -y autoconf
shell> yum install -y m4
```

### 安装pdo_mysql扩展

```shell
# 假设php的安装包解压在/usr/local/src/php-7.3.7目录下,php已经安装在/usr/local/php下
# 查看php的扩展模块
shell> /usr/local/php/bin/php -m
shell> cd /usr/local/src/php-7.3.7
shell> cd ext/pdo_mysql
shell> /usr/local/php/bin/phpize
# /usr/local/mysql为mysql安装路径
shell> ./configure --with-php-config=/usr/local/php/bin/php-config --with-pdo-mysql=/usr/local/mysql/
shell> make
shell> make install
# make install后会出现一个安装路径,查看phpinfo中的extension_dir,pdo_mysql.so就在这个目录下
# 查看phpinfo中php配置文件的目录,如果目录下没有php.ini,vi一个php.ini
shell> vi php.ini
shell> /usr/local/php/bin/php -m # 查看pdo_mysql模块
shell> /etc/init.d/php-fpm stop
shell> /etc/init.d/php-fpm start
shell> /usr/local/nginx/sbin/nginx -s stop
shell> /usr/local/nginx/sbin/nginx
# 查看info.php页面,查看pdo_mysql
```

### 添加扩展信息

```
extension=pdo_mysql.so
```

### info.php (http://xxx/info.php)

```php
<?php
  phpinfo();
?>
```
