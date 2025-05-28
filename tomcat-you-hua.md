---
title: TOMCAT 8.53优化配置
date: 2025-05-28 15:33:00
tags: [TOMCAT, YOUHUA]
categories: TOMCAT
---

### TOMCAT优化配置

##### bin/catalina.sh

```shell
# ----- Execute The Requested Command -----------------------------------------
JAVA_OPTS="$JAVA_OPTS -server -Xms4096m -Xmx4096m -XX:MetaspaceSize=256m -XX:MaxMetaspaceSize=512m -XX:+UseG1GC -XX:MaxGCPauseMillis=200 -XX:ParallelGCThreads=4 -XX:ConcGCThreads=2 -XX:+DisableExplicitGC -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/usr/local/tomcat_dekt/logs/heapdump_%p.hprof -XX:ErrorFile=/usr/local/tomcat_dekt/logs/hs_err_%p.log -Djava.awt.headless=true -Djava.net.preferIPv4Stack=true -Dfile.encoding=UTF-8"
```

##### conf/server.xml

```
<Executor 
        name="tomcatThreadPool"
        namePrefix="catalina-exec-"
        maxThreads="400"
        minSpareThreads="100"
        prestartminSpareThreads="true"
        maxQueueSize="100"
        maxIdleTime="60000" />
<Connector 
        protocol="org.apache.coyote.http11.Http11NioProtocol" 
        port="8080"
        maxThreads="400"           
        minSpareThreads="100"     
        acceptCount="200"    
        connectionTimeout="20000"
        redirectPort="8443"
        enableLookups="false"     
        compression="on"      
        compressionMinSize="1024"
        compressableMimeType="text/html,text/xml,text/plain,application/json"
        disableUploadTimeout="true"
        keepAliveTimeout="30000"  
        maxKeepAliveRequests="100"
        maxConnections="10000"
        executor="tomcatThreadPool"
        URIEncoding="UTF-8"/>
```


