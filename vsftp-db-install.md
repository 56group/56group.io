---
title:  Vsftp数据库配置安装
date: 2017-07-27 20:18:00
tags: [VSFTP, 安装]
categories:  Linux
---

### VSFTP数据库版安装

> 登陆用户可以上传文件,匿名用户只能下载文件

##### 下载工具

```
[mysql](http://dev.mysql.com/downloads/mysql/)
[pam_mysql](http://pam-mysql.sourceforge.net/) 梯子
[vsftpd](https://security.appspot.com/vsftpd.html) 梯子
[mysql vsftpd pam_mysql](ftp://222.26.224.60/software/vsftpd/) 内网下载
```

##### 安装

```
# 安装mysql, 可以通过两个方式安装,因为这个是ftp服务同时登陆的上传用户不会很多,所以基本的mysql就可以了,同理也可以通过上面下载的源码包安装
shell> yum install mysql mysql-client mysql-devel -y
# 安装pam_mysql
shell> tar zxvf pam_mysql-0.7RC1.tar.gz
shell> cd pam_mysql-0.7RC1
shell> chown u+x configure
shell> ./configure
shell> make
shell> make install
shell> ln -s /usr/lib/security/pam_mysql.so /lib/security/pam_mysql.so
# 安装vsftpd
shell> tar zxvf vsftpd-3.0.3.tar.gz
shell> cd vsftpd-3.0.3
shell> make
shell> make install
shell> useradd -d /var/ftp -s /sbin/nologin vsftpd
shell> passwd vsftpd
shell> chown -R vsftpd.vsftpd /var/ftp
```

##### 数据库配置

```
shell> mysql -uroot -p
mysql> CREATE SCHEMA `vsftp` DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
mysql> CREATE USER 'vsftpd'@'localhost' IDENTIFIED BY 'vsftp';
mysql> GRANT ALL PRIVILEGES ON vsftp.* TO 'vsftpd'@'localhost' WITH GRANT OPTION;
mysql> \q
shell> mysql -uvsftpd -pvsftp
mysql> use vsftp
mysql> create table users ( id tinyint AUTO_increment, name char(20), passwd char(48), primary key(id));
mysql> insert into users (name, passwd) values ('testuser', password('testuser'));
```

##### vsftpd配置
```
# /etc/vsftpd.conf
anonymous_enable=YES
local_enable=YES
write_enable=YES
local_umask=002
anon_upload_enable=NO
anon_mkdir_write_enable=NO
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES
chroot_local_user=YES
listen=YES
pam_service_name=vsftpd
userlist_enable=YES
#tcp_wrappers=YES

guest_enable=YES
guest_username=vsftpd
pam_service_name=vsftpd.mysql
user_config_dir=/etc/vsftpd/vusers_dir
anon_root=/var/ftp
ftp_username=vsftpd
anon_umask=022

# /etc/vsftpd.user_list
shell> cat /etc/passwd | grep -v vsftpd | awk -F ':' '{print $1}' >> /etc/vsftpd.user_list

# /etc/pam.d/vsftpd.mysql
auth required /lib/security/pam_mysql.so user=vsftpd passwd=vsftp host=localhost db=vsftp table=users usercolumn=name passwdcolumn=passwd crypt=2
account required /lib/security/pam_mysql.so user=vsftpd passwd=vsftp host=localhost db=vsftp table=users usercolumn=name passwdcolumn=passwd crypt=2

# /etc/pam.d/vsftpd
#%PAM-1.0
session    optional     pam_keyinit.so    force revoke
auth       required pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed
auth       required pam_shells.so
auth       include  password-auth
account    include  password-auth
session    required     pam_loginuid.so
session    include  password-auth

# /etc/vsftpd/users_dir
shell> mkdir -p /etc/vsftpd/vusers_dir
shell> cd /etc/vsftpd/vusers_dir
shell> touch testuser
# anon_upload_enable=YES
# anon_mkdir_write_enable=YES
# anon_other_write_enable=YES

# 配置防火墙
shell> setenforce 0
shell> vi /etc/sysconfig/iptables
# -A INPUT -m state --state NEW -m tcp -p tcp --dport 21 -j ACCEPT
# -A INPUT -m state --state NEW -m tcp -p tcp --dport 20 -j ACCEPT

# 启动服务和测试
shell> /usr/local/sbin/vsftpd &
```
