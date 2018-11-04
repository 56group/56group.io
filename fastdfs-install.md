---
title:  FasfDFS暗黄
date: 2018-11-04 15:18:00
tags: [FastDFS, 安装]
categories:  Linux
---

### FastDFS安装

##### 环境准备

```
shell> yum update -y
shell> yum upgrade -y
shell> yum install gcc wget -y
```
##### 安装

```
shell> wget https://github.com/happyfish100/libfastcommon/archive/V1.0.39.tar.gz
shell> tar zxvf V1.0.39.tar.gz
shell> cd libfastcommon-1.0.39/
shell> .make.sh
shell> ./make.sh install
shell> ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so
shell> ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so
shell> cd ..
shell> wget https://github.com/happyfish100/fastdfs/archive/V5.11.tar.gz
shell> tar zxvf V5.11.tar.gz
shell> cd fastdfs-5.11/
shell> ./make.sh
shell> ./make.sh install
shell> ls /etc/init.d # 目录下包含fdfs_trackerd,fdfs_storaged启动项
shell> ls ls /etc/fdfs/ # 目录包含client.conf.sample  storage.conf.sample  storage_ids.conf.sample  tracker.conf.sample
shell> ls /usr/bin/ | grep fdfs
#fdfs_appender_test
#fdfs_appender_test1
#fdfs_append_file
#fdfs_crc32
#fdfs_delete_file
#fdfs_download_file
#fdfs_file_info
#fdfs_monitor
#fdfs_storaged
#fdfs_test
#fdfs_test1
#fdfs_trackerd
#fdfs_upload_appender
#fdfs_upload_file
#stop.sh
#restart.sh
shell> ln -s /usr/bin/fdfs_trackerd /usr/local/bin/fdfs_trackerd
shell> ln -s /usr/bin/fdfs_storaged /usr/local/bin/fdfs_storaged
shell> ln -s /usr/bin/stop.sh /usr/local/bin/stop.sh
shell> ln -s /usr/bin/restart.sh /usr/local/bin/restart.sh
shell> cd /etc/fdfs/
shell> mv tracker.conf.sample tracker.conf
shell> vi tracker.conf # tracker.conf
shell> mkdir -p /var/fastdfs/tracker
shell> service iptables stop # 或者配置防火墙22122
shell> service fdfs_trackerd start
shell> ls /var/fastdfs/tracker/ # 第一次启动成功存在data和logs两个目录
shell> netstat -npl | grep fdfs # 存在22122端口服务
shell> service fdfs_trackerd stop
shell> chkconfig fdfs_trackerd on
shell> cd /etc/fdfs
shell> cp storage.conf.sample storage.conf
shell> vi storage.conf
shell> mkdir /var/fastdfs/file
shell> mkdir /var/fastdfs/storage
shell> service iptales stop # 或者防火墙配置23000
shell> service fdfs_trackerd start
shell> service fdfs_storaged start
shell> netstat -npl | grep fdfs # 22122和23000端口
shell> chkconfig fdfs_storaged on
# 查看storage和tracker是否通信
shell> /usr/bin/fdfs_monitor /etc/fdfs/storage.conf # active
# 查看文件存储目录
shell> ls /var/fastdfs/file/data/
shell> cd /etc/fdfs
shell> cp client.conf.sample client.conf
shell> vi client.conf
shell> mkdir /var/fastdfs/client
# 测试文件上传
shell> /usr/bin/fdfs_upload_file /etc/fdfs/client.conf /root/hello.jpg 
# group1/M00/00/00/rBF7wlve7MWAYkBMAAFyMfWGgG0950.jpg # 组/磁盘/目录/目录/文件名
shell> ls /var/fastdfs/file/data/00/00/
# rBF7wlve7MWAYkBMAAFyMfWGgG0950.jpg
```
##### 安装nginx

```
shell> cd
shell> yum install gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel -y
shell> wget http://nginx.org/download/nginx-1.15.5.tar.gz
shell> tar zxvf nginx-1.15.5.tar.gz
shell> cd nginx-1.15.5
shell> ./configure --prefix=/usr/local/nginx
shell> make && make install
shell> cd /usr/local/nginx/
shell> sbin/nginx # 启动
shell> vi /etc/rc.local # 添加一行/usr/local/nginx/sbin/nginx
shell> chmod 755 /etc/rc.local
# 查看模块
shell> /usr/local/nginx/sbin/nginx -V # 确定是否有fdfs模块
shell> service iptables stop # 或者设置80防火墙
shell> vi /usr/local/nginx/conf/nginx.conf
# 添加如下信息
#location /file {
#	alias /var/fastdfs/file/data;
#}
shell> /usr/local/nginx/sbin/nginx -s reload
# 访问http://IP/file/00/00/rBF7wlve7MWAYkBMAAFyMfWGgG0950.jpg
```
##### 安装FastDFS的Nginx模块

```
shell> cd
shell> wget https://github.com/happyfish100/fastdfs-nginx-module/archive/V1.20.tar.gz
shell> tar zxvf V1.20.tar.gz
shell> /usr/local/nginx/sbin/nginx -s stop
shell> cd nginx-1.15.5
shell> ln -s /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so
shell> ln -s /usr/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so
shell> ln -s /usr/include/fastcommon /usr/local/include/fastcommon
shell> ln -s /usr/include/fastdfs /usr/local/include/fastdfs
shell> vi ../fastdfs-nginx-module-1.20/src/config # 更改如下项
# CORE_INCS="$CORE_INCS /usr/local/include/fastdfs /usr/local/include/fastcommon"
# CORE_LIBS="$CORE_LIBS -L /usr/lib -lfastcommon -lfdfsclient"
shell> cp /usr/local/include/fastcommon/* /usr/local/include/fastdfs/
shell> cp /usr/local/include/fastcommon/* /usr/local/include/fastdfs/
shell> ./configure --prefix=/usr/local/nginx --add-module=../fastdfs-nginx-module-1.20/src
shell> make && make install
shell> /usr/local/nginx/sbin/nginx -V # 查看包含fastdfs模块
shell> cd ~/fastdfs-nginx-module-1.20/src/
shell> cp mod_fastdfs.conf /etc/fdfs/
shell> vi /etc/fdfs/mod_fastdfs.conf
shell> cd ~/fastdfs-5.11/conf/
shell> cp anti-steal.jpg http.conf mime.types /etc/fdfs/
shell> cd /usr/local/nginx
shell> vi conf/nginx.conf
shell> /usr/local/nginx/sbin/nginx # 启动
# 访问http://39.105.88.191/M00/00/00/rBF7wlve7MWAYkBMAAFyMfWGgG0950.jpg
```
[参考](https://www.cnblogs.com/chiangchou/p/fastdfs.html)

##### tracker.conf (调整如下配置项)

```
# is this config file disabled
# false for enabled
# true for disabled
disabled=false

# bind an address of this host
# empty for bind all addresses of this host
bind_addr=

# the tracker server port
port=22122
# the base path to store data and log files
base_path=/var/fastdfs/tracker

# HTTP port on this tracker server
http.server_port=80
```
##### storage.conf (调整如下配置项)

```
# is this config file disabled
# false for enabled
# true for disabled
disabled=false

# the name of the group this storage server belongs to
#
# comment or remove this item for fetching from tracker server,
# in this case, use_storage_id must set to true in tracker.conf,
# and storage_ids.conf must be configed correctly.
group_name=group1

# the storage server port
port=23000

# the base path to store data and log files
base_path=/var/fastdfs/storage

# store_path#, based 0, if store_path0 not exists, it's value is base_path
# the paths must be exist
store_path0=/var/fastdfs/file
#store_path1=/home/yuqing/fastdfs2

# tracker_server can ocur more than once, and tracker_server format is
#  "host:port", host can be hostname or ip address
tracker_server=IP:22122

# the port of the web server on this storage server
http.server_port=80
```
##### active

```
[2018-11-04 20:47:09] DEBUG - base_path=/var/fastdfs/storage, connect_timeout=30, network_timeout=60, tracker_server_count=1, anti_steal_token=0, anti_steal_secret_key length=0, use_connection_pool=0, g_connection_pool_max_idle_time=3600s, use_storage_id=0, storage server id count: 0

server_count=1, server_index=0

tracker server is 172.17.123.194:22122

group count: 1

Group 1:
group name = group1
disk total space = 40187 MB
disk free space = 36601 MB
trunk free space = 0 MB
storage server count = 1
active server count = 1
storage server port = 23000
storage HTTP port = 80
store path count = 1
subdir count per path = 256
current write server index = 0
current trunk file id = 0

	Storage 1:
		id = 172.17.123.194
		ip_addr = 172.17.123.194 (iZ2ze0ajic0vbsvb8u5lguZ)  ACTIVE # ACTIVE表示通信
		http domain = 
		version = 5.11
		join time = 2018-11-04 20:43:49
		up time = 2018-11-04 20:43:49
		total storage = 40187 MB
		free storage = 36601 MB
		upload priority = 10
		store_path_count = 1
		subdir_count_per_path = 256
		storage_port = 23000
		storage_http_port = 80
		current_write_path = 0
		source storage id = 
		if_trunk_server = 0
		connection.alloc_count = 256
		connection.current_count = 0
		connection.max_count = 0
		total_upload_count = 0
		success_upload_count = 0
		total_append_count = 0
		success_append_count = 0
		total_modify_count = 0
		success_modify_count = 0
		total_truncate_count = 0
		success_truncate_count = 0
		total_set_meta_count = 0
		success_set_meta_count = 0
		total_delete_count = 0
		success_delete_count = 0
		total_download_count = 0
		success_download_count = 0
		total_get_meta_count = 0
		success_get_meta_count = 0
		total_create_link_count = 0
		success_create_link_count = 0
		total_delete_link_count = 0
		success_delete_link_count = 0
		total_upload_bytes = 0
		success_upload_bytes = 0
		total_append_bytes = 0
		success_append_bytes = 0
		total_modify_bytes = 0
		success_modify_bytes = 0
		stotal_download_bytes = 0
		success_download_bytes = 0
		total_sync_in_bytes = 0
		success_sync_in_bytes = 0
		total_sync_out_bytes = 0
		success_sync_out_bytes = 0
		total_file_open_count = 0
		success_file_open_count = 0
		total_file_read_count = 0
		success_file_read_count = 0
		total_file_write_count = 0
		success_file_write_count = 0
		last_heart_beat_time = 2018-11-04 20:46:53
		last_source_update = 1970-01-01 08:00:00
		last_sync_update = 1970-01-01 08:00:00
		last_synced_timestamp = 1970-01-01 08:00:00
```
##### client.conf (调整如下配置项)

```
# the base path to store log files
base_path=/var/fastdfs/client

# tracker_server can ocur more than once, and tracker_server format is
#  "host:port", host can be hostname or ip address
tracker_server=IP:22122
```
##### mod_fdfs.conf

```
# connect timeout in seconds
# default value is 30s
connect_timeout=10

# FastDFS tracker_server can ocur more than once, and tracker_server format is
#  "host:port", host can be hostname or ip address
# valid only when load_fdfs_parameters_from_tracker is true
tracker_server=172.17.123.194:22122

# the port of the local storage server
# the default value is 23000
storage_server_port=23000

# if the url / uri including the group name
# set to false when uri like /M00/00/00/xxx
# set to true when uri like ${group_name}/M00/00/00/xxx, such as group1/M00/xxx
# default value is false
url_have_group_name = false # 如果访问路径中有group**需要为true

# store_path#, based 0, if store_path0 not exists, it's value is base_path
# the paths must be exist
# must same as storage.conf
store_path0=/var/fastdfs/file
```
##### nginx.conf

```
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

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

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }
       
        location ~/M00 {
            ngx_fastdfs_module;
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