---
title: PMM install
date: 2017-05-11 13:53:00
tags: [mysql, mongodb, monitor]
categories: DB
---

### [PMM INSTALL](https://www.percona.com/doc/percona-monitoring-and-management/deploy/index.html)

```
yum install docker -y
service docker restart

# create data container
docker create \
   -v /opt/prometheus/data \
   -v /opt/consul-data \
   -v /var/lib/mysql \
   -v /var/lib/grafana \
   --name pmm-data \
   percona/pmm-server:1.1.3 /bin/true
   
# run pmm-server
docker run -d \
   -p 8081:80 \
   --volumes-from pmm-data \
   --name pmm-server \
   --restart always \
   percona/pmm-server:1.1.3
   
# verifying
http://IP:8081/
http://IP:8081/qan/
# admin/admin (default)
http://IP:8081/graph/
http://IP:8081/orchestrator/

# install client
yum install http://www.percona.com/downloads/percona-release/redhat/0.1-4/percona-release-0.1-4.noarch.rpm
yum install pmm-client

# connect pmm-server
pmm-admin config --server 121.42.195.197:8081

# start mysql and mongodb
service mysqld restart # /etc/init.d/mysqld restart
bin/mongod -f conf/mongodb.cnf

# add monitor
pmm-admin add mysql --user DBUSER --password DBUSERPASS
pmm-admin add mongodb

# show
pmm-admin list
```


##### mongodb.cnf

```
dbpath=/root/data/
logpath=mongodb.log
logappend=true
fork=true
verbose=true
vvvvv=true
```
