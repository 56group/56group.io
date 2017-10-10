---
title:  simple vsftpd install
date: 2017-09-30 16:29:00
tags: [vsftpd, simple]
categories:  VSFTPD
---

# simple-vsftpd

### vsftpd安装

```
shell> yum install vsftpd -y
shell> chkconfig vsftpd on
shell> vi /etc/vsftpd/vsftpd.conf # 修改配置如下
shell> useradd -d /home/ftp -s /sbin/nologin ftpuser
shell> passwd ftpuser # password
shell> service vsftpd restart
```
### vsftpd.conf

```
chroot_local_user=YES
anonymous_enable=NO
```


