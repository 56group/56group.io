---
title: EFK安装
date: 2021-12-01 10:46:00
tags: [EFK, INSTALL]
categories: EFK
---

### ### EFK安装

##### JDK安装

```
shell> tar -xvf jdk-11.0.13_linux-x64_bin.tar.gz -C /usr/local/
shell> ln -s /usr/local/jdk-11.0.13/ /usr/local/jdk
shell> vi /etc/profile.d/env.sh
shell> chmod u+x /etc/profile.d/env.sh
shell> source /etc/profile.d/env.sh
```

##### /etc/profile.d/env.sh

```
export ES_JAVA_HOME=/usr/local/jdk
export PATH=$ES_JAVA_HOME/bin:$PATH
```

##### elasticsearch安装

```
shell> tar -xvf elasticsearch-7.15.2-linux-x86_64.tar.gz -C /usr/local/
shell> ln -s /usr/local/elasticsearch-7.15.2/ /usr/local/elasticsearch
shell> cd /usr/local/elasticsearch
shell> rm -rf LICENSE.txt NOTICE.txt README.asciidoc
shell> groupadd elasticsearch
shell> useradd -r -g elasticsearch elasticsearch
shell> passwd elasticsearch
shell> mkdir /var/elk/data /var/elk/logs
shell> chown -R elasticsearch.elasticsearch /usr/local/elasticsearch-7.15.2/
shell> chown -R elasticsearch.elasticsearch /var/elk
shell> vi config/elasticsearch.yml
shell> vi /etc/sysctl.conf
shell> sysctl -p
shell> vi config/jvm.options
```

##### elasticsearch.yml

```
cluster.name: yunzhu
node.name: 114.115.210.82
path.data: /var/elk/data
path.logs: /var/elk/logs
# 如果是对外IP要配置为0.0.0.0
network.host: 0.0.0.0
http.port: 9200
discovery.seed_hosts: ["114.115.210.82"]
cluster.initial_master_nodes: ["114.115.210.82"]
```

##### jvm.options

```
-Xms4g
-Xmx4g
```

##### sysctl.conf

```
# 在文件最后面加
vm.max_map_count=262144
```

##### elasticsearch启动

```
shell> su elasticsearch
shell> cd /usr/local/elasticsearch
shell> bin/elasticsearch -d
shell> netstat -npl | grep 9200
shell> netstat -npl | grep 9300
```

##### elasticsearch访问

```
# 114.115.210.82:9200
{
  "name" : "114.115.210.82",
  "cluster_name" : "yunzhu",
  "cluster_uuid" : "_na_",
  "version" : {
    "number" : "7.15.2",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "93d5a7f6192e8a1a12e154a2b81bf6fa7309da0c",
    "build_date" : "2021-11-04T14:04:42.515624022Z",
    "build_snapshot" : false,
    "lucene_version" : "8.9.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

##### Kibana安装

```
shell> tar -xvf kibana-7.15.2-linux-x86_64.tar.gz -C /usr/local/
shell> ln -s /usr/local/kibana-7.15.2-linux-x86_64/ /usr/local/kibana
shell> cd /usr/local/kibana
shell> vi config/kibana.yml
shell> groupadd kibana
shell> useradd -r -g kibana kibana
shell> passwd kibana
shell> chown -R kibana.kibana /usr/local/kibana-7.15.2-linux-x86_64/
shell> su kibana
shell> sbin/kibana &
```

##### kibana.yml

```
server.port: 5601
# 外网访问
server.host: "0.0.0.0"
server.name: "yunzhu-kibana"
elasticsearch.hosts: ["http://114.115.210.82:9200"]
i18n.locale: "zh-CN"
```

##### Kibana访问

```
# 访问地址
114.115.210.82:5601
```

##### Filebeat安装(每一个采集的机器都要安装)

```
shell> tar -xvf filebeat-7.15.2-linux-x86_64.tar.gz -C /usr/local/
shell> ln -s /usr/local/filebeat-7.15.2-linux-x86_64/ /usr/local/filebeat
shell> cd /usr/local/filebeat
shell> vi filebeat.yml
shell> ./filebeat -e -c /usr/local/filebeat/filebeat.yml
```

##### filebeat.yml

```
- type: log

  enabled: true
  backoff: "10s"
  tail_files: false

  paths:
    - /var/log/*.log
  #exclude_lines: ['^DBG']
  #include_lines: ['^ERR', '^WARN']
  exclude_files: ['.gz$']
  multiline.pattern: ^\[
  multiline.negate: false
  multiline.match: after
```

