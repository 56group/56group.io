---
title:  Springboot项目部署
date: 2020-08-16 16:49:00
tags: [SPRINGBOOT, 部署]
categories:  JAVA
---

###Springboot项目部署

##### JAR项目部署

```
# 配置server port application.yml
server:
  port: 9999

# 项目发布
shell> /usr/bin/nohup /usr/local/jdk/bin/java -jar /usr/local/src/bh-wx-middleware.jar > zhongshan-bh-wx-middleware-log.txt 2>&1 &
```



