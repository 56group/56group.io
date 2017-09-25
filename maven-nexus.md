---
title:  maven nexus
date: 2017-07-27 20:02:00
tags: [maven, nexus]
categories:  MAVEN
---

### maven nexus

```
<repositories>
    <repository>
        <id>nexus</id>
        <name>nexus</name>
        <url>http://IP:8081/nexus/content/groups/public/</url>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
    </repository>
  </repositories>

  <pluginRepositories>
    <pluginRepository>
        <id>nexus</id>
        <name>nexus</name>
        <url>http://IP:8081/nexus/content/groups/public/</url>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
    </pluginRepository>
  </pluginRepositories>

```
