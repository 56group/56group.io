---
title: MySQL MHA安装
date: 2019-07-05 17:00:00
tags: [MYSQL, LINUX, 安装]
categories: MYSQL
---

# MySQL MHA安装

### 准备环境

```

# 机器

 Role | IP | host  | server_id | type

------------ | -------------- | ------- | --------- | ----------

Monitor Host | 222.26.224.235 | manager | - | 监控复制组

Master  | 222.26.224.238 | Master |  101  | 写入

Candicate Master | 222.26.224.237 | Slave1 | 102 | 读

Slave | 222.26.224.236 | Slave2 |  103  | 读

----------------------------------------------------------------

＃ 所有机器上安装 Manager Master Slave1 Slave2

shell> wget https://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm

shell> rpm -ivh epel-release-6-8.noarch.rpm

shell> rm -rf epel-release-6-8.noarch.rpm

shell> yum install perl-DBD-MySQL perl-Config-Tiny perl-Log-Dispatch perl-Parallel-ForkManager perl-CPAN perl-Time-HiRes -y

```

### 安装MHA NODE

```

# 所有机器上安装 mha4mysql-node

shell> cd /usr/local/src

shell> tar zxvf mha4mysql-node-0.56.tar.gz

shell> cd mha4mysql-node-0.56

shell> perl Makefile.PL

shell> make

shell> make install

# 查看所有安装脚本

shell> cd /usr/local/bin/

[root@zookeeper03 bin]# ll

总用量 44

-r-xr-xr-x. 1 root root 16367 7月 13 13:26 apply_diff_relay_logs

-r-xr-xr-x. 1 root root 4807 7月 13 13:26 filter_mysqlbinlog

-r-xr-xr-x. 1 root root 8261 7月 13 13:26 purge_relay_logs

-r-xr-xr-x. 1 root root 7525 7月 13 13:26 save_binary_logs

```

### 安装MHA MANAGER

```

# 在MHA manager机器上安装 mha manager

shell> tar zxvf mha4mysql-manager-0.56.tar.gz

shell> cd mha4mysql-manager-0.56

shell> perl Makefile.PL

shell> make

shell> make install

# 查看安装脚本

shell> ls /usr/local/bin

# 复制脚本

shell> cd /usr/local/src/mha4mysql-manager-0.56/samples/scripts

shell> cp * /usr/local/bin/

```

### 配置SSH

```

# 在所有节点配置SSH,所有节点运行下面命令
shell> ssh-keygen -t rsa
shell> ssh-copy-id -i ~/.ssh/id_rsa.pub root@222.26.224.238
shell> ssh-copy-id -i ~/.ssh/id_rsa.pub root@222.26.224.237
shell> ssh-copy-id -i ~/.ssh/id_rsa.pub root@222.26.224.236
shell> ssh-copy-id -i ~/.ssh/id_rsa.pub root@222.26.224.235
```

>  所有机器舞密码登陆

### 配置Master-Slave架构

```
# 参考对应文档安装Master-Slave Replication
# 配置slave read－only

slave1> set global read_only=1;
slave1> show variables like '%read_only%';
slave2> set global read_only=1;
slave2> show variables like '%read_only%';
```

### 在Master添加监控用户

```
master> grant all privileges on *.* to 'root'@'222.26.224.%' identified by 'root@remote';
master> flush privileges;
```

### 配置MHA

```
# 在mha Manager配置MHA,在Manager主机
shell> mkdir /etc/masterha /var/log/masterha /var/log/masterha/app1
shell> pwd
/usr/local/src/mha4mysql-manager-0.56

shell> cp samples/conf/app1.cnf /etc/masterha/
```

### 配置MHA Manager app1.cnf

```
[server default]
manager_workdir=/var/log/masterha/app1.log
manager_log=/var/log/masterha/app1/manager.log
master_binlog_dir=/usr/local/mysql/mysql-files/
#master_ip_failover_script= /usr/local/bin/master_ip_failover
master_ip_online_change_script= /usr/local/bin/master_ip_online_change
password=root@remote
user=root
ping_interval=1
remote_workdir=/tmp
repl_password=slave
repl_user=slave
report_script=/usr/local/bin/send_report
secondary_check_script= /usr/local/bin/masterha_secondary_check -s 222.26.224.237 -s 222.26.224.238 --user=root --master_host=222.26.224.238 --master_ip=222.26.224.238 --master_port=3306
shutdown_script=""
ssh_user=root

[server1]
hostname=222.26.224.238
port=3306

[server2]
hostname=222.26.224.237
port=3306
candidate_master=1
check_repl_delay=0

[server3]
hostname=222.26.224.236
port=3306

```

### 在Slave配置relay log

```
slave1> set global relay_log_purge=0;
slave2> set global relay_log_purge=0;
```

### 在MANAGER MHA状态检查

```
# SSH状态
shell> masterha_check_ssh --conf=/etc/masterha/app1.cnf

# REPL状态,注意app1.cnf的注释
shell> masterha_check_repl --conf=/etc/masterha/app1.cnf

# MHA RUN status
shell> masterha_check_status --conf=/etc/masterha/app1.cnf

# start mha manager 监控
shell> nohup masterha_manager --conf=/etc/masterha/app1.cnf --remove_dead_master_conf --ignore_last_failover < /dev/null > /var/log/masterha/app1/manager.log 2>&1 &

# stop mha manager 监控
shell> masterha_stop --conf=/etc/masterha/app1.cnf
```

### 配置VIP[自动]

##### 在master和Candicate Master安装keepalived

```
shell> yum install openssl-devel gcc* -y
shell> cd /usr/local/src
shell> wget http://www.keepalived.org/software/keepalived-1.2.23.tar.gz
shell> tar zxvf keepalived-1.2.23.tar.gz
shell> cd keepalived-1.2.23
shell> ./configure --prefix=/usr/local/keepalived
shell> make
shell> make install
shell> mkdir /etc/keepalived
shell> cp /usr/local/keepalived/etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf
```

### Keepalived配置

```
! Configuration File for keepalived

global_defs {
  notification_email {
    weiniyangfg@163.com
  }

 notification_email_from weiniyangfg@163.com
 smtp_server 127.0.0.1
 smtp_connect_timeout 30
 router_id MySQL-HA
}

vrrp_instance VI_1 {
 state BACKUP
 interface eth0
 virtual_router_id 51
 priority 120
 advert_int 1
 nopreempt
 authentication {
   auth_type PASS
   auth_pass 1111
 }

 virtual_ipaddress {
   192.168.200.23
 }
}
```

### Keepalived启动

```
# 在master启动keepalived
shell> cd /usr/local/keepalived
shell> sbin/keepalived --use-file=/etc/keepalived/keepalived.conf
shell> tail -f /var/log/messages

# 在Candicate Master启动keepalived
shell> cd /usr/local/keepalived
shell> sbin/keepalived --use-file=/etc/keepalived/keepalived.conf
shell> tail -f /var/log/messages
```

> 通过日志查看状态

>  Master

```
[root@hadoop03 keepalived]# tail -f /var/log/messages

Jul 14 16:09:07 hadoop03 Keepalived_vrrp[19784]: Registering Kernel netlink reflector

Jul 14 16:09:07 hadoop03 Keepalived_vrrp[19784]: Registering Kernel netlink command channel

Jul 14 16:09:07 hadoop03 Keepalived_vrrp[19784]: Registering gratuitous ARP shared channel

Jul 14 16:09:07 hadoop03 Keepalived_healthcheckers[19783]: Opening file '/etc/keepalived/keepalived.conf'.

Jul 14 16:09:07 hadoop03 Keepalived_vrrp[19784]: Opening file '/etc/keepalived/keepalived.conf'.

Jul 14 16:09:07 hadoop03 Keepalived_vrrp[19784]: Using LinkWatch kernel netlink reflector...

Jul 14 16:09:07 hadoop03 Keepalived_healthcheckers[19783]: Using LinkWatch kernel netlink reflector...

Jul 14 16:09:07 hadoop03 Keepalived_vrrp[19784]: VRRP_Instance(VI_1) Entering BACKUP STATE

Jul 14 16:09:11 hadoop03 Keepalived_vrrp[19784]: VRRP_Instance(VI_1) Transition to MASTER STATE

Jul 14 16:09:12 hadoop03 Keepalived_vrrp[19784]: VRRP_Instance(VI_1) Entering MASTER STATE

```

> Candicate Master

```

Jul 14 16:09:46 hadoop02 Keepalived_healthcheckers[7763]: Registering Kernel netlink reflector

Jul 14 16:09:46 hadoop02 Keepalived_healthcheckers[7763]: Registering Kernel netlink command channel

Jul 14 16:09:46 hadoop02 Keepalived_healthcheckers[7763]: Opening file '/etc/keepalived/keepalived.conf'.

Jul 14 16:09:46 hadoop02 Keepalived_vrrp[7764]: Registering Kernel netlink reflector

Jul 14 16:09:46 hadoop02 Keepalived_vrrp[7764]: Registering Kernel netlink command channel

Jul 14 16:09:46 hadoop02 Keepalived_vrrp[7764]: Registering gratuitous ARP shared channel

Jul 14 16:09:46 hadoop02 Keepalived_vrrp[7764]: Opening file '/etc/keepalived/keepalived.conf'.

Jul 14 16:09:46 hadoop02 Keepalived_vrrp[7764]: Using LinkWatch kernel netlink reflector...

Jul 14 16:09:46 hadoop02 Keepalived_healthcheckers[7763]: Using LinkWatch kernel netlink reflector...

Jul 14 16:09:46 hadoop02 Keepalived_vrrp[7764]: VRRP_Instance(VI_1) Entering BACKUP STATE

```

### MHA Keepalived配置

##### 修改master_ip_failover脚本

```

# 在MHA Manager上配置master_ip_failover

#!/usr/bin/env perl

# Copyright (C) 2011 DeNA Co.,Ltd.

#

# This program is free software; you can redistribute it and/or modify

# it under the terms of the GNU General Public License as published by

# the Free Software Foundation; either version 2 of the License, or

# (at your option) any later version.

#

# This program is distributed in the hope that it will be useful,

# but WITHOUT ANY WARRANTY; without even the implied warranty of

# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the

# GNU General Public License for more details.

#

# You should have received a copy of the GNU General Public License

#  along with this program; if not, write to the Free Software

# Foundation, Inc.,

# 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA

## Note: This is a sample script and is not complete. Modify the script based on your environment.

use strict;

use warnings FATAL => 'all';

use Getopt::Long;

use MHA::DBHelper;

my (

 $command, $ssh_user,  $orig_master_host,

 $orig_master_ip, $orig_master_port, $new_master_host,

 $new_master_ip, $new_master_port, $new_master_user,

 $new_master_password

);

# new

my $vip = '192.168.200.23';

my $ssh_start_vip = "/etc/init.d/keepalived start";

my $ssh_stop_vip = "/etc/init.d/keepalived stop";

GetOptions(

 'command=s'  => \$command,

 'ssh_user=s' => \$ssh_user,

 'orig_master_host=s' => \$orig_master_host,

 'orig_master_ip=s' => \$orig_master_ip,

 'orig_master_port=i' => \$orig_master_port,

 'new_master_host=s'  => \$new_master_host,

 'new_master_ip=s'  => \$new_master_ip,

 'new_master_port=i'  => \$new_master_port,

 'new_master_user=s'  => \$new_master_user,

 'new_master_password=s' => \$new_master_password,

);

exit &main();

 # new

 print "\n\nIN SCRIPT TEST====$ssh_stop_vip==$ssh_start_vip===\n\n";

sub main {

 if ( $command eq "stop" || $command eq "stopssh" ) {

 # $orig_master_host, $orig_master_ip, $orig_master_port are passed.

 # If you manage master ip address at global catalog database,

 # invalidate orig_master_ip here.

 my $exit_code = 1;

 eval {

 # new

 print "Disabling the VIP on old master: $orig_master_host \n";

 &stop_vip();

 # updating global catalog, etc

 $exit_code = 0;

 };

 if ($@) {

 warn "Got Error: $@\n";

 exit $exit_code;

 }

 exit $exit_code;

 }

 elsif ( $command eq "start" ) {

 # all arguments are passed.

 # If you manage master ip address at global catalog database,

 # activate new_master_ip here.

 # You can also grant write access (create user, set read_only=0, etc) here.

 my $exit_code = 10;

 eval {

 my $new_master_handler = new MHA::DBHelper();

 # args: hostname, port, user, password, raise_error_or_not

 $new_master_handler->connect( $new_master_ip, $new_master_port,

 $new_master_user, $new_master_password, 1 );

 ## Set read_only=0 on the new master

 $new_master_handler->disable_log_bin_local();

 print "Set read_only=0 on the new master.\n";

 $new_master_handler->disable_read_only();

 ## Creating an app user on the new master

 print "Creating app user on the new master..\n";

 FIXME_xxx_create_user( $new_master_handler->{dbh} );

 $new_master_handler->enable_log_bin_local();

 $new_master_handler->disconnect();

 ## Update master ip on the catalog database, etc

 # FIXME_xxx;

 #new

 print "Enabling the VIP - $vip on the new master - $new_master_host \n";

 &start_vip();

 $exit_code = 0;

 };

 if ($@) {

 warn $@;

 # If you want to continue failover, exit 10.

 exit $exit_code;

 }

 exit $exit_code;

 }

 elsif ( $command eq "status" ) {

 #new

 print "Checking the Status of the script.. OK \n";

 # do nothing

 exit 0;

 }

 else {

 &usage();

 exit 1;

 }

}

# A simple system call that enable the VIP on the new master

sub start_vip() {

 `ssh $ssh_user\@$new_master_host \" $ssh_start_vip \"`;

}

# A simple system call that disable the VIP on the old_master

sub stop_vip() {

 return 0 unless ($ssh_user);

 `ssh $ssh_user\@$orig_master_host \" $ssh_stop_vip \"`;

}

sub usage {

 print

"Usage: master_ip_failover --command=start|stop|stopssh|status --orig_master_host=host --orig_master_ip=ip --orig_master_port=port --new_master_host=host --new_master_ip=ip --new_master_port=port\n";

}

```

### 配置MHA

```

# 在MHA Manager添加master_ip_failover_script

shell> grep master_ip_failover_script /etc/masterha/app1.cnf

master_ip_failover_script= /usr/local/bin/master_ip_failover

```

### MHA状态

```

# 查看MHA状态

shell> masterha_check_repl --conf=/etc/masterha/app1.cnf 

# 启动MHA

shell> nohup masterha_manager --conf=/etc/masterha/app1.cnf --remove_dead_master_conf --ignore_last_failover < /dev/null > /var/log/masterha/app1/manager.log 2>&1 &

```

### 测试

```

# 安装sysbench

shell> yum install git automake libtool -y

shell> git clone https://github.com/akopytov/sysbench.git

shell> cd sysbench/

shell> ./autogen.sh

shell> ./configure

shell> ./configure -with-mysql-includes=/usr/local/mysql --with-mysql-libs=/usr/local/mysql/lib/

shell> make

shell> make install

shell> export LD_LIBRARY_PATH=/usr/local/mysql/lib/

# Master测试

mysql> create database sbtest;

shell> sysbench --test=/usr/local/src/sysbench/sysbench/tests/db/oltp.lua --oltp-table-size=1000000 --oltp-read-only=off --init-rng=on --num-threads=16 --max-requests=0 --oltp-dist-type=uniform --max-time=1800 --mysql-user=root --mysql-socket=/usr/local/mysql/mysql-files/mysql.sock --mysql-password=root --db-driver=mysql --mysql-table-engine=innodb --oltp-test-mode=complex prepare

# stop one slave io_thread, 另一个slave不停止

shell> mysql -h 127.0.0.1 -proot -uroot --prompt='slave1> '

slave1> stop slave io_thread;

# Master压力测试

shell> sysbench --test=/usr/local/src/sysbench/sysbench/tests/db/oltp.lua --oltp-table-size=1000000 --oltp-read-only=off --init-rng=on --num-threads=16 --max-requests=0 --oltp-dist-type=uniform --max-time=180 --mysql-user=root --mysql-socket=/usr/local/mysql/mysql-files/mysql.sock --mysql-password=root --db-driver=mysql --mysql-table-engine=innodb --oltp-test-mode=complex run

# start stop io_thread's salve's io_thread

slave1> start slave io_thread;

# 停止Master mysqld

shell> pkill -9 mysqld

# 查看MHA日志

shell> cat /var/log/masterha/app1/manager.log

```

### MHA failover日志

```
Fri Jul 15 10:50:00 2016 - [warning] Got error on MySQL select ping: 2006 (MySQL server has gone away)

Fri Jul 15 10:50:00 2016 - [info] Executing secondary network check script: /usr/local/bin/masterha_secondary_check -s 222.26.224.237 -s 222.26.224.238 --user=root --master_host=222.26.224.238 --master_ip=222.26.224.238 --master_port=3306 --user=root --master_host=222.26.224.238 --master_ip=222.26.224.238 --master_port=3306 --master_user=root --master_password=root@remote --ping_type=SELECT

Fri Jul 15 10:50:00 2016 - [info] Executing SSH check script: exit 0

Fri Jul 15 10:50:00 2016 - [info] HealthCheck: SSH to 222.26.224.238 is reachable.

Monitoring server 222.26.224.237 is reachable, Master is not reachable from 222.26.224.237\. OK.

Monitoring server 222.26.224.238 is reachable, Master is not reachable from 222.26.224.238\. OK.

Fri Jul 15 10:50:00 2016 - [info] Master is not reachable from all other monitoring servers. Failover should start.

Fri Jul 15 10:50:01 2016 - [warning] Got error on MySQL connect: 2013 (Lost connection to MySQL server at 'reading initial communication packet', system error: 111)

Fri Jul 15 10:50:01 2016 - [warning] Connection failed 2 time(s)..

Fri Jul 15 10:50:02 2016 - [warning] Got error on MySQL connect: 2013 (Lost connection to MySQL server at 'reading initial communication packet', system error: 111)

Fri Jul 15 10:50:02 2016 - [warning] Connection failed 3 time(s)..

Fri Jul 15 10:50:03 2016 - [warning] Got error on MySQL connect: 2013 (Lost connection to MySQL server at 'reading initial communication packet', system error: 111)

Fri Jul 15 10:50:03 2016 - [warning] Connection failed 4 time(s)..

Fri Jul 15 10:50:03 2016 - [warning] Master is not reachable from health checker!

Fri Jul 15 10:50:03 2016 - [warning] Master 222.26.224.238(222.26.224.238:3306) is not reachable!

Fri Jul 15 10:50:03 2016 - [warning] SSH is reachable.

Fri Jul 15 10:50:03 2016 - [info] Connecting to a master server failed. Reading configuration file /etc/masterha_default.cnf and /etc/masterha/app1.cnf again, and trying to connect to all servers to check server status..

Fri Jul 15 10:50:03 2016 - [warning] Global configuration file /etc/masterha_default.cnf not found. Skipping.

Fri Jul 15 10:50:03 2016 - [info] Reading application default configuration from /etc/masterha/app1.cnf..

Fri Jul 15 10:50:03 2016 - [info] Reading server configuration from /etc/masterha/app1.cnf..

Fri Jul 15 10:50:04 2016 - [info] GTID failover mode = 1

Fri Jul 15 10:50:04 2016 - [info] Dead Servers:

Fri Jul 15 10:50:04 2016 - [info]  222.26.224.238(222.26.224.238:3306)

Fri Jul 15 10:50:04 2016 - [info] Alive Servers:

Fri Jul 15 10:50:04 2016 - [info]  222.26.224.237(222.26.224.237:3306)

Fri Jul 15 10:50:04 2016 - [info]  222.26.224.236(222.26.224.236:3306)

Fri Jul 15 10:50:04 2016 - [info] Alive Slaves:

Fri Jul 15 10:50:04 2016 - [info]  222.26.224.237(222.26.224.237:3306) Version=5.7.13-log (oldest major version between slaves) log-bin:enabled

Fri Jul 15 10:50:04 2016 - [info]  GTID ON

Fri Jul 15 10:50:04 2016 - [info]  Replicating from 222.26.224.238(222.26.224.238:3306)

Fri Jul 15 10:50:04 2016 - [info]  Primary candidate for the new Master (candidate_master is set)

Fri Jul 15 10:50:04 2016 - [info]  222.26.224.236(222.26.224.236:3306) Version=5.7.13-log (oldest major version between slaves) log-bin:enabled

Fri Jul 15 10:50:04 2016 - [info]  GTID ON

Fri Jul 15 10:50:04 2016 - [info]  Replicating from 222.26.224.238(222.26.224.238:3306)

Fri Jul 15 10:50:04 2016 - [info] Checking slave configurations..

Fri Jul 15 10:50:04 2016 - [info] Checking replication filtering settings..

Fri Jul 15 10:50:04 2016 - [info] Replication filtering check ok.

Fri Jul 15 10:50:04 2016 - [info] Master is down!

Fri Jul 15 10:50:04 2016 - [info] Terminating monitoring script.

Fri Jul 15 10:50:04 2016 - [info] Got exit code 20 (Master dead).

Fri Jul 15 10:50:04 2016 - [info] MHA::MasterFailover version 0.56.

Fri Jul 15 10:50:04 2016 - [info] Starting master failover.

Fri Jul 15 10:50:04 2016 - [info] 

Fri Jul 15 10:50:04 2016 - [info] * Phase 1: Configuration Check Phase..

Fri Jul 15 10:50:04 2016 - [info] 

Fri Jul 15 10:50:05 2016 - [info] GTID failover mode = 1

Fri Jul 15 10:50:05 2016 - [info] Dead Servers:

Fri Jul 15 10:50:05 2016 - [info]  222.26.224.238(222.26.224.238:3306)

Fri Jul 15 10:50:05 2016 - [info] Checking master reachability via MySQL(double check)...

Fri Jul 15 10:50:05 2016 - [info] ok.

Fri Jul 15 10:50:05 2016 - [info] Alive Servers:

Fri Jul 15 10:50:05 2016 - [info]  222.26.224.237(222.26.224.237:3306)

Fri Jul 15 10:50:05 2016 - [info]  222.26.224.236(222.26.224.236:3306)

Fri Jul 15 10:50:05 2016 - [info] Alive Slaves:

Fri Jul 15 10:50:05 2016 - [info]  222.26.224.237(222.26.224.237:3306) Version=5.7.13-log (oldest major version between slaves) log-bin:enabled

Fri Jul 15 10:50:05 2016 - [info]  GTID ON

Fri Jul 15 10:50:05 2016 - [info]  Replicating from 222.26.224.238(222.26.224.238:3306)

Fri Jul 15 10:50:05 2016 - [info]  Primary candidate for the new Master (candidate_master is set)

Fri Jul 15 10:50:05 2016 - [info]  222.26.224.236(222.26.224.236:3306) Version=5.7.13-log (oldest major version between slaves) log-bin:enabled

Fri Jul 15 10:50:05 2016 - [info]  GTID ON

Fri Jul 15 10:50:05 2016 - [info]  Replicating from 222.26.224.238(222.26.224.238:3306)

Fri Jul 15 10:50:05 2016 - [info] Starting GTID based failover.

Fri Jul 15 10:50:05 2016 - [info] 

Fri Jul 15 10:50:05 2016 - [info] ** Phase 1: Configuration Check Phase completed.

Fri Jul 15 10:50:05 2016 - [info] 

Fri Jul 15 10:50:05 2016 - [info] * Phase 2: Dead Master Shutdown Phase..

Fri Jul 15 10:50:05 2016 - [info] 

Fri Jul 15 10:50:05 2016 - [info] Forcing shutdown so that applications never connect to the current master..

Fri Jul 15 10:50:05 2016 - [info] Executing master IP deactivation script:

Fri Jul 15 10:50:05 2016 - [info]  /usr/local/bin/master_ip_failover --orig_master_host=222.26.224.238 --orig_master_ip=222.26.224.238 --orig_master_port=3306 --command=stopssh --ssh_user=root  

Disabling the VIP on old master: 222.26.224.238 

bash: /etc/init.d/keepalived: 没有那个文件或目录

Fri Jul 15 10:50:05 2016 - [info] done.

Fri Jul 15 10:50:05 2016 - [warning] shutdown_script is not set. Skipping explicit shutting down of the dead master.

Fri Jul 15 10:50:05 2016 - [info] * Phase 2: Dead Master Shutdown Phase completed.

Fri Jul 15 10:50:05 2016 - [info] 

Fri Jul 15 10:50:05 2016 - [info] * Phase 3: Master Recovery Phase..

Fri Jul 15 10:50:05 2016 - [info] 

Fri Jul 15 10:50:05 2016 - [info] * Phase 3.1: Getting Latest Slaves Phase..

Fri Jul 15 10:50:05 2016 - [info] 

Fri Jul 15 10:50:05 2016 - [info] The latest binary log file/position on all slaves is mysql-bin.000010:397675666

Fri Jul 15 10:50:05 2016 - [info] Retrieved Gtid Set: 71bbe17b-47fd-11e6-828f-005056be4afb:1-5577

Fri Jul 15 10:50:05 2016 - [info] Latest slaves (Slaves that received relay log files to the latest):

Fri Jul 15 10:50:05 2016 - [info]  222.26.224.236(222.26.224.236:3306) Version=5.7.13-log (oldest major version between slaves) log-bin:enabled

Fri Jul 15 10:50:05 2016 - [info]  GTID ON

Fri Jul 15 10:50:05 2016 - [info]  Replicating from 222.26.224.238(222.26.224.238:3306)

Fri Jul 15 10:50:05 2016 - [info] The oldest binary log file/position on all slaves is mysql-bin.000010:391361615

Fri Jul 15 10:50:05 2016 - [info] Retrieved Gtid Set: 71bbe17b-47fd-11e6-828f-005056be4afb:1-2785

Fri Jul 15 10:50:05 2016 - [info] Oldest slaves:

Fri Jul 15 10:50:05 2016 - [info]  222.26.224.237(222.26.224.237:3306) Version=5.7.13-log (oldest major version between slaves) log-bin:enabled

Fri Jul 15 10:50:05 2016 - [info]  GTID ON

Fri Jul 15 10:50:05 2016 - [info]  Replicating from 222.26.224.238(222.26.224.238:3306)

Fri Jul 15 10:50:05 2016 - [info]  Primary candidate for the new Master (candidate_master is set)

Fri Jul 15 10:50:05 2016 - [info] 

Fri Jul 15 10:50:05 2016 - [info] * Phase 3.3: Determining New Master Phase..

Fri Jul 15 10:50:05 2016 - [info] 

Fri Jul 15 10:50:05 2016 - [info] Searching new master from slaves..

Fri Jul 15 10:50:05 2016 - [info] Candidate masters from the configuration file:

Fri Jul 15 10:50:05 2016 - [info]  222.26.224.237(222.26.224.237:3306) Version=5.7.13-log (oldest major version between slaves) log-bin:enabled

Fri Jul 15 10:50:05 2016 - [info]  GTID ON

Fri Jul 15 10:50:05 2016 - [info]  Replicating from 222.26.224.238(222.26.224.238:3306)

Fri Jul 15 10:50:05 2016 - [info]  Primary candidate for the new Master (candidate_master is set)

Fri Jul 15 10:50:05 2016 - [info] Non-candidate masters:

Fri Jul 15 10:50:05 2016 - [info] Searching from candidate_master slaves which have received the latest relay log events..

Fri Jul 15 10:50:05 2016 - [info]  Not found.

Fri Jul 15 10:50:05 2016 - [info] Searching from all candidate_master slaves..

Fri Jul 15 10:50:05 2016 - [info] New master is 222.26.224.237(222.26.224.237:3306)

Fri Jul 15 10:50:05 2016 - [info] Starting master failover..

Fri Jul 15 10:50:05 2016 - [info] 

From:

222.26.224.238(222.26.224.238:3306) (current master)

 +--222.26.224.237(222.26.224.237:3306)

 +--222.26.224.236(222.26.224.236:3306)

To:

222.26.224.237(222.26.224.237:3306) (new master)

 +--222.26.224.236(222.26.224.236:3306)

Fri Jul 15 10:50:05 2016 - [info] 

Fri Jul 15 10:50:05 2016 - [info] * Phase 3.3: New Master Recovery Phase..

Fri Jul 15 10:50:05 2016 - [info] 

Fri Jul 15 10:50:05 2016 - [info] Waiting all logs to be applied.. 

Fri Jul 15 10:50:07 2016 - [info]  done.

Fri Jul 15 10:51:09 2016 - [info] Replicating from the latest slave 222.26.224.236(222.26.224.236:3306) and waiting to apply..

Fri Jul 15 10:51:09 2016 - [info] Waiting all logs to be applied on the latest slave.. 

Fri Jul 15 10:51:09 2016 - [info] Resetting slave 222.26.224.237(222.26.224.237:3306) and starting replication from the new master 222.26.224.236(222.26.224.236:3306)..

Fri Jul 15 10:51:09 2016 - [info] Executed CHANGE MASTER.

Fri Jul 15 10:51:10 2016 - [info] Slave started.

Fri Jul 15 10:51:10 2016 - [info] Waiting to execute all relay logs on 222.26.224.237(222.26.224.237:3306)..

```

### MHA 附加

### MHA NODE脚本

```

save_binary_logs 保存和复制master的二进制日志

apply_diff_relay_logs  识别差异的中继日志事件并将其差异的事件应用于其他的slave

filter_mysqlbinlog 去除不必要的ROLLBACK事件（MHA已不再使用这个工具）

purge_relay_logs 清除中继日志（不会阻塞SQL线程）

```

### MHA MANAGER脚本

```

masterha_check_ssh 检查MHA的SSH配置状况

masterha_check_repl  检查MySQL复制状况

masterha_manger  启动MHA

masterha_check_status  检测当前MHA运行状态

masterha_master_monitor  检测master是否宕机

masterha_master_switch 控制故障转移（自动或者手动）

masterha_conf_host 添加或删除配置的server信息

```

### app1.cnf解释

```

[server default]

manager_workdir=/var/log/masterha/app1.log  # 设置manager的工作目录

manager_log=/var/log/masterha/app1/manager.log # 设置manager的日志

master_binlog_dir=/data/mysql # 设置master 保存binlog的位置，以便MHA可以找到master的日志，我这里的也就是mysql的数据目录

master_ip_failover_script= /usr/local/bin/master_ip_failover # 设置自动failover时候的切换脚本

master_ip_online_change_script= /usr/local/bin/master_ip_online_change # 设置手动切换时候的切换脚本

password=123456 # 设置mysql中root用户的密码，这个密码是前文中创建监控用户的那个密码

user=root  # 设置监控用户root

ping_interval=1 # 设置监控主库，发送ping包的时间间隔，默认是3秒，尝试三次没有回应的时候自动进行railover

remote_workdir=/tmp # 设置远端mysql在发生切换时binlog的保存位置

repl_password=123456 # 设置复制用户的密码

repl_user=repl # 设置复制环境中的复制用户名

report_script=/usr/local/send_report # 设置发生切换后发送的报警的脚本

secondary_check_script= /usr/local/bin/masterha_secondary_check -s server03 -s server02 --user=root --master_host=server02 --master_ip=192.168.0.50 --master_port=3306 # 一旦MHA到server02的监控之间出现问题，MHA Manager将会尝试从server03登录到server02

shutdown_script="" # 设置故障发生后关闭故障主机脚本（该脚本的主要作用是关闭主机放在发生脑裂,这里没有使用）

ssh_user=root # 设置ssh的登录用户名

[server1]

hostname=192.168.0.50

port=3306

[server2]

hostname=192.168.0.60

port=3306

candidate_master=1 # 设置为候选master，如果设置该参数以后，发生主从切换以后将会将此从库提升为主库，即使这个主库不是集群中事件最新的slave

check_repl_delay=0 # 默认情况下如果一个slave落后master 100M的relay logs的话，MHA将不会选择该slave作为一个新的master，因为对于这个slave的恢复需要花费很长时间，通过设置check_repl_delay=0,MHA触发切换在选择一个新的master的时候将会忽略复制延时，这个参数对于设置了candidate_master=1的主机非常有用，因为这个候选主在切换的过程中一定是新的master

[server3]

hostname=192.168.0.70

port=3306

```

### 参考

[[MHA介绍和安装](http://www.cnblogs.com/gomysql/p/3675429.html)](http://www.cnblogs.com/gomysql/p/3675429.html)

[[MHA并行复制](http://www.ttlsa.com/mysql/mysql-5-7-enhanced-multi-thread-salve/)](http://www.ttlsa.com/mysql/mysql-5-7-enhanced-multi-thread-salve/)



