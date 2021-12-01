---
title: 云竹平台服务环境安装
date: 2021-07-15 17:45:00
tags: [SEATA, MYSQL, FASTDFS, NACOS, INSTALL]
categories:  安装
---

# 云竹平台服务环境安装

### Docker服务安装

##### 添加DNS

```
shell> vi /etc/resolv.conf
# nameserver 114.114.114.114
```

##### 安装docker

```
shell> yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
shell> yum install docker-ce docker-ce-cli containerd.io
shell> systemctl start docker
shell> systemctl enable docker
```

##### 配置docker

```
shell> mkdir -p /etc/docker 
shell> tee /etc/docker/daemon.json <<-'EOF' 
{ 
  "registry-mirrors": ["https://ws7edg6v.mirror.aliyuncs.com"] 
}
EOF 
shell> systemctl daemon-reload 
shell> systemctl restart docker
```

### MySQL服务安装

##### centos8环境

```
shell> docker container run -d --name="mysql" --privileged="true" -p 3306:3306 -v /data/mysql:/data centos:centos8 /sbin/init
shell> docker exec -it mysql /bin/bash
```

##### mysql安装

```
shell> yum install libaio-devel numactl openssl openssl-devel -y
shell> tar -xvf mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz
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

##### my.cnf

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
# general_log = 1
# general_log_file = /usr/local/mysql/mysql-files/mysql-general.log
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
lower_case_table_names = 1
# INNODB
innodb_strict_mode=1
innodb_read_io_threads=4
innodb_write_io_threads=8 # 8 ~ 12
innodb_io_capacity=1000 # HDD:800 ~ 1200 SSD: 10000+
innodb_adaptive_flushing=1 # SSD: 0
innodb_flush_log_at_trx_commit=1
innodb_max_dirty_pages_pct=75
innodb_buffer_pool_dump_at_shutdown=1
innodb_buffer_pool_load_at_startup=1
innodb_flush_neighbors=1 # SSD:0
innodb_log_file_size=1024M
innodb_purge_threads=1
innodb_lock_wait_timeout=3
innodb_print_all_deadlocks=1
innodb_buffer_pool_size=1024
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

##### env.sh

```
export MYSQL_HOME=/usr/local/mysql
export PATH=$MYSQL_HOME/bin:$PATH
```

### Redis服务安装

##### 配置文件

```
shell> mkdir -p /data/conf
shell> cd /data/conf
shell> vi redis.conf
```

##### redis.conf

```
requirepass yunzhu
maxmemory 1G
```

##### redis安装

```
shell> docker run --name="redis" -p 6379:6379 -v /data/conf/redis.conf:/etc/redis/redis.conf -v /data/redis:/data -d redis redis-server /etc/redis/redis.conf --appendonly yes
```

### MongoDB服务安装

##### mongodb安装

```
shell> mkdir /data/mongodb
shell> docker run -d -m 1G --memory-swap -1 -p 27017:27017 --name="mongodb" -v /data/mongodb:/data/db -e MONGO_INITDB_ROOT_USERNAME=yunzhu -e MONGO_INITDB_ROOT_PASSWORD=yunzhu mongo --auth
```

##### mongodb用户配置

```
shell> docker exec -it mongodb /bin/bash
shell> mongo -u yunzhu -p yunzhu
mongo> use YOUR_DB
mongo> db.createUser(
   {
     user: "YOUR_USER",
     pwd: "YOUR_PASS",
     roles: [ "YOUR_ROLE" ]
   }
)
```

##### mongodb连接字符串

```
mongodb://yunzhu:yunzhu@120.46.145.155:27017/?authSource=admin&readPreference=primary&appname=MongoDB%20Compass&ssl=false
```

### FastDFS服务安装

##### centos8环境

```
shell> docker container run -d --name="fastdfs" --privileged="true" -p 3306:3306 -v /data/mysql:/data centos:centos8 /sbin/init
shell> docker exec -it mysql /bin/bash
```

##### docker目录

```
shell> mkdir -p /data/fastdfs/storage/store
shell> mkdir -p /data/fastdfs/tracker
```

##### tracker安装

```
shell> docker run -ti -d --name tracker -v /data/fastdfs/tracker:/fastdfs/tracker/data --net=host season/fastdfs:1.2 tracker
```

##### storage安装

```
shell> docker run -ti --name storage -v /data/fastdfs/storage:/fastdfs/storage/data -v /data/fastdfs/storage/store:/fastdfs/store_path --net=host -e TRACKER_SERVER=120.46.145.155:22122 season/fastdfs:1.2 storage
shell> docker cp storage:/fdfs_conf/storage.conf .
shell> vi storage.conf # 更改tracker IP
shell> docker cp storage.conf storage:/fdfs_conf
```

##### storage.conf

```
# is this config file disabled
# false for enabled
# true for disabled
disabled=false

# the name of the group this storage server belongs to
group_name=group1

# bind an address of this host
# empty for bind all addresses of this host
bind_addr=

# if bind an address of this host when connect to other servers 
# (this storage server as a client)
# true for binding the address configed by above parameter: "bind_addr"
# false for binding any address of this host
client_bind=true

# the storage server port
port=23000

# connect timeout in seconds
# default value is 30s
connect_timeout=30

# network timeout in seconds
# default value is 30s
network_timeout=60

# heart beat interval in seconds
heart_beat_interval=30

# disk usage report interval in seconds
stat_report_interval=60

# the base path to store data and log files
base_path=/fastdfs/storage

# max concurrent connections the server supported
# default value is 256
# more max_connections means more memory will be used
max_connections=256

# the buff size to recv / send data
# this parameter must more than 8KB
# default value is 64KB
# since V2.00
buff_size = 256KB

# accept thread count
# default value is 1
# since V4.07
accept_threads=1

# work thread count, should <= max_connections
# work thread deal network io
# default value is 4
# since V2.00
work_threads=4

# if disk read / write separated
##  false for mixed read and write
##  true for separated read and write
# default value is true
# since V2.00
disk_rw_separated = true

# disk reader thread count per store base path
# for mixed read / write, this parameter can be 0
# default value is 1
# since V2.00
disk_reader_threads = 1

# disk writer thread count per store base path
# for mixed read / write, this parameter can be 0
# default value is 1
# since V2.00
disk_writer_threads = 1

# when no entry to sync, try read binlog again after X milliseconds
# must > 0, default value is 200ms
sync_wait_msec=50

# after sync a file, usleep milliseconds
# 0 for sync successively (never call usleep)
sync_interval=0

# storage sync start time of a day, time format: Hour:Minute
# Hour from 0 to 23, Minute from 0 to 59
sync_start_time=00:00

# storage sync end time of a day, time format: Hour:Minute
# Hour from 0 to 23, Minute from 0 to 59
sync_end_time=23:59

# write to the mark file after sync N files
# default value is 500
write_mark_file_freq=500

# path(disk or mount point) count, default value is 1
store_path_count=1

# store_path#, based 0, if store_path0 not exists, it's value is base_path
# the paths must be exist
store_path0=/fastdfs/store_path
#store_path1=/home/yuqing/fastdfs2

# subdir_count  * subdir_count directories will be auto created under each 
# store_path (disk), value can be 1 to 256, default value is 256
subdir_count_per_path=256

# tracker_server can ocur more than once, and tracker_server format is
#  "host:port", host can be hostname or ip address
tracker_server=120.46.145.155:22122

#standard log level as syslog, case insensitive, value list:
### emerg for emergency
### alert
### crit for critical
### error
### warn for warning
### notice
### info
### debug
log_level=info

#unix group name to run this program, 
#not set (empty) means run by the group of current user
run_by_group=

#unix username to run this program,
#not set (empty) means run by current user
run_by_user=

# allow_hosts can ocur more than once, host can be hostname or ip address,
# "*" means match all ip addresses, can use range like this: 10.0.1.[1-15,20] or
# host[01-08,20-25].domain.com, for example:
# allow_hosts=10.0.1.[1-15,20]
# allow_hosts=host[01-08,20-25].domain.com
allow_hosts=*

# the mode of the files distributed to the data path
# 0: round robin(default)
# 1: random, distributted by hash code
file_distribute_path_mode=0

# valid when file_distribute_to_path is set to 0 (round robin), 
# when the written file count reaches this number, then rotate to next path
# default value is 100
file_distribute_rotate_count=100

# call fsync to disk when write big file
# 0: never call fsync
# other: call fsync when written bytes >= this bytes
# default value is 0 (never call fsync)
fsync_after_written_bytes=0

# sync log buff to disk every interval seconds
# must > 0, default value is 10 seconds
sync_log_buff_interval=10

# sync binlog buff / cache to disk every interval seconds
# default value is 60 seconds
sync_binlog_buff_interval=10

# sync storage stat info to disk every interval seconds
# default value is 300 seconds
sync_stat_file_interval=300

# thread stack size, should >= 512KB
# default value is 512KB
thread_stack_size=512KB

# the priority as a source server for uploading file.
# the lower this value, the higher its uploading priority.
# default value is 10
upload_priority=10

# the NIC alias prefix, such as eth in Linux, you can see it by ifconfig -a
# multi aliases split by comma. empty value means auto set by OS type
# default values is empty
if_alias_prefix=

# if check file duplicate, when set to true, use FastDHT to store file indexes
# 1 or yes: need check
# 0 or no: do not check
# default value is 0
check_file_duplicate=0

# file signature method for check file duplicate
## hash: four 32 bits hash code
## md5: MD5 signature
# default value is hash
# since V4.01
file_signature_method=hash

# namespace for storing file indexes (key-value pairs)
# this item must be set when check_file_duplicate is true / on
key_namespace=FastDFS

# set keep_alive to 1 to enable persistent connection with FastDHT servers
# default value is 0 (short connection)
keep_alive=0

# you can use "#include filename" (not include double quotes) directive to 
# load FastDHT server list, when the filename is a relative path such as 
# pure filename, the base path is the base path of current/this config file.
# must set FastDHT server list when check_file_duplicate is true / on
# please see INSTALL of FastDHT for detail
##include /home/yuqing/fastdht/conf/fdht_servers.conf

# if log to access log
# default value is false
# since V4.00
use_access_log = false

# if rotate the access log every day
# default value is false
# since V4.00
rotate_access_log = false

# rotate access log time base, time format: Hour:Minute
# Hour from 0 to 23, Minute from 0 to 59
# default value is 00:00
# since V4.00
access_log_rotate_time=00:00

# if rotate the error log every day
# default value is false
# since V4.02
rotate_error_log = false

# rotate error log time base, time format: Hour:Minute
# Hour from 0 to 23, Minute from 0 to 59
# default value is 00:00
# since V4.02
error_log_rotate_time=00:00

# rotate access log when the log file exceeds this size
# 0 means never rotates log file by log file size
# default value is 0
# since V4.02
rotate_access_log_size = 0

# rotate error log when the log file exceeds this size
# 0 means never rotates log file by log file size
# default value is 0
# since V4.02
rotate_error_log_size = 0

# if skip the invalid record when sync file
# default value is false
# since V4.02
file_sync_skip_invalid_record=false

# if use connection pool
# default value is false
# since V4.05
use_connection_pool = false

# connections whose the idle time exceeds this time will be closed
# unit: second
# default value is 3600
# since V4.05
connection_pool_max_idle_time = 3600

# use the ip address of this storage server if domain_name is empty,
# else this domain name will ocur in the url redirected by the tracker server
http.domain_name=

# the port of the web server on this storage server
http.server_port=8888
```

##### fastdfs_shell安装

```
shell> docker run -itd --name fdfs_sh --net=host season/fastdfs:1.2 shell
```

##### 测试

```
shell> docker cp storage.conf fdfs_sh:/fdfs_conf/
shell> docker exec -it fdfs_sh bash
shell> touch a.txt
shell> cd fdfs_conf/ && fdfs_upload_file storage.conf /a.txt # group1/M00/00/00/wKgAe2DqykCAY-r8AAAAAAAAAAA999.txt
```

##### nginx安装

```
shell> cd /data/conf/
shell> vi nginx.conf
shell> docker run -it -d --name fdfs_nginx -v /data/fastdfs/storage:/fastdfs/storage/data -v /data/fastdfs/storage/store:/fastdfs/store_path -v /data/conf/nginx.conf:/etc/nginx/conf/nginx.conf --net=host season/fastdfs:1.2 nginx
shell> docker cp fdfs_nginx:/fdfs_conf/mod_fastdfs.conf .
shell> vi mod_fastdfs.conf # 更改tracker IP
shell> docker container stop fdfs_nginx
shell> docker cp ~/mod_fastdfs.conf fdfs_nginx:/fdfs_conf/
shell> docker container start fdfs_nginx
# http://120.46.145.155:7003/group1/M00/00/00/wKgAe2DqykCAY-r8AAAAAAAAAAA999.txt
```

##### mod_fastdfs.conf

````
# connect timeout in seconds
# default value is 30s
connect_timeout=2

# network recv and send timeout in seconds
# default value is 30s
network_timeout=30

# the base path to store log files
base_path=/tmp

# if load FastDFS parameters from tracker server
# since V1.12
# default value is false
load_fdfs_parameters_from_tracker=true

# storage sync file max delay seconds
# same as tracker.conf
# valid only when load_fdfs_parameters_from_tracker is false
# since V1.12
# default value is 86400 seconds (one day)
storage_sync_file_max_delay = 86400

# if use storage ID instead of IP address
# same as tracker.conf
# valid only when load_fdfs_parameters_from_tracker is false
# default value is false
# since V1.13
use_storage_id = false

# specify storage ids filename, can use relative or absolute path
# same as tracker.conf
# valid only when load_fdfs_parameters_from_tracker is false
# since V1.13
storage_ids_filename = storage_ids.conf

# FastDFS tracker_server can ocur more than once, and tracker_server format is
#  "host:port", host can be hostname or ip address
# valid only when load_fdfs_parameters_from_tracker is true
tracker_server=120.46.145.155:22122

# the port of the local storage server
# the default value is 23000
storage_server_port=23000

# the group name of the local storage server
group_name=group1

# if the url / uri including the group name
# set to false when uri like /M00/00/00/xxx
# set to true when uri like ${group_name}/M00/00/00/xxx, such as group1/M00/xxx
# default value is false
url_have_group_name=true

# path(disk or mount point) count, default value is 1
# must same as storage.conf
store_path_count=1

# store_path#, based 0, if store_path0 not exists, it's value is base_path
# the paths must be exist
# must same as storage.conf
store_path0=/fastdfs/store_path
#store_path1=/home/yuqing/fastdfs1

# standard log level as syslog, case insensitive, value list:
### emerg for emergency
### alert
### crit for critical
### error
### warn for warning
### notice
### info
### debug
log_level=info

# set the log filename, such as /usr/local/apache2/logs/mod_fastdfs.log
# empty for output to stderr (apache and nginx error_log file)
log_filename=

# response mode when the file not exist in the local file system
## proxy: get the content from other storage server, then send to client
## redirect: redirect to the original storage server (HTTP Header is Location)
response_mode=proxy

# the NIC alias prefix, such as eth in Linux, you can see it by ifconfig -a
# multi aliases split by comma. empty value means auto set by OS type
# this paramter used to get all ip address of the local host
# default values is empty
if_alias_prefix=

# use "#include" directive to include HTTP config file
# NOTE: #include is an include directive, do NOT remove the # before include
#include http.conf


# if support flv
# default value is false
# since v1.15
flv_support = true

# flv file extension name
# default value is flv
# since v1.15
flv_extension = flv


# set the group count
# set to none zero to support multi-group
# set to 0  for single group only
# groups settings section as [group1], [group2], ..., [groupN]
# default value is 0
# since v1.14
group_count = 0

# group settings for group #1
# since v1.14
# when support multi-group, uncomment following section
#[group1]
#group_name=group1
#storage_server_port=23000
#store_path_count=2
#store_path0=/home/yuqing/fastdfs
#store_path1=/home/yuqing/fastdfs1

# group settings for group #2
# since v1.14
# when support multi-group, uncomment following section as neccessary
#[group2]
#group_name=group2
#storage_server_port=23000
#store_path_count=1
#store_path0=/home/yuqing/fastdfs
````

##### nginx.conf

```
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

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;

    sendfile        on;

    keepalive_timeout  65;

    server {
        listen       7003;
        server_name  192.168.0.5;

        #charset koi8-r;

        access_log  logs/host.access.log  main;

        location /group1/M00 {
            root /fastdfs/storage/data/store/data;
            ngx_fastdfs_module;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

### Nacos服务安装

##### docker-compose安装

```
shell> curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
shell> chmod +x /usr/local/bin/docker-compose
shell> ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

##### 准备数据库和安装nacos

```
shell> yum install git -y
shell> cd /data
shell> git clone https://github.com/nacos-group/nacos-docker.git
shell> docker exec mysql -it /bin/bash
shell> mysql -uroot -pYOUR_PASS
mysql> CREATE SCHEMA `nacos_yunzhu` DEFAULT CHARACTER SET utf8 COLLATE utf8_bin;
mysql> CREATE USER 'nacos'@'%' IDENTIFIED BY 'nacos@zhulin';
msyql> GRANT ALL PRIVILEGES ON nacos_yunzhu.* TO 'nacos'@'%' WITH GRANT OPTION;
mysql> use nacos_yunzhu
# 导入这个位置的数据库 https://github.com/alibaba/nacos/blob/develop/distribution/conf/nacos-mysql.sql
mysql> exit
shell> exit
shell> nacos-docker/env
shell> vi nacos-standlone-mysql.env
shell> cd ../example/
shell> vi standalone-mysql-8.yaml
shell> docker-compose -f example/standalone-mysql-8.yaml up -d
```

##### nacos-standlone-mysql.env

```
PREFER_HOST_MODE=ip
MODE=standalone
SPRING_DATASOURCE_PLATFORM=mysql
MYSQL_SERVICE_HOST=120.46.145.155
MYSQL_SERVICE_DB_NAME=nacos_yunzhu
MYSQL_SERVICE_PORT=3306
MYSQL_SERVICE_USER=nacos
MYSQL_SERVICE_PASSWORD=nacos@zhulin
MYSQL_SERVICE_DB_PARAM=characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useSSL=false
```

##### standalone-mysql-8.yaml

```
version: "2"
services:
  nacos:
    image: nacos/nacos-server:${NACOS_VERSION}
    container_name: nacos-standalone-mysql
    env_file:
      - ../env/nacos-standlone-mysql.env
    volumes:
      - ./standalone-logs/:/home/nacos/logs
      - ./init.d/custom.properties:/home/nacos/init.d/custom.properties
    ports:
      - "8848:8848"
      - "9848:9848"
      - "9555:9555"
    restart: always
```

### Seata服务安装

###### [下载seata-server源代码](https://seata.io/zh-cn/blog/download.html)

##### mysql

```
# 上传sql文件到数据库服务器
shell> docker cp mysql_init.sql mysql:/
shell> docker exec -it mysql /bin/bash
shell> mysql -uroot -pYOUR_PASS
mysql> CREATE SCHEMA `seata_yunzhu` DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
mysql> CREATE USER 'seata'@'%' IDENTIFIED BY 'seata@zhulin';
mysql> GRANT ALL PRIVILEGES ON seata_yunzhu.* TO 'seata'@'%' WITH GRANT OPTION;
mysql> use seata_yunzhu
mysql> source mysql_init.sql
```

##### script/server/db/mysql.sql

```
-- -------------------------------- The script used when storeMode is 'db' --------------------------------
-- the table to store GlobalSession data
CREATE TABLE IF NOT EXISTS `global_table`
(
    `xid`                       VARCHAR(128) NOT NULL,
    `transaction_id`            BIGINT,
    `status`                    TINYINT      NOT NULL,
    `application_id`            VARCHAR(32),
    `transaction_service_group` VARCHAR(32),
    `transaction_name`          VARCHAR(128),
    `timeout`                   INT,
    `begin_time`                BIGINT,
    `application_data`          VARCHAR(2000),
    `gmt_create`                DATETIME,
    `gmt_modified`              DATETIME,
    PRIMARY KEY (`xid`),
    KEY `idx_gmt_modified_status` (`gmt_modified`, `status`),
    KEY `idx_transaction_id` (`transaction_id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

-- the table to store BranchSession data
CREATE TABLE IF NOT EXISTS `branch_table`
(
    `branch_id`         BIGINT       NOT NULL,
    `xid`               VARCHAR(128) NOT NULL,
    `transaction_id`    BIGINT,
    `resource_group_id` VARCHAR(32),
    `resource_id`       VARCHAR(256),
    `branch_type`       VARCHAR(8),
    `status`            TINYINT,
    `client_id`         VARCHAR(64),
    `application_data`  VARCHAR(2000),
    `gmt_create`        DATETIME(6),
    `gmt_modified`      DATETIME(6),
    PRIMARY KEY (`branch_id`),
    KEY `idx_xid` (`xid`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;

-- the table to store lock data
CREATE TABLE IF NOT EXISTS `lock_table`
(
    `row_key`        VARCHAR(128) NOT NULL,
    `xid`            VARCHAR(128),
    `transaction_id` BIGINT,
    `branch_id`      BIGINT       NOT NULL,
    `resource_id`    VARCHAR(256),
    `table_name`     VARCHAR(100),
    `pk`             VARCHAR(100),
    `gmt_create`     DATETIME,
    `gmt_modified`   DATETIME,
    PRIMARY KEY (`row_key`),
    KEY `idx_branch_id` (`branch_id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8;
```

##### 修改script/config-center/config.txt

```
store.mode=db
store.db.dbType=mysql
store.db.driverClassName=com.mysql.cj.jdbc.Driver
store.db.url=jdbc:mysql://YOUR_IP:3306/YOUR_DB?useUnicode=true&rewriteBatchedStatements=true
store.db.user=YOUR_USER
store.db.password=YOUR_PASS
service.vgroupMapping.yunzhu_tx_group=default
service.default.grouplist=YOUR_IP:8091
# 因为我们用的是mysql不是redis所以redis可以不配置
store.redis.single.host=120.46.145.155
store.redis.password=yunzhu
store.redis.database=5
```

##### Nacos配置

- 1.新建一个seata的命名空间

##### 上传seata配置到nacos

```
# 在script/config-center/nacos目录下
shell> cd script/config-center/nacos
shell> chmod u+x nacos-config.sh
shell> sh nacos-config.sh -h YOUR_IP -p 8848 -g yunzhu -t YOUR_NAMESPACE_ID -u nacos -w YOUR_PASS
# nacos配置中心seata空间内用配置信息
```

#####  registry.conf

```
registry {
  # file,nacos,eureka,redis,zk,consul,etcd3,sofa
  type = "nacos"

  nacos {
    application = "seata-server"
    serverAddr = "120.46.145.155:8848"
    group = "yunzhu"
    namespace = "128d127f-4008-4cf0-85c1-8050aa7eca2b"
    cluster = "default"
    username = "nacos"
    password = "Zzmxqsm123456"
  }
  eureka {
    serviceUrl = "http://localhost:8761/eureka"
    application = "default"
    weight = "1"
  }
  redis {
    serverAddr = "localhost:6379"
    db = 0
    password = ""
    cluster = "default"
    timeout = 0
  }
  zk {
    cluster = "default"
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  consul {
    cluster = "default"
    serverAddr = "127.0.0.1:8500"
  }
  etcd3 {
    cluster = "default"
    serverAddr = "http://localhost:2379"
  }
  sofa {
    serverAddr = "127.0.0.1:9603"
    application = "default"
    region = "DEFAULT_ZONE"
    datacenter = "DefaultDataCenter"
    cluster = "default"
    group = "SEATA_GROUP"
    addressWaitTime = "3000"
  }
  file {
    name = "file.conf"
  }
}

config {
  # file、nacos 、apollo、zk、consul、etcd3
  type = "nacos"

  nacos {
    serverAddr = "120.46.145.155:8848"
    namespace = "128d127f-4008-4cf0-85c1-8050aa7eca2b"
    group = "yunzhu"
    username = "nacos"
    password = "Zzmxqsm123456"
    dataId = "seataServer.properties"
  }
  consul {
    serverAddr = "127.0.0.1:8500"
  }
  apollo {
    appId = "seata-server"
    apolloMeta = "http://192.168.1.204:8801"
    namespace = "application"
    apolloAccesskeySecret = ""
  }
  zk {
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
    nodePath = "/seata/seata.properties"
  }
  etcd3 {
    serverAddr = "http://localhost:2379"
  }
  file {
    name = "file.conf"
  }
}
```

##### seata服务docker安装

```
shell> cd /data
shell> mkdir -p seata-server/resources seata-server/logs
shell> cp ~/seata-1.4.2/script/server/config/registry.conf seata-server/resources/
shell> vi docker-compose.yml
shell> docker-compose -f docker-compose.yml up -d
shell> docker logs -f seata-server
```

##### docker-compose.yml

```
version: "3"
services:
  seata-server:
    image: seataio/seata-server
    hostname: seata-server
    container_name: seata-server
    ports:
      - "8091:8091"
    environment:
      - SEATA_PORT=8091
      - STORE_MODE=db
      - SEATA_IP=YOUR_IP
    volumes:
      - "./seata-server/resources/registry.conf:/seata-server/resources/registry.conf"
      - "./seata-server/logs:/root/logs/seata"
```

##### 发布机器的NGINX配置

```
user  www;
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

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  122.9.41.19;

        #charset koi8-r;

        access_log  logs/yunzhu.access.log  main;

        location / {
            root   /usr/local/www/yunzhu;
            index  index.html;
        }
        
        location /api/ {
           proxy_set_header Host $http_host;
	   proxy_set_header X-Real-IP $remote_addr;
	   proxy_set_header REMOTE-HOST $remote_addr;
	   proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	   proxy_pass http://localhost:20000/;
	}
        
        location /oauth/ {
           proxy_set_header Host $http_host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header REMOTE-HOST $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_pass http://localhost:10007;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

    }
}
```

### Sentinel安装

### SkyWalking安装

