---
title: 大数据环境安装
date: 2019-07-31 13:52:00
tags: [大数据, INSTALL]
categories: 大数据
---

### 准备环境安装 (CentOS 7)

##### 配置系统主机名

```shell
shell> vi /etc/sysconfig/network
shell> vi /etc/hosts
shell> vi /etc/hostname
```

##### hosts

```
IP_1 HOST_NAME_1.com.cn HOST_NAME_1
IP_2 HOST_NAME_2.com.cn  HOST_NAME_2
IP_3 HOST_NAME_3.com.cn HOST_NAME_3
IP_4 HOST_NAME_4.com.cn HOST_NAME_4
```

##### network

```
NETWORKING=yes
HOSTANAME=YOUR_HOST_NAME
```

##### 关闭防火墙

```shell
shell> systemctl status firewalld
shell> systemctl disable firewalld
shell> systemctl stop firewalld
```

#### 关闭SELINUX

```shell
# sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
shell> vi /etc/selinux/config
```

##### config

```
SELINUX=disabled
```

##### 关闭THP

```
# sed -i 's/quiet/quiet transparent_hugepage=never/g' /etc/default/grub
shell> vi /etc/default/grub
shell> vi /etc/init.d/disable-transparent-hugepages
shell> chmod 755 /etc/init.d/disable-transparent-hugepages
shell> chkconfig --add disable-transparent-hugepages
shell> grub2-mkconfig -o /boot/grub2/grub.cfg 
shell> systemctl disable tuned
# [参考](https://blog.csdn.net/ffwar/article/details/77853498)
```

##### grub

```
GRUB_CMDLINE_LINUX="net.ifnames=0 spectre_v2=off nopti crashkernel=auto transparent_hugepage=never "
```

##### disable-transparent-hugepages

```
#!/bin/bash
### BEGIN INIT INFO
# Provides:          disable-transparent-hugepages
# Required-Start:    $local_fs
# Required-Stop:
# X-Start-Before:    mongod mongodb-mms-automation-agent
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Disable Linux transparent huge pages
# Description:       Disable Linux transparent huge pages, to improve
#                    database performance.
### END INIT INFO

case $1 in
  start)
    if [ -d /sys/kernel/mm/transparent_hugepage ]; then
      thp_path=/sys/kernel/mm/transparent_hugepage
    elif [ -d /sys/kernel/mm/redhat_transparent_hugepage ]; then
      thp_path=/sys/kernel/mm/redhat_transparent_hugepage
    else
      return 0
    fi

    echo 'never' > ${thp_path}/enabled
    echo 'never' > ${thp_path}/defrag

    re='^[0-1]+$'
    if [[ $(cat ${thp_path}/khugepaged/defrag) =~ $re ]]
    then
      # RHEL 7
      echo 0  > ${thp_path}/khugepaged/defrag
    else
      # RHEL 6
      echo 'no' > ${thp_path}/khugepaged/defrag
    fi

    unset re
    unset thp_path
    ;;
esac
```

##### 关闭PackageKit(如果存在)

```shell
# 禁止离线更新
shell> systemctl disable packagekit
shell> systemctl  stop  packagekit
shell> yum remove PackageKit
shell> systemctl  status packagekit
```

##### 同步时钟(所有机器同步到一个机器上)

```shell
# 单独机器同步时钟方案
# shell> crontab -e
# 0 1 * * * /usr/sbin/ntpdate cn.pool.ntp.org

# 集群同步时钟方案
shell> vi /etc/ntp.conf
shell> systemctl restart ntpd
shell> chkconfig ntpd on
shell> ntpstat
shell> ntpq -p

# [参考]([https://www.cnblogs.com/quchunhui/p/7658853.html](https://www.cnblogs.com/quchunhui/p/7658853.html))
```

##### Server的ntp.conf

```
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
server 127.127.1.0 fudge
fudge 127.127.1.0 stratum 0
#server 0.rhel.pool.ntp.org iburst
#server 1.rhel.pool.ntp.org iburst
#server 2.rhel.pool.ntp.org iburst
#server 3.rhel.pool.ntp.org iburst
```

##### Client的ntp.conf

```
# Hosts on local network are less restricted.
restrict YOUR_SERVER_IP mask YOUR_SERVER_MARK(255.255.255.0) nomodify notrap	

# Please consider joining the pool (http://www.pool.ntp.org/join.html).
server YOUR_SERVER_IP prefer
fudge 127.127.1.0 stratum 3
```

##### 互信通信

```shell
shell> ssh-keygen # 3次回车,不要输入密码
shell> cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
shell> ssh-copy-id -i ~/.ssh/id_rsa.pub root@YOUR_IP
```

##### 安装JDK环境(JDK 1.7+)

```shell
shell> tar zxvf jdk-8u221-linux-x64.tar.gz -C /usr/local/
shell> ln -s /usr/local/jdk1.8.0_221/ /usr/local/jdk8
shell> vi /etc/profile.d/env.sh
shell> source /etc/profile.d/env.sh
shell> javac
shell> java -version
```

##### env.sh

```bash
export JAVA_HOME=/usr/local/jdk8
export PATH=$JAVA_HOME/bin:$PATH
```

##### 启动httpd (只有Ambari机器安装httpd)

```shell
shell> yum -y install httpd
shell> systemctl start httpd
shell> chkconfig httpd on
```

##### 安装依赖库

```shell
shell> yum install libtirpc-devel* openssl openssl-devel python-devel -y
shell> yum -y install nfs*
```

##### 替换yum源

```shell
shell> cd /etc/yum.repos.d
shell> mkdir bak
shell> mv *.repo bak
# 上传新的yum源
```

##### centos7.repo

```
[centos7]
name=centos7
baseurl=http://10.1.240.11/centos7
enabled=1
gpgcheck=0
```

##### ambari.repo

```
```

##### HDP.repo

```
```

##### HDP-UTILS.repo

```
```

##### 配置数据库(ambari机器)

```
mysql> CREATE USER 'ambari'@'localhost' IDENTIFIED BY 'ambari!23';
mysql> CREATE USER 'hive'@'localhost' IDENTIFIED BY 'hive!@3';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'ambari'@'localhost' WITH GRANT OPTION;
mysql> GRANT ALL PRIVILEGES ON *.* TO 'hive'@'localhost' WITH GRANT OPTION;
mysql> exit
shell> mysql -uambari -p
mysql> CREATE SCHEMA `ambari` DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
mysql> use ambari
mysql> source Ambari-DDL-MySQL-CREATE.sql;
```

##### 上传mysql jar驱动

```shell
shell> mkdir /usr/share/java
shell> mv mysql-connector-java-6.0.6.jar  /usr/share/java/
```

##### 解压对应包

```shell
shell> mv HDP-...tar.gz /var/www/html
shell> mv HDP-UTILS...tar.gz /var/www/html
shell> mv ambari-...tar.gz /var/www/html
shell> cd /var/www/html
shell> tar zxvf HDP-...tar.gz
shell> tar zxvf HDP-UTILS...tar.gz
shell> tar zxvf ambari-...tar.gz
```

##### http服务

```
shell> cd /var/www/html
shell> chown -R www.www .
```

##### 环境检测

```shell
```
