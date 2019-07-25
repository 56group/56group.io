---
title: SpringBoot IDEA热部署
date: 2018-06-11 15:46:00
tags: [SPRINGBOOT, IDEA]
categories: IDE
---

### SpringBoot IDEA热部署

##### pom.xml的dependency

```
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-devtools</artifactId>
   <!--<scope>runtime</scope>-->
  <optional>true</optional>
</dependency>
```
##### pom.xml的build

```
<plugin>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-maven-plugin</artifactId>
   <configuration>
      <fork>true</fork>
   </configuration>
   <!--<addResources>true</addResources>--> </plugin>
```
##### application.yml关闭缓存 

```
spring:
  thymeleaf:
    cache: false
```
##### 开启自动编译

```
// 在IDEA的配置中配置自动化编译
File -> Settings -> Build, Execution, Deployment -> Compiler -> Build project automatically //勾选
// apply
```
##### 配置Registry (Ctrl+Shift+A => Registry)

```
compiler.automake.allow.when.app.running //勾选 -> Close
```