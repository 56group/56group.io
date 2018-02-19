---
title:  Linux系统PHP Laravel项目部署
date: 2017-11-21 13:25:00
tags: [PHP, Laravel, 部署]
categories:  PHP
---

# php-laravel-project-deploy

### 系统环境

```
System: Linux CentOS 6.9 x86_64
DB: MySQL 5.7, MongoDB 3.4
EVN: nignx, PHP
```

### MySQL数据库环境安装

```
# 上传或者下载MySQL程序包
shell> tar zxvf mysql-VERSION-linux-glibc2.12-x86_64.tar.gz -C /usr/local/
shell> cd /usr/local
shell> ln -s /usr/local/mysql-5.7.19-linux-glibc2.12-x86_64/ /usr/local/mysql
shell> groupadd mysql
shell> useradd -r -g mysql -s /bin/false mysql
shell> cd mysql
shell> mkdir mysql-files data
shell> chmod 750 mysql-files
shell> chown -R mysql .
shell> chgrp -R mysql .
shell> bin/mysqld --initialize --user=mysql --datadir=/usr/local/mysql/data/ # copy and save tmp password
shell> bin/mysql_ssl_rsa_setup --datadir=/usr/local/mysql/data/
shell> chown -R root .
shell> chown -R mysql data mysql-files
shell> mv /etc/my.cnf /etc/my.cnf.bak
shell> vim /etc/my.cnf # 添加如下my.cnf内容
shell> cp support-files/mysql.server /etc/init.d/mysqld
shell> chmod u+x /etc/init.d/mysqld
shell> chkconfig mysqld on
shell> service mysqld restart
shell> mysql -uroot -pTMP_PASS
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'YOUR_NEW_PASS';
```
### my.cnf

```
[client]
socket=/usr/local/mysql/mysql-files/mysql.sock

[mysqld]
datadir=/usr/local/mysql/data
socket=/usr/local/mysql/mysql-files/mysql.sock
user=mysql

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

# LOG
slow_query_log = 1
slow_query_log_file = /usr/local/mysql/mysql-files/mysql-slow.log
long_query_time = 2

# GENERAL LOG
general_log = 1
general_log_file = /usr/local/mysql/mysql-files/mysql-general.log

# BINARY LOG
server_id=101
log_bin=/usr/local/mysql/mysql-files/mysql-bin.log
binlog_format=ROW
sync_binlog=1
expire_logs_days=7

# ERROR LOG
log_error=/usr/local/mysql/mysql-files/mysql.err

# OTHER
character_set_server = utf8mb4
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
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```
### MongoDB数据库环境安装

```
# 上传mongodb安装包
shell> tar zxvf mongodb-linux-x86_64-rhel62-3.4.10.tgz -C /usr/local/
shell> cd /usr/local
shell> ln -s /usr/local/mongodb-linux-x86_64-rhel62-3.4.10/ /usr/local/mongodb
shell> cd mongodb
shell> mkdir conf data log
shell> vi conf/mongodb.cnf # 添加如下mongodb.cnf文件内容
shell> vi /etc/init.d/mongodb # 添加如下mongodb文件内容
shell> chmod u+x /etc/init.d/mongodb
shell> service mongodb restart
shell> chkconfig mongodb on
```
### PHP环境安装

```
# 上传PHP程序包
shell> tar zxvf php-7.1.11.tar.gz
shell> yum -y install gcc gcc-c++ gcc-g77 autoconf automake fiex libxml libxml-devel libxml2 libxml2-devel ncurses-devel libmcrypt libmcrypt-devel libtool-ltdl-devel gettext gettextdevel bison bison-devel bzip2-devel libcurl-devel curl-devel libjpeg-devel libpng-devel freetype-devel gd gd-devel net-snmp net-snmp-devel openssl openssl-devel
shell> cd php-7.1.11
shell> ./configure --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --with-mysql=mysqlnd --with-mysqli=mysqlnd --enable-fpm --disable-phar --with-fpm-user=www --with-fpm-group=www --with-pcre-regex --with-zlib --with-bz2 --enable-calendar --with-curl --enable-dba --with-libxml-dir --with-gd --with-jpeg-dir --with-png-dir --with-zlib-dir --with-freetype-dir --enable-gd-native-ttf --enable-gd-jis-conv --with-mhash --enable-mbstring --with-mcrypt --enable-pcntl --enable-xml --disable-rpath --enable-shmop --enable-sockets --enable-zip --enable-bcmath --with-snmp --disable-ipv6 --enable-soap --with-openssl
shell> make ZEND_EXTRA_LIBS='-lresolv' # 或 make
shell> make test
shell> make install
shell> cd /usr/local/php/
shell> cp /usr/local/src/php-5.6.5/sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
shell> cp /root/php-5.6.31/sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
shell> chmod 755 /etc/init.d/php-fpm
shell> cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
shell> useradd -s /sbin/nologin -d /usr/local/www/ www
shell> cd etc/php-fpm.d/
shell> cp www.conf.default www.conf
shell> service php-fpm restart
shell> netstat -npl | grep 9000
shell> chkconfig php-fpm on
```
### nginx环境安装

```
shell> tar zxvf nginx-1.13.6.tar.gz
shell> cd nginx-1.13.6
shell> yum install zlib pcre zlib-devel pcre-devel openssl openssl-devel gcc -y
shell> ./configure --prefix=/usr/local/nginx --pid-path=/usr/local/nginx/nginx.pid --with-http_ssl_module
shell> make
shell> make install
shell> mkdir /usr/local/www
shell> chown -R www.www /usr/local/www/
shell> vi /etc/init.d/nginx # 添加如下nginx文件内容
shell> chmod u+x /etc/init.d/nginx
shell> cd /usr/local/nginx/conf/
shell> mv nginx.conf nginx.conf.bak
shell> vi nginx.conf # 添加如下nginx配置
```
### php扩展安装

```
# 安装openssl
shell> cd /usr/local/src/php-7.1.11/ext/openssl/
shell> mv config.mp4 config.m4
shell> /usr/local/php/bin/phpize
shell> ./configure --with-openssl --with-php-config=/usr/local/php/bin/php-config
shell> make
shell> make install
# 安装mongodb, 下载或上传mongodb驱动
shell> tar zxvf mongodb-1.3.2.tgz
shell> cd mongodb-1.3.2
shell> /usr/local/php/bin/phpize
shell> ./configure --with-php-config=/usr/local/php/bin/php-config
shell> make
shell> make install
# 安装pdo_mysql
shell> cd pdo_mysql/
shell> phpize
shell> ./configure --with-php-config=/usr/local/php/bin/php-config
shell> make
shell> make install
shell> vi /usr/local/php/etc/php.ini # 添加php.ini内容
shell> service php-fpm restart
```
### php.ini

```
extension=mongodb.so
extension=openssl.so
extension=pdo_mysql.so
```

### mongodb.cnf

```
port=27017
dbpath=/usr/local/mongodb/data/
logpath=/usr/local/mongodb/log/mongodb.log
logappend=true
fork=true
verbose=true
vvvvv=true
```
### nginx.conf

```
user www;

worker_processes 1;
error_log logs/error.log;
error_log logs/error.log notice;
error_log logs/error.log info;

#pid logs/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include mime.types;
    default_type application/octet-stream;
    log_format access '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"';
    #access_log logs/access.log main;
    sendfile on;
    #tcp_nopush on;
    #keepalive_timeout 0;
    keepalive_timeout 65;
    #gzip on;
    server {
        listen 8000;
        server_name localhost;
        root /usr/local/www;
        charset utf-8;
        
        location / {
            index index.php;
        }
        
        location ~ .*\.(php|php5)?$ {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            include fastcgi.conf;
        }
    }

    server {
	client_max_body_size 100m;
	listen 80;
	server_name localhost;
	root /usr/local/www/dllgb/public;
	charset utf-8;
	#access_log logs/host.access.log main;
        
	location / {
            try_files $uri $uri/ /index.php?$query_string;
            index index.php index.html index.htm;
	}

	location ~ .*\.(js|jpg|png|css|jpeg|gif|bmp) {
	    expires 30d;
	}

	location ~ .*\.(php|php5)?$ {
	    fastcgi_pass 127.0.0.1:9000;
	    fastcgi_index index.php;
            #include fastcgi.conf;
            fastcgi_split_path_info       ^(.+\.php)(/.+)$;    
            fastcgi_param PATH_INFO       $fastcgi_path_info;    
            fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;    
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;    
            include                       fastcgi_params;
	}

        location ~ /\.ht {    
            deny all;    
        }
    }
}
```

### mongodb

```
#!/bin/bash
#
# chkconfig: mongodb on
# description: mongodb

 start() {
  /usr/local/mongodb/bin/mongod -f /usr/local/mongodb/conf/mongodb.cnf
 }

 stop() {
   /usr/local/mongodb/bin/mongod -f /usr/local/mongodb/conf/mongodb.cnf --shutdown
 }

 case "$1" in
   start)
  start
  ;;

 stop)
  stop
  ;;

 restart)
  stop
  start
  ;;
   *)
  echo 
 $"Usage: $0 {start|stop|restart}"
  exit 1
 esac
```
### 配置环境变量

```
export MYSQL_HOME=/usr/local/mysql
export MONGODB_HOME=/usr/local/mongodb
export PHP_HOME=/usr/local/php
export PATH=$MYSQL_HOME/bin:$MONGODB_HOME/bin:$PHP_HOME/bin:$PATH
```
### nginx

```
#!/bin/sh
#
# nginx - this script starts and stops the nginx daemon
#
# chkconfig:   - 85 15
# description:  NGINX is an HTTP(S) server, HTTP(S) reverse \
#               proxy and IMAP/POP3 proxy server
# processname: nginx
# config:      /etc/nginx/nginx.conf
# config:      /etc/sysconfig/nginx
# pidfile:     /var/run/nginx.pid

# Source function library.
. /etc/rc.d/init.d/functions

# Source networking configuration.
. /etc/sysconfig/network

# Check that networking is up.
[ "$NETWORKING" = "no" ] && exit 0

nginx="/usr/local/nginx/sbin/nginx"
prog=$(basename $nginx)

NGINX_CONF_FILE="/usr/local/nginx/conf/nginx.conf"

[ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx

lockfile=/var/lock/subsys/nginx

make_dirs() {
   # make required directories
   user=`$nginx -V 2>&1 | grep "configure arguments:.*--user=" | sed 's/[^*]*--user=\([^ ]*\).*/\1/g' -`
   if [ -n "$user" ]; then
      if [ -z "`grep $user /etc/passwd`" ]; then
         useradd -M -s /bin/nologin $user
      fi
      options=`$nginx -V 2>&1 | grep 'configure arguments:'`
      for opt in $options; do
          if [ `echo $opt | grep '.*-temp-path'` ]; then
              value=`echo $opt | cut -d "=" -f 2`
              if [ ! -d "$value" ]; then
                  # echo "creating" $value
                  mkdir -p $value && chown -R $user $value
              fi
          fi
       done
    fi
}

start() {
    [ -x $nginx ] || exit 5
    [ -f $NGINX_CONF_FILE ] || exit 6
    make_dirs
    echo -n $"Starting $prog: "
    daemon $nginx -c $NGINX_CONF_FILE
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile
    return $retval
}

stop() {
    echo -n $"Stopping $prog: "
    killproc $prog -QUIT
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}

restart() {
    configtest || return $?
    stop
    sleep 1
    start
}

reload() {
    configtest || return $?
    echo -n $"Reloading $prog: "
    killproc $nginx -HUP
    RETVAL=$?
    echo
}

force_reload() {
    restart
}

configtest() {
  $nginx -t -c $NGINX_CONF_FILE
}

rh_status() {
    status $prog
}

rh_status_q() {
    rh_status >/dev/null 2>&1
}

case "$1" in
    start)
        rh_status_q && exit 0
        $1
        ;;
    stop)
        rh_status_q || exit 0
        $1
        ;;
    restart|configtest)
        $1
        ;;
    reload)
        rh_status_q || exit 7
        $1
        ;;
    force-reload)
        force_reload
        ;;
    status)
        rh_status
        ;;
    condrestart|try-restart)
        rh_status_q || exit 0
            ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
        exit 2
esac
```