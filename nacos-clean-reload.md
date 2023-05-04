---
title: NACOS不更改数据重新拉取镜像
date: 2022-03-05 15:37:00
tags: [NACOS, DOCKER]
categories: NACOS
---

### NACOS不更改数据库重新拉取镜像

```shell
shell> docker container stop nacos
shell> docker container rm nacos
shell> docker image rm NACOS_IMAGE
shell> cd /data/nacos-docker/
shell> docker-compose -f example/standalone-mysql-8.yaml up -d
shell> docker exec -it nacos /bin/bash
```
