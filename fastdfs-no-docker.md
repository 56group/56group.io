---
title: FastDFS非Docker安装
date: 2022-06-30 15:03:00
tags: [FASTDFS, INSTALL]
categories: FASTDFS
---

### FastDFS安装

##### libfastcommon安装

[下载](https://gitee.com/fastdfs100/libfastcommon/tags)

```
shell> unzip libfastcommon-V1.0.58.zip
shell> cd libfastcommon-V1.0.58
shell> ./make.sh clean && ./make.sh && ./make.sh install
```

##### libserverframe(V6.09需要)安装

```shell
shell> unzip libserverframe-V1.1.21.zip
shell> cd libserverframe-V1.1.21
shell> ./make.sh clean
shell> ./make.sh
shell> ./make.sh install
```

##### fastdfs安装

[下载](https://gitee.com/fastdfs100/fastdfs/tags)

```
shell> unzip fastdfs-V6.08.zip
shell> cd fastdfs-V6.08
shell> ./make.sh clean && ./make.sh && ./make.sh install
shell> ./setup.sh /etc/fdfs # 新建配置文件 /etc/fdfs目录下
shell> ls /etc/fdfs/ # 查看配置文件
shell> ls /usr/bin/ | grep fdfs # 查看shell
shell> mkdir /var/fastdfs/storage
shell> mkdir -p /var/fastdfs/tracker
shell> mkdir -p /var/fastdfs/files
shell> vi /etc/fdfs/storage.conf
shell> vi /etc/fdfs/tracker.conf
shell> vi /etc/fdfs/client.conf
shell> /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf restart
shell> /usr/bin/fdfs_storaged /etc/fdfs/storage.conf restart
shell> chkconfig fdfs_storaged on # 测试时不好用的 不需要执行操作
shell> chkconfig fdfs_trackerd on # 测试时不好用的 不需要执行操作
shell> touch test.txt
shell> echo "hello world" > test.txt
shell> /usr/bin/fdfs_upload_file /etc/fdfs/client.conf test.txt
shell> /usr/bin/fdfs_test /etc/fdfs/client.conf upload /var/fastdfs/test.txt
```

##### 启动和停止服务

```
shell> /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf start
shell> /usr/bin/fdfs_storaged /etc/fdfs/storage.conf start
shell> /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf stop
shell> /usr/bin/fdfs_storaged /etc/fdfs/storage.conf stop
```

##### storage.conf

```
tracker_sever = 119.3.203.187:22122
store_path0 = /var/fastdfs/files
base_path = /var/fastdfs/storage
```

##### tracker.conf

```
base_path=/var/fastdfs/tracker
```

##### client.conf

```
tracker_server= 119.3.203.187:22122
base_path = /var/fastdfs
```

##### Nginx安装

##### [nginx下载](http://nginx.org/en/download.html)

##### [fdfs-nginx下载](https://gitee.com/fastdfs100/fastdfs-nginx-module/tags)

```
shell> yum install gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel -y
shell> tar -xvf nginx-1.21.3.tar.gz
shell> unzip fastdfs-nginx-module-V1.22.zip
shell> cd nginx-1.21.3
shell> ./configure --prefix=/usr/local/fdfs_nginx --add-module=/root/fastdfs-nginx-module-V1.22/src
shell> make && make install
shell> /usr/local/fdfs_nginx/sbin/nginx -V # 查看是否包含fastdfs模块
shell> cd /usr/local/fdfs_nginx/conf
shell> cp nginx.conf nginx.conf.bak_YYYYMMDDHHMM
shell> vi nginx.conf
shell> ln -s /var/fastdfs/storage/data  /var/fastdfs/storage/data/M00
shell> cd /root/fastdfs-V6.08
shell> cp conf/http.conf conf/mime.types /etc/fdfs/
shell> cd /root/fastdfs-nginx-module-V1.22
shell> cp src/mod_fastdfs.conf /etc/fdfs/
shell> /usr/local/fdfs_nginx/sbin/nginx
shell> echo "hello world" > /root/a.txt
shell> /usr/bin/fdfs_test /etc/fdfs/client.conf upload /root/a.txt
# http://YOUR_IP:10000/M00/00/00/dwPLu2K-UsmABUksAAAADK8IOy0222_big.txt
```

##### nginx.conf

```
location /M00 {
    root /var/fastdfs/storage/data; # ${base_path}/data
    ngx_fastdfs_module;
}
```

##### http.conf (暂无更改)

```
# HTTP default content type
http.default_content_type = application/octet-stream

# MIME types mapping filename
# MIME types file format: MIME_type  extensions
# such as:  image/jpeg    jpeg jpg jpe
# you can use apache's MIME file: mime.types
http.mime_types_filename = mime.types

# if use token to anti-steal
# default value is false (0)
http.anti_steal.check_token = false

# token TTL (time to live), seconds
# default value is 600
http.anti_steal.token_ttl = 900

# secret key to generate anti-steal token
# this parameter must be set when http.anti_steal.check_token set to true
# the length of the secret key should not exceed 128 bytes
http.anti_steal.secret_key = FastDFS1234567890

# return the content of the file when check token fail
# default value is empty (no file sepecified)
http.anti_steal.token_check_fail = /home/yuqing/fastdfs/conf/anti-steal.jpg

# if support multi regions for HTTP Range
# default value is true
http.multi_range.enabed = true
```

##### mod_fastdfs.conf

```
#tracker_server=tracker:22122
tracker_server=119.3.203.187:22122
store_path0=/var/fastdfs/files
#store_path0=/home/yuqing/fastdfs
```

##### /group[0-9]访问配置

```shell
shell> vi /etc/fdfs/tracker.conf
# 更改url_have_group_name = true,原来是false
# 重启服务
shell> cd /usr/local/fdfs_nginx/conf
shell> vi nginx.conf
# 原来路径是/M00,更改为~/group([0-9])/M00
shell> cd ..
shell> sbin/nginx -s reload
# 上传测试文件
shell> /usr/bin/fdfs_test /etc/fdfs/client.conf upload fdfs_test.txt
```

##### /group[0-9]路径添加

```shell
# nginx配置更改
location ~/group[0-])/M00 {
    root /var/fastdfs/storage/data;
    ngx_fastdfs_module;
 }
# mod_fastdfs.conf url_have_group_name更改为true
url_have_group_name = true
# 重启服务
shell> /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf stop
shell> /usr/bin/fdfs_storaged /etc/fdfs/storage.conf stop
shell> /usr/bin/fdfs_storaged /etc/fdfs/storage.conf start
shell> /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf start
shell> /usr/local/fdfs_nginx/sbin/nginx -s stop
shell> 
# 测试
/usr/bin/fdfs_test /etc/fdfs/client.conf upload hello.txt
```

##### [参考文档](https://gitee.com/fastdfs100/fastdfs/blob/master/INSTALL)
