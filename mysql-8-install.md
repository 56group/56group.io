---
title: MySQL 8 安装
date: 2020-12-25 15:35:15
tags: [安装, MYSQL]
categories:  MYSQL
---

### MySQL 8安装

##### MySQL数据库环境安装

```sh
shell> yum install libaio-devel numactl openssl openssl-devel -y
shell> tar -xvf mysql-8.0.22-linux-glibc2.12-x86_64.tar.xz
shell> ln -s /data/mysql-8.0.22-linux-glibc2.12-x86_64 /usr/local/mysql
shell> cd /usr/local/mysql/
shell> groupadd mysql
shell> useradd -r -g mysql -s /bin/false mysql
shell> mkdir mysql-files data
shell> chmod 750 mysql-files
shell> chown -R mysql .
shell> chgrp -R mysql .
shell> bin/mysqld --initialize --lower-case-table-names=1 --user=mysql --datadir=/usr/local/mysql/data/ # 注意初始化密码
shell> bin/mysql_ssl_rsa_setup --datadir=/usr/local/mysql/data/
shell> chown -R root .
shell> chown -R mysql data mysql-files
shell> mv /etc/my.cnf /etc/my.cnf.bak # 可选确定本地有没有本地文件
shell> vi /etc/my.cnf
shell> cp support-files/mysql.server /etc/init.d/mysqld
shell> chmod u+x /etc/init.d/mysqld
shell> systemctl enable mysqld
shell> systemctl restart mysqld
shell> vi /etc/profile.d/env.sh
shell> chmod u+x /etc/profile.d/env.sh
shell> source /etc/profile.d/env.sh
shell> ln -s /usr/lib64/libtinfo.so.6.1 /usr/lib64/libtinfo.so.5 # 根据环境可选
shell> mysql -uroot -p # 填写初始化时候的mysql密码
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'YOUR_NEW_PASS';
mysql> exit
shell> mysql -uroot -pYOUR_PASS
```

##### MySQL配置文件(my.cnf)

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
default-time-zone = '+8:00'
lower_case_table_names = 1 # 根据自己需要更改
#innodb_large_prefix = 1 # 根据自己需要更改
# INNODB
innodb_strict_mode=1
#innodb_file_format=Barracuda # 根据版本配置 6可 7 8不可
#innodb_file_format_max=Barracuda # 根据版本配置 6可 7 8不可
innodb_read_io_threads=4
innodb_write_io_threads=8 # 8 ~ 12
innodb_io_capacity=1000 # HDD:800 ~ 1200 SSD: 10000+
innodb_adaptive_flushing=1 # SSD: 0
innodb_flush_log_at_trx_commit=1
innodb_max_dirty_pages_pct=75
innodb_buffer_pool_dump_at_shutdown=1
innodb_buffer_pool_load_at_startup=1
innodb_flush_neighbors=1 # SSD:0
innodb_log_file_size=1024M # SSD:4G~8G HDD:1G~2G 根据自己需要更改
innodb_purge_threads=1 # SSD:4
innodb_lock_wait_timeout=3
innodb_print_all_deadlocks=1
innodb_buffer_pool_size=4096M # MEM 60%~80% 根据自己需要更改
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

##### 环境配置

```
export MYSQL_HOME=/usr/local/mysql
export PATH=$MYSQL_HOME/bin:$PATH
```
