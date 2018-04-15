---
title:  常用命令
date: 2017-07-27 20:43:00
tags: [常用]
categories:  常用
---

### 常用命令

##### MySQL Create user
```
mysql> CREATE USER 'monty'@'localhost' IDENTIFIED BY 'some_pass';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'monty'@'localhost' WITH GRANT OPTION;
mysql> CREATE USER 'monty'@'%' IDENTIFIED BY 'some_pass';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'monty'@'%' WITH GRANT OPTION;
mysql> CREATE USER 'admin'@'localhost' IDENTIFIED BY 'admin_pass';
mysql> GRANT RELOAD,PROCESS ON *.* TO 'admin'@'localhost';
mysql> CREATE USER 'dummy'@'localhost';
```

##### MySQL Create database

```
CREATE SCHEMA `new_schema` DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
```

##### MySQL逻辑备份

```
mysqldump -h HOST -u USER -pPASS DB_NAME > DB_NAME_`date "+%Y_%m_%d_%H_%M_%S"`.sql
```

#####MySQL表操作

```
# mysql更改列的字符集
mysql> ALTER TABLE table_name CHANGE col_name col_name VARCHAR(100) CHARACTER SET utf8 COLLATE utf8_unicode_ci;
# mysql表列字符集查看
mysql> SHOW FULL COLUMNS FROM table_name;
```

##### MongoDB Create document

```
use mydb # 插入数据才能 show dbs
``

##### MongoDB Permission config

```
use mydb
db.createUser( { "user" : "accountAdmin01",
                 "pwd": "cleartext password",
                 "customData" : { employeeId: 12345 },
                 "roles" : [ { role: "clusterAdmin", db: "admin" },
                             { role: "readAnyDatabase", db: "admin" },
                             "readWrite"
                             ] },
               { w: "majority" , wtimeout: 5000 } )
# the clusterAdmin and readAnyDatabase roles on the admin database
# the readWrite role on the mydb database
db.createUser(
   {
     user: "accountUser",
     pwd: "password",
     roles: [ "readWrite", "dbAdmin" ]
   }
)
# creates accountUser in the mydb database and gives the user the readWrite and dbAdmin roles.
```

##### MongoDB Drop user

```
db.dropUser("reportUser1", {w: "majority", wtimeout: 5000})
```

##### MongoDB Create db

```
# 添加数据库
use newdb
# 添加用户
db.createUser(
   {
     user: "accountUser",
     pwd: "password",
     roles: [ "readWrite", "dbAdmin" ]
   }
)
# 测试
mongo -u accountUser -p password localhost/newdb
```

##### MongoDB 备份

```
shell> mongodump -d YOUR_DB_NAME -o YOUR_STORE_PATH
shell> tar -cvf YOUR_STORE_NAME_`date "+%Y-%m-%d"`.tar.gz YOUR_STORE_PATH/YOUR_DB_NAME/
```


##### PHP Start cgi

```
php-cgi.exe -b 127.0.0.1:9000 -c php.ini
```

##### Maven create web project

```
# curl http://repo1.maven.org/maven2/archetype-catalog.xml #保存到本地库目录里面
mvn archetype:generate -DarchetypeCatalog=local -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-webapp -DarchetypeVersion=1.0 -DgroupId=taoli.dubbo -DartifactId=taoliWeb -Dversion=1.0-SNAPSHO
```

##### Linux daemon process

```
nohup command > PATH_TO_LOG 2>&1 &
```
##### Redmine

```
shell> ruby bin/rails server webrick -e production -d -b 127.0.0.1 -p 3000
```