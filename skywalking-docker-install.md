---
title: SkyWalking Docker安装
date: 2021-12-01 22:08:00
tags: [安装, SKYWLKING]
categories: DOCKER
---

### Skywalking安装

```
shell> docker run --name skywalking-backend -p 12800:12800 -p 11800:11800 --restart always -d -e SW_STORAGE=elasticsearch -e SW_STORAGE_ES_CLUSTER_NODES=114.115.210.82:9200 -e SW_CORE_RECORD_DATA_TTL=2 -e SW_CORE_METRICS_DATA_TTL=3 apache/skywalking-oap-server:8.8.0

shell> docker run --name skywalking-ui -p 10000:8080 --restart always -d -e SW_OAP_ADDRESS=http://skywalking-backend:12800 apache/skywalking-ui:8.8.0
```

