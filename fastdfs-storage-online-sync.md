---
title: FastDFS在线同步storage数据
date: 2023-02-01 08:55:00
tags: [FASRDFS, SYNC]
categories: FASRDFS
---

### FastDFS在线同步storage数据

##### 停止新的storage服务

```
shell> /usr/bin/fdfs_storaged /etc/fdfs/storage.conf stop
```

##### 修改storage的tracker_server配置

```
# 配置为原来的tracker server的地址
tracker_server = 120.46.145.155:22122
```

##### 启动storage服务

```
# 查看新storage的group的IP
shell> /usr/bin/fdfs_monitor /etc/fdfs/client.conf
# 删除新storage原有链接
shell> /usr/bin/fdfs_monitor /etc/fdfs/client.conf delete group1(YOUR_GROUP) YOUR_IP
# 备份新服务器的数据文件
shell> mv /var/fastdfs/files/data/(YOUR_STORAGE_DATA_DIR) /var/fastdfs/files/back_data(YOUR_BACK_DATA_DIR)
# 启动storage服务
shell> /usr/bin/fdfs_storaged /etc/fdfs/storage.conf start
# 查看状态
shell> /usr/bin/fdfs_monitor /etc/fdfs/storage.conf
```

- 注意:如果启动storage服务,查看状态有错误(/usr/bin/fdfs_monitor /etc/fdfs/storage.conf),可以多试几次,但是如果版本不兼容总是错误就需要用别的方式同步文件了,下面是使用linux的rsync服务同步storage文件

##### 使用RSYNC服务同步文件数据

```shell
# 在原有和新的fastdfs服务上安装rsync和启动rsync服务(多台机器都需要启动)
shell> yum install -y rsync
shell> yum -y install inotify-tools
shell> vi /etc/rsyncd.conf
shell> rsync --daemon
```

##### /etc/rsyncd.conf

```
# /etc/rsyncd: configuration file for rsync daemon mode

# See rsyncd.conf man page for more options.

# configuration example:

# uid = nobody
# gid = nobody
# use chroot = yes
# max connections = 4
# pid file = /var/run/rsyncd.pid
# exclude = lost+found/
# transfer logging = yes
# timeout = 900
# ignore nonreadable = yes
# dont compress   = *.gz *.tgz *.zip *.z *.Z *.rpm *.deb *.bz2

# [ftp]
#        path = /home/ftp
#        comment = ftp export area

port = 873
use chroot = no
max connections = 200
timeout = 600
read only = false
list = false
log file = /var/log/rsyncd.log
lock file = /var/run/rsyncd.lock
pid file = /var/run/rsyncd.pid

# 下面是一个模块配置(同时是放在被链接的那个服务,如果是push就是被推送的服务器,如果是pull就是拉取的服务器)
[test]
comment = welcome to linux!
path = /root/rsync_temp
# 是文件同步时用的用户和用户组
uid = root(自定义)
gid = root(自定义)
```

##### 测试同步文件

```shell
shell> echo "root:Zzmxqsm123456" > /etc/rsync.passwd
shell> chmod 600 /etc/rsync.passwd
# 客户端向服务端推送文件数据
shell> rsync -arzv --password-file=/etc/rsync.passwd tmp_rsync/ root@120.46.145.155::test
```

##### 通过inotify-tools监听同步文件脚本

```shell
#!/bin/bash
/usr/bin/inotifywait  -mrq  --format '%Xe  %w  %f' -e create,modify,delete,attrib,close_write  /root/test | while read line;
do
    rsync -arzv --password-file=/etc/rsync.passwd /root/test root@119.3.203.187::test
done
```

- 注意: inotify-tools监听的目录和要推送的目录必须一直

##### 运行脚本

```shell
shell> /usr/bin/sh /root/shell/dlut_fastdfs_rsync.sh > /root/shell/fastdfs_rsync.log 2>&1 &
# 切换到要同步数据的目录,新建一个文件触发数据同步
shell> cd /root/test
shell> echo "hello world" > a.txt
# 查看数据同步日志文件
shell> tail -f 
```




