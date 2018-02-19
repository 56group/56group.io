---
title: Java的Oracle JDBC链接
date: 2017-07-27 20:00:00
tags: [JAVA, JDBC, Oracle]
categories: JAVA
---

### MAVEN oracle jdbc

```
# download oracle jdbc
[addr](http://www.oracle.com/technetwork/database/features/jdbc/index-091264.html)
# create mvn dependency need GAV
mvn install:install-file -DgroupId=com.oracle -DartifactId=YOUR_ID -Dversion=YOUR_VERSION -Dpackaging=jar -Dfile=YOUR_FILE
# add dependency in pom.xml
<dependency>
    <groupId>com.oracle</groupId>
    <artifactId>YOUR_ID</artifactId>
    <version>YOUR_VERSION</version>
</dependency>
```
