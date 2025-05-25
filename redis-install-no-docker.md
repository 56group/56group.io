---
title: Redis非docker安装
date: 2022-06-29 13:47:00
tags: [REDIS, INSTALL]
categories:  REDIS
---

### Redis安装

##### [Redis下载](https://redis.io/download/#redis-stack-downloads)

```
shell> yum install tcl -y
shell> tar -xvf redis-6.2.7.tar.gz -C /usr/local/
shell> cd /usr/local/redis-6.2.7/
shell> make
shell> make install # 这一步操作安装的文件会安装到/usr/local/bin下,可以不执行
shell> ls /usr/local/bin/ # 查看安装文件
shell> ln -s /usr/local/redis-6.2.7/ /usr/local/redis
shell> cd /usr/local/redis
shell> cd src
shell> ./redis-server # 启动redis服务,测试是否可以启动 CTRL+C停止服务
shell> cd /usr/local/redis
shell> cp redis.conf redis.conf.bak_YYYYMMDDHHMM
shell> vi redis.conf
shell> /usr/local/bin/redis-server /usr/local/redis/redis.conf # 启动服务
shell> /usr/local/bin/redis-cli -a YOUR_PASS shutdown # 关闭redis服务
shell> vi /etc/systemd/system/redis.service
shell> systemctl daemon-reload
shell> systemctl enable redis
shell> systemctl start redis
```

##### redis.conf

```
#bind 127.0.0.1 -::1 # 注释
bind 0.0.0.0 # 新增 远程访问
#daemonize no # 注释
daemonize yes # 新增 守护进程运行
requirepass YOUR_PASS # 访问密码
port 6379
dir ./
databases 16
maxmemory 1gb # 配置最大使用内存大小
maxmemory-policy volatile-lru # 配置淘汰策略
#logfile "" # 注释
logfile "redis.log" # 新增 配置日志记录文件
```

##### 需要注意redis是否开启持久化

```
# RDB 配置
save 10 1                # 10秒内1次修改触发快照（加速测试）
dbfilename dump_test.rdb # 测试专用文件名
dir /tmp/redis_test      # 指定测试目录（避免干扰生产数据）

# AOF 配置
appendonly yes
appendfilename "appendonly_test.aof"
appendfsync everysec     # 平衡性能与安全性
auto-aof-rewrite-percentage 50  # 降低重写阈值
auto-aof-rewrite-min-size 16mb  # 降低最小重写大小

# 混合模式（可选）
aof-use-rdb-preamble yes
```

##### 开启持久化策略

```shell
shell> vi /etc/sysctl.conf
vm.overcommit_memory = 1 # 添加这个值
shell> sysctl -p
```

##### redis.service

```
[Unit]
Description=redis-server
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/bin/redis-server /usr/local/redis/redis.conf
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```
