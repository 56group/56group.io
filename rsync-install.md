---
title: RSYNC 安装
date: 2019-07-05 17:18:00
tags: [RSYNC, 安装]
categories: RSYNC
---

# RSYNC 安装

### rsync安装
```
shell> cd /usr/local/src
shell> wget https://download.samba.org/pub/rsync/src/rsync-3.1.2.tar.gz
shell> tar zxvf rsync-3.1.2.tar.gz
shell> cd rsync-3.1.2
shell> ./configure --prefix=/usr/local/rsync
shell> make && make install
shell> mkdir /etc/rsyncd
shell> vi /etc/rsyncd/rsyncd.conf
shell> ln -s /etc/rsyncd/rsyncd.conf /etc/rsyncd.conf
shell> vi /etc/rsyncd/rsyncd.secrets
shell> chown root:root /etc/rsyncd/rsyncd.secrets
shell> chmod 600 /etc/rsyncd/rsyncd.secrets
shell> vi /etc/rsyncd/rsyncd.motd
shell> /usr/local/rsync/bin/rsync --daemon
shell> netstat -npl | grep 873
```

### 同步

```
shell> rsync -avzP testrsyncuser@222.26.224.236::rsynctest /root/test
```

### 附加

##### rsyncd.conf

```
uid = root
gid = root
use chroot = true
read only = true
hosts allow = * # 主机IP
max connections = 5
pid file = /var/run/rsyncd.pid
secrets file = /etc/rsyncd/rsyncd.secrets # 用户密码
motd file = /etc/rsyncd/rsyncd.motd # 同步信息配置
log file = /var/log/rsync.log
transfer logging = yes
log format = %t %a %m %f %b
syslog facility = local3
timeout = 300

[rsynctest]
path = /usr/local/test
list = yes
ignore errors
auth users = testrsyncuser
comment = test rsync dir
exclude = important # 不同步的文件夹
```

### rsyncd.secrets

```
testrsyncuser:testrsyncuser
```

### rsyncd.motd

```
++++++++++++++++++++++++++++++++
+  RSYNC FILES +
++++++++++++++++++++++++++++++++
```

### 参考资料

[[安装](http://www.cnblogs.com/mchina/p/2829944.html)](http://www.cnblogs.com/mchina/p/2829944.html)


