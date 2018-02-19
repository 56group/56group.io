---
title:  Vsftp通用安装
date: 2017-07-27 20:33:00
tags: [Vsftp, 安装]
categories:  Linux
---

### VSFTP 普通安装

##### vsftpd基础安装

```
shell> yum update -y
shell> yum install vsftpd
shell> useradd -d /ftp -s /sbin/nologin ftpuser
shell> passwd ftpuser
shell> chmod -R 755 /ftp
shell> chown -R ftpuser.ftpuser /ftp
```

##### vsftpd配置

```
local_enable=YES
local_umask=022
dirmessage_enable=YES
dirlist_enable=YES
connect_from_port_20=YES
listen=YES
anonymous_enable=NO
userlist_enable=YES
userlist_deny=NO
userlist_file=/etc/vsftpd/user_list
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
chroot_local_user=NO
xferlog_file=/var/log/vsftpd.log
xferlog_enable=YES
xferlog_std_format=YES
idle_session_timeout=60
data_connection_timeout=60
accept_timeout=60
connect_timeout=60
max_clients=200
pasv_enable=YES
pasv_min_port=5000
pasv_max_port=6000
ftpd_banner=Welcome to Ftp Server!
pam_service_name=vsftpd
write_enable=YES
```

##### user_list

```
ftpuser
```

##### ftpusers

```
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
news
uucp
operator
games
nobody
```

##### chroot_list

```
ftpuser
```

##### 配置文件结构

```
shell> ls /etc/vsftpd
chroot_list  ftpusers  user_list  vsftpd.conf  vsftpd.conf.bak  vsftpd_conf_migrate.sh
```
