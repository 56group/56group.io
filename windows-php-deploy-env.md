---
title: PHP Windows发布环境安装
date: 2018-06-21 13:03:00
tags: [PHP, Windows, 环境]
categories: PHP
---
### Windows PHP发不环境安装

> Windows Server 2003 R2可以安装PHP 5.6.x版本的PHP环境, Windows Server 2012 R2可以安装PHP 7.2.x版本的PHP环境, 因为不同Server版本的vc++环境不一样, 2003 R2安装VC++2012, 2012 R2安装VC++2015

##### 下载PHP,下载Nginx,下载MySQL,下载VC++

[PHP 5.6.36](http://php.net/downloads.php#v5.6.36)
[PHP 7.2.6](http://php.net/downloads.php#v7.2.6)
[Nginx 1.15.0](http://nginx.org/en/download.html)
[MySQL 5.7.22](https://dev.mysql.com/downloads/mysql/5.7.html#downloads)
[VC++ 2012](https://www.microsoft.com/en-us/download/details.aspx?id=30679)
[VC++ 2015](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

> PHP和Nginx和MySQL下载压缩包,解压到对应的安装目录, VC++下载对应服务器版本的对应版本,然后正常安装

##### PHP文件配置

- 1. 复制php.ini-development为php.ini
- 2. 更改php.ini,打开相关的扩展和配置

```
# php.ini配置更改
extension=php_openssl.dll
extension=php_pdo_mysql.dll
extension_dir = "./ext"
```
##### MySQL数据库安装

- 1. 使用管理员打开cmd,切换到MySQL解压目录,运行**mysqld.exe install**
- 2. 更改数据库配置,在MySQL解压目录下新建一个my.ini文件,添加相关配置

```
[client]
socket=D:/lxxhd/mysql-5.7.22/mysql.sock
[mysqld]
datadir=D:/lxxhd/mysql-5.7.22/data
socket=D:/lxxhd/mysql-5.7.22/mysql.sock
user=mysql
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# LOG
slow_query_log = 1
slow_query_log_file = D:/lxxhd/mysql-5.7.22/mysql-slow.log
long_query_time = 2
# GENERAL LOG
general_log = 1
general_log_file = D:/lxxhd/mysql-5.7.22/mysql-general.log
# BINARY LOG
server_id=101
log_bin=D:/lxxhd/mysql-5.7.22/mysql-bin.log
binlog_format=ROW
sync_binlog=1
expire_logs_days=7
# ERROR LOG
log_error=D:/lxxhd/mysql-5.7.22/mysql.err
# OTHER
character_set_server = utf8
transaction-isolation = READ-COMMITTED
max_connections = 1000
log-queries-not-using-indexes
log_throttle_queries_not_using_indexes = 10
# INNODB
innodb_strict_mode=1
innodb_file_format=Barracuda
innodb_file_format_max=Barracuda
innodb_read_io_threads=4
innodb_write_io_threads=8 # 8 ~ 12
innodb_io_capacity=1000 # HDD:800 ~ 1200 SSD: 10000+
innodb_adaptive_flushing=1 # SSD: 0
innodb_flush_log_at_trx_commit=1
innodb_max_dirty_pages_pct=75
innodb_buffer_pool_dump_at_shutdown=1
innodb_buffer_pool_load_at_startup=1
innodb_flush_neighbors=1 # SSD:0
innodb_log_file_size=1024M # SSD:4G~8G HDD:1G~2G
innodb_purge_threads=1 # SSD:4
innodb_lock_wait_timeout=3
innodb_print_all_deadlocks=1
innodb_buffer_pool_size=4096M # MEM 60%~80%
[mysqld_safe]
log-error=D:/lxxhd/mysql-5.7.22/log/mysqld.log
pid-file=D:/lxxhd/mysql-5.7.22/log/mysqld.pid
```
- 3. 在MySQL解压目录的cmd命令行中init数据库,运行**mysqld.exe --initialize-insecure**命令
- 4. 这个时候数据库的密码为'',运行**ALTER USER  'root'@'localhost' IDENTIFIED BY 'YOUR_NEW_PASS';**命令更改密码
- 5. 使用**net start mysql**启动服务
- 6. 配置MySQL环境变量

##### Nginx服务安装和配置

- 1. 解压安装Nginx
- 2. 运行nginx.exe文件,然后通过**tasklist /fi  "imagename eq nginx.exe"**命令查看nginx进程, 访问**http://localhost**查看nginx页面(注:需要注意访问端口的配置)
- 3. 加入PHP配置文件,更改nginx.conf加入php关联配置

```
# nginx配置文件

#user  nobody;
worker_processes  1;

error_log  logs/error.log;
error_log  logs/error.log  notice;
error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format access '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"';
    log_format main '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    #server {  
    #    listen       80 default_server;
    #    server_name  _;  
  
    #    return 444;  
    #}

    server {
        listen       8080;
        server_name  localhost;
        # client_max_body_size 100m;

        #charset koi8-r;
        charset utf-8;
        # root D:/lxxhd/www/lxxhd/public;

        access_log  logs/lxxhd.access.log  main;

        location / {
            # try_files $uri $uri/ /index.php?$query_string;
            # index index.php index.html index.htm;
            root D:/lxxhd/www;
            index index.php index.html index.htm;
        }

        error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        location ~ \.php$ { 
            fastcgi_pass 127.0.0.1:9000;
	    fastcgi_index index.php;
            #include fastcgi.conf;
            fastcgi_split_path_info       ^(.+\.php)(/.+)$;    
            fastcgi_param PATH_INFO       $fastcgi_path_info;    
            fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;    
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;    
            include                       fastcgi_params;
        }

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        location ~ /\.ht {
            deny  all;
        }
    }
}
```
- 4. 在www目录下编写index.php文件

```
<?php
    phpinfo();
?>
```
- 5. 启动php服务

```
D:/lxxhd/php-5.6.36/php-cgi.exe -b 127.0.0.1:9000 -c D:/lxxhd/php-5.6.36/php.ini
```
- 6. 重启nginx服务(可以通过进程管理停止nginx服务,然后运行nginx.exe),访问**http://localhost**查看phpinfo()信息

##### 编写bat脚本

> 下载[RunHiddenConsole.exe](https://redmine.lighttpd.net/attachments/660/RunHiddenConsole.zip)

```
# start.bat
REM REM是bat文件的注释类似于php的//
REM 设置不输出命令
@ECHO off
REM 设置Nginx和php-cgi的目录
SET php_home=PHP安装目录
SET nginx_home=NGINX安装目录

REM 输出状态
ECHO Starting PHP FastCGI...
REM 启动php-cgi -b 端口 -c php.ini位置
REM %php_home%为获取上面set的php_home的值
RunHiddenConsole %php_home%php-cgi.exe -b 127.0.0.1:9000 -c %php_home%php.ini
REM 输出状态
ECHO Starting nginx...
REM 启动Nginx -p Nginx的根目录
RunHiddenConsole %nginx_home%nginx.exe -p %nginx_home%
```
```
# stop.bat
@ECHO off ECHO Stopping nginx...
REM 结束进程 /F 强制终止 /IM 指定的进程
TASKKILL /F /IM nginx.exe
ECHO Stopping PHP FastCGI...
TASKKILL /F /IM php-cgi.exe
REM 关闭窗口
EXIT
```
> start.bat和stop.bat和RunHiddenConsole.exe放在同一个目录下,使用start.bat启动服务

> 注:nginx的worker_processes要与cpu的数量一致

##### NGINX Laravel发布配置

```

#user  nobody;
worker_processes  32;

error_log  logs/error.log;
error_log  logs/error.log  notice;
error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format access '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"';
    log_format main '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    #server {  
    #    listen       80 default_server;
    #    server_name  _;  
  
    #    return 444;  
    #}

    server {
        listen       8080;
        server_name  localhost;
        client_max_body_size 100m;

        #charset koi8-r;
        charset utf-8;
        root D:/lxxhd/www/lxxhd/public;

        access_log  logs/lxxhd.access.log  main;

        location / {
            try_files $uri $uri/ /index.php?$query_string;
            index index.php index.html index.htm;
        }

        error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        location ~ \.php$ { 
            fastcgi_pass 127.0.0.1:9000;
	    fastcgi_index index.php;
            #include fastcgi.conf;
            fastcgi_split_path_info       ^(.+\.php)(/.+)$;    
            fastcgi_param PATH_INFO       $fastcgi_path_info;    
            fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;    
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;    
            include                       fastcgi_params;
        }

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        location ~ /\.ht {
            deny  all;
        }
    }
}
```