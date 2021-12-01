---
title: YAPI数据迁移
date: 2020-10-17 16:08:00
tags: [YAPI]
categories: YAPI
---

### YAPI数据迁移

##### Mongodb数据库备份

```
docker shell> mongodump --authenticationDatabase admin -uyapi -pyapi -d yapi -o /data/bak/
```

##### 新建mongodb docker

```
shell> docker run -d -p 27017:27017 --name="yunzhu-mongodb" -v /data/mongodb:/data/db -e MONGO_INITDB_ROOT_USERNAME=yunzhu -e MONGO_INITDB_ROOT_PASSWORD=yunzhu mongo --auth
```

##### 新建mongodb用户

```
shell> mongo -uyunzhu -pyunzhu
> use yapi
> db.createUser(
   {
     user: "yapi",
     pwd: "yapi",
     roles: [ "readWrite", "dbAdmin" ]
   }
)
```

##### 还原yapi数据库

```
mongodb docker shell> mongorestore -uyapi -pyapi -d yapi /yapi/
```

##### 新建YAPI docker

```
shell> docker container run -d --name="yapi" --privileged="true" -p 9001:9001 -v /data/yapi:/data centos:centos8 /sbin/init
```

##### Node.js安装

```
docker yapi> tar -xvf node-v16.13.0-linux-x64.tar.xz
docker yapi> ln -s /data/node-v16.13.0-linux-x64 /data/node
docker yapi> vi /etc/profile.d/env.sh
docker yapi> chmod u+x /etc/profile.d/env.sh
docker yapi> source /etc/profile.d/env.sh
```

##### env.sh

```
export NODE_HOME=/data/node
export PATH=$NODE_HOME/bin:$PATH
```

##### YAPI安装

```
# 上传备份的yapi code,从被迁移服务器上备份原来的yapi code
shell> tar -cvf yapi_code.tar yapi
# 在另一台服务器展开
shell> tar -xvf yapi_code.tar
```

##### config.json

```
{
  "port": "9001",
  "adminAccount": "admin@admin.com",
  "timeout":120000,
  "closeRegister":true,
  "db": {
    "servername": "192.168.0.20",
    "DATABASE": "yapi",
    "port": 27017,
    "user": "yapi",
    "pass": "yapi",
    "authSource": "yapi"
  },
  "mail": {
    "enable": true,
    "host": "smtp.163.com",
    "port": 465,
    "from": "weiniyangfg@163.com",
    "auth": {
      "user": "weiniyangfg@163.com",
      "pass": "ForGirl5211314"
    }
  }
}
```

##### 启动YAPI

```
docker shell> nohup node /data/yapi/vendors/server/app.js > /data/yapi/yapi.log 2>&1 &
```

