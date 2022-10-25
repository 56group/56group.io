---
title: MongoDB安装
date: 2018-08-15 08:29:00
tags: [MONGODB, 环境]
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
shell> vi mongo.conf # 配置内容如文件 mongo.conf
shell> bin/mongod -f mongo.conf
shell> ps aux | grep mongod
shell> bin/mongo
mongo> db.createUser({
  user:"admin",
  pwd:"YOUR_PASS",
  roles: [
    { 
      role: "root", 
      db: "admin" 
    }
  ]
})
shell> bin/mongo
mongo> use admin
mongo> db.auth('admin', 'YOUR_PASS')
```

##### Mongodb新建数据和数据库用户访问(只有读写)

```
shell> bin/mongo
mongo> use admin
mongo> db.auth('admin', 'YOUR_PASS')
mongo> use YOUR_DB
mongo> db.createUser({
  user:"YOUR_USER",
  pwd:"YOUR_PASS",
  roles: [
    { 
      role: "readWrite", 
      db: "YOUR_DB" 
    }
  ]
})
```

##### 删除用户

```
shell> bin/mongo
mongo> use admin
mongo> db.auth('admin', 'YOUR_PASS')
mongo> db.dropUser('YOUR_USER')
```

##### 更改密码

```
shell> bin/mongo
mongo> use admin
mongo> db.auth('admin', 'YOUR_PASS')
mongo> db.changeUserPassword('YOUR_USER','YOUR_PASS')
```

##### mongo.conf

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
   timeZoneInfo: /usr/share/zoneinfo
net:
   bindIp: 127.0.0.1 # 外网访问 0.0.0.0 注意外网须开启认证
   port: 27017
setParameter:
   enableLocalhostAuthBypass: true # 在最开始初始化数据库用户的时候设置
security:
   authorization: enabled
```

##### liblzma

```shell
shell> yum install xz-compat-libs
```

##### [MongoDB用户](https://www.mongodb.com/docs/manual/tutorial/create-users/)

##### [配置选项](https://www.mongodb.com/docs/manual/reference/configuration-options)
