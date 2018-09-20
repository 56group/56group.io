---
title: MongoDB安装
date: 2018-08-15 08:29:00
tags: [MongoDB, 环境]
categories: MongoDB
---


### MongoDB安装

```shell
shell> wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel62-4.0.1.tgz
shell> tar zxvf mongodb-linux-x86_64-rhel62-4.0.1.tgz -C /usr/local/
shell> cd /usr/local/
shell> ln -s /usr/local/mongodb-linux-x86_64-rhel62-4.0.1/ /usr/local/mongodb
shell> cd mongodb
shell> rm -rf GNU-AGPL-3.0 LICENSE-Community.txt MPL-2 README THIRD-PARTY-NOTICES
shell> mkdir data
shell> vi mongos.conf # 配置内容如文件 mongos.conf
shell> bin/mongod -f mongos.conf
shell> ps aux | grep mongod
shell> bin/mongo
```

##### mongos.conf

```
systemLog:
   destination: file
   path: "/usr/local/mongodb/mongod.log"
   logAppend: true
storage:
   dbPath: /usr/local/mongodb/data
   journal:
      enabled: true
processManagement:
   fork: true
net:
   bindIp: 127.0.0.1
   port: 27017
setParameter:
   enableLocalhostAuthBypass: false
```
