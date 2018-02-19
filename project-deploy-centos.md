---
title:  Tomcat项目部署环境安装
date: 2017-09-30 16:29:00
tags: [Tomcat, 环境]
categories:  Linux
---

# project-deploy-centos

### 基础环境

```
System: CentOS 6.9 x86_64
Install: MySQL 5.7, JDK 1.8, Tomcat 8, nginx 1.8.1
```
### MySQL Install

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

### JDK安装

```
# 上传或者下载jdk安装包
shell> tar zxvf jdk-8u144-linux-x64.tar.gz -C /usr/local/
shell> cd /usr/local/
shell> ln -s /usr/local/jdk1.8.0_144/ /usr/local/jdk
```
### Tomcat安装

```
shell> tar zxvf apache-tomcat-8.5.20.tar.gz -C /usr/local/
shell> cd /usr/local/
shell> ln -s /usr/local/apache-tomcat-8.5.20/ /usr/local/tomcat
shell> vim /etc/profile.d/product.sh # 添加product.sh内容
shell> source /etc/profile.d/product.sh
```
### product.sh

```
export JAVA_HOME=/usr/local/jdk
export MYSQL_HOME=/usr/local/mysql
export CATALINA_HOME=/usr/local/tomcat
export PATH=$JAVA_HOME/bin:$MYSQL_HOME/bin:$CATALINA_HOME/bin:$PATH
```
