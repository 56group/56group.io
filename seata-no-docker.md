---
title: Seata非Docker安装
date: 2022-10-21 15:11:00
tags: [SEATA, 安装]
categories: SEATA
---

### Seata非Docker安装

[下载](https://github.com/seata/seata/releases)

```shell
shell> tar -xvf seata-server-1.5.2.tar.gz
shell> mv seata /usr/local
shell> mysql -uroot -proot
mysql> CREATE SCHEMA `dlut_seata` DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
mysql> CREATE USER 'seata'@'%' IDENTIFIED BY 'seata123dlut';
mysql> GRANT ALL PRIVILEGES ON dlut_seata.* TO 'seata'@'%' WITH GRANT OPTION;
mysql> exit
shell> cd /usr/local/seata
shell> cd script/server/db/
shell> ls
shell> mysql -uroot -proot
mysql> use dlut_seata
mysql> source mysql.sql
mysql> exit
shell> cd /usr/local/seata
shell> cd conf
shell> cp application.yml application.yml.bak_yyyymmddhhmmss
shell> vi application.yml
```

##### nacos配置

```shell
shell> cd /usr/local/seata
shell> vi script/config-center/config.txt
shell> cd script/config-center/nacos
shell> sh nacos-config.sh -h YOUR_NACOS_IP -p YOUR_NACOS_PORT -g YOUR_NACOS_GROUP -t YOUR_NACOS_NAMESPACE -u nacos -w YOUR_NACOS_PASS
```

##### seata服务启动

```shell
shell> cd /usr/local/seata
# 注意1.5.2不好用
shell> bash /usr/local/seata/bin/seata-server.sh
shell> tail -f /usr/local/seata/logs/start.out
# 注意1.5.2需要如下方式启动
shell> cd /usr/local/seata/bin
shell> ./seata-server.sh
```

##### 在nacos中查看对应实例是否启动

##### application.yml

```yml
server:
  port: 7091

spring:
  application:
    name: seata-server

logging:
  config: classpath:logback-spring.xml
  file:
    path: ${user.home}/logs/seata
  # 注释elk日志
  #extend:
  #  logstash-appender:
  #    destination: 127.0.0.1:4560
  #  kafka-appender:
  #    bootstrap-servers: 127.0.0.1:9092
  #    topic: logback_to_logstash

console:
  user:
    username: seata
    password: YOUR_PASS

seata:
  config:
    # support: nacos, consul, apollo, zk, etcd3
    type: nacos
    nacos:
        server-addr: NACOS_IP:8848
        namespace: NACOS_SPACE_ID
        group: NACOS_GROUP
        username: NACOS_USER
        password: NACOS_PASS
        data-id: seata-server.properties
  registry:
    # support: nacos, eureka, redis, zk, consul, etcd3, sofa
    type: nacos
    nacos:
        application: seata-server
        server-addr: NACOS_IP:8848
        namespace: NACOS_SPACE_ID
        group: NACOS_GROUP
        username: NACOS_USER
        password: NACOS_PASS
        cluster: default
  store:
    # support: file ã€ db ã€ redis
    mode: db
    db:
        datasource: druid
        dbType: mysql
        driverClassName: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://202.118.69.50:3306/NACOS_DB?useUnicode=true&rewriteBatchedStatements=true&allowPublicKeyRetrieval=true
        user: SEATA_DB_USER
        password: SEATA_DB_PASS
        minConn: 5
        maxConn: 30
        globalTable: global_table
        branchTable: branch_table
        distributedLockTable: distributed_lock
        queryLimit: 100
        lockTable: lock_table
        maxWait: 5000
#  server:
#    service-port: 8091 #If not configured, the default is '${server.port} + 1000'
  security:
    secretKey: SeataSecretKey0c382ef121d778043159209298fd40bf3850a017
    tokenValidityInMilliseconds: 1800000
    ignore:
      urls: /,/**/*.css,/**/*.js,/**/*.html,/**/*.map,/**/*.svg,/**/*.png,/**/*.ico,/console-fe/public/**,/api/v1/auth/login
```

##### script/config-center/config.txt

```properties
#For details about configuration items, see https://seata.io/zh-cn/docs/user/configurations.html
#Transport configuration, for client and server
transport.type=TCP
transport.server=NIO
transport.heartbeat=true
transport.enableTmClientBatchSendRequest=false
transport.enableRmClientBatchSendRequest=true
transport.enableTcServerBatchSendResponse=false
transport.rpcRmRequestTimeout=30000
transport.rpcTmRequestTimeout=30000
transport.rpcTcRequestTimeout=30000
transport.threadFactory.bossThreadPrefix=NettyBoss
transport.threadFactory.workerThreadPrefix=NettyServerNIOWorker
transport.threadFactory.serverExecutorThreadPrefix=NettyServerBizHandler
transport.threadFactory.shareBossWorker=false
transport.threadFactory.clientSelectorThreadPrefix=NettyClientSelector
transport.threadFactory.clientSelectorThreadSize=1
transport.threadFactory.clientWorkerThreadPrefix=NettyClientWorkerThread
transport.threadFactory.bossThreadSize=1
transport.threadFactory.workerThreadSize=default
transport.shutdown.wait=3
transport.serialization=seata
transport.compressor=none

#Transaction routing rules configuration, only for the client
# service.vgroupMapping.default_tx_group=default
service.vgroupMapping.YOUR_PREFIX_tx_group=default
# 上面的vgroupMapping中YOUR_PREFIX自定义但是一定要可client的的配置值一致 客户端yml配置的项是tx-service-group
#If you use a registry, you can ignore it
# service.default.grouplist=127.0.0.1:8091
service.default.grouplist=YOUR_SEATA_IP:8091
service.enableDegrade=false
service.disableGlobalTransaction=false

#Transaction rule configuration, only for the client
client.rm.asyncCommitBufferLimit=10000
client.rm.lock.retryInterval=10
client.rm.lock.retryTimes=30
client.rm.lock.retryPolicyBranchRollbackOnConflict=true
client.rm.reportRetryCount=5
client.rm.tableMetaCheckEnable=true
client.rm.tableMetaCheckerInterval=60000
client.rm.sqlParserType=druid
client.rm.reportSuccessEnable=false
client.rm.sagaBranchRegisterEnable=false
client.rm.sagaJsonParser=fastjson
client.rm.tccActionInterceptorOrder=-2147482648
client.tm.commitRetryCount=5
client.tm.rollbackRetryCount=5
client.tm.defaultGlobalTransactionTimeout=60000
client.tm.degradeCheck=false
client.tm.degradeCheckAllowTimes=10
client.tm.degradeCheckPeriod=2000
client.tm.interceptorOrder=-2147482648
client.undo.dataValidation=true
client.undo.logSerialization=jackson
client.undo.onlyCareUpdateColumns=true
server.undo.logSaveDays=7
server.undo.logDeletePeriod=86400000
client.undo.logTable=undo_log
client.undo.compress.enable=true
client.undo.compress.type=zip
client.undo.compress.threshold=64k
#For TCC transaction mode
tcc.fence.logTableName=tcc_fence_log
tcc.fence.cleanPeriod=1h

#Log rule configuration, for client and server
log.exceptionRate=100

#Transaction storage configuration, only for the server. The file, DB, and redis configuration values are optional.
#store.mode=file
store.mode=db
#store.lock.mode=file
store.lock.mode=db
#store.session.mode=file
store.session.mode=db
#Used for password encryption
#store.publicKey=

#If `store.mode,store.lock.mode,store.session.mode` are not equal to `file`, you can remove the configuration block.
store.file.dir=file_store/data
store.file.maxBranchSessionSize=16384
store.file.maxGlobalSessionSize=512
store.file.fileWriteBufferCacheSize=16384
store.file.flushDiskMode=async
store.file.sessionReloadReadSize=100

#These configurations are required if the `store mode` is `db`. If `store.mode,store.lock.mode,store.session.mode` are not equal to `db`, you can remove the configuration block.
store.db.datasource=druid
store.db.dbType=mysql
store.db.driverClassName=com.mysql.jdbc.Driver
store.db.url=jdbc:mysql://YOUR_MYSQL_IP:3306/YOUR_DB?useUnicode=true&rewriteBatchedStatements=true
# jdbc:mysql://YOUR_MYSQL_IP:3306/YOUR_DB?useUnicode=true&characterEncoding=utf8&useSSL=false&&serverTimezone=UTC
store.db.user=YOUR_USER
store.db.password=YOUR_PASS
store.db.minConn=5
store.db.maxConn=30
store.db.globalTable=global_table
store.db.branchTable=branch_table
store.db.distributedLockTable=distributed_lock
store.db.queryLimit=100
store.db.lockTable=lock_table
store.db.maxWait=5000

#These configurations are required if the `store mode` is `redis`. If `store.mode,store.lock.mode,store.session.mode` are not equal to `redis`, you can remove the configuration block.
store.redis.mode=single
store.redis.single.host=127.0.0.1
store.redis.single.port=6379
store.redis.sentinel.masterName=
store.redis.sentinel.sentinelHosts=
store.redis.maxConn=10
store.redis.minConn=1
store.redis.maxTotal=100
store.redis.database=0
store.redis.password=
store.redis.queryLimit=100

#Transaction rule configuration, only for the server
server.recovery.committingRetryPeriod=1000
server.recovery.asynCommittingRetryPeriod=1000
server.recovery.rollbackingRetryPeriod=1000
server.recovery.timeoutRetryPeriod=1000
server.maxCommitRetryTimeout=-1
server.maxRollbackRetryTimeout=-1
server.rollbackRetryTimeoutUnlockEnable=false
server.distributedLockExpireTime=10000
server.xaerNotaRetryTimeout=60000
server.session.branchAsyncQueueSize=5000
server.session.enableBranchAsyncRemove=false
server.enableParallelRequestHandle=false

#Metrics configuration, only for the server
metrics.enabled=false
metrics.registryType=compact
metrics.exporterList=prometheus
metrics.exporterPrometheusPort=9898
```

##### Seata的nacos的对外IP配置

```shell
shell> cd /usr/local/seata
shell> cd bin
shell> vi seata-server.sh 
```

##### seata-server.sh添加IP配置

```shell
export SEATA_IP="YOUR_IP"
# resolve links - $0 may be a softlink (在此行之上添加)
```

> 如果使用MySQL需要注意nacos的seata的配置store.db.driverClassName=com.mysql.cj.jdbc.Driver需要加上cj
