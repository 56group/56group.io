---
title:  MySQL常用状态查看
date: 2017-10-03 09:56:00
tags: [MYSQL, 常用]
categories:  Database
---

# MySQL常用状态查看


### 查看本次MySQL服务运行时间(单位:秒)

```
mysql> show status like 'uptime';
```

###  查看SELECT执行次数

```
mysql> show [global] status like 'com_select';
```

###  查看INSERT执行次数

```
mysql> show [global] status like 'com_insert';
```

###  查看UPDATE执行次数

```
mysql> show [global] status like 'com_update';
```

###  查看DELETE执行次数

```
mysql> show [global] status like 'com_delete';
```

### 查看尝试连接到MySQL服务的连接数(不在乎连接成功不成功)

```
mysql> show status like 'connections';
```

### 查看线程缓存内的线程数

```
mysql> show status like 'threads_cached';
```

### 查看当前使用的线程数

```
mysql> show status like 'threads_connected';
```

### 查看创建用于连接的线程数(可以修改thread_cache_size)

```
mysql> show status like 'thread_created';
```

### 查看正在运行的线程数

```
mysql> show status like 'thread_running';
```

### 查看立即获得表锁的次数

```
mysql> show status like 'table_locks_immediate';
```

### 查看不能立即获得表锁的次数(如果数较大和存在性能瓶颈,可以优化查询)

```
mysql> show status like 'table_locks_waited';
```

### 查看创建时间超过slow_launch_time秒的线程数

```
mysql> show status like 'slow_launch_threads'; # show variables like 'slow_launch_time';
```

### 查看慢查询时间超过long_query_time秒的查询个数

```
mysql> show status like 'slow_queries'; # show variables like 'long_query_time';
```