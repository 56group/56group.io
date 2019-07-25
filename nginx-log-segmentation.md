---
title:  Nginx日志分割
date: 2017-10-03 09:56:00
tags: [NGINX, LOG]
categories:  Linux
---

# nginx log segmentation

> 正常安装nginx服务

### 编写脚本

```
LOGS_PATH="/home/log/nginx/logs"
ARCHIVE_YEAR=$(date -d "yesterday" "+%Y")
ARCHIVE_MONTH=$(date -d "yesterday" "+%m")
if [ -r /opt/nginx/nginx.pid ]; then
  mkdir -p "${LOGS_PATH}/${ARCHIVE_YEAR}/${ARCHIVE_MONTH}"
  mv "${LOGS_PATH}/access.log" "${LOGS_PATH}/${ARCHIVE_YEAR}/access_${ARCHIVE_MONTH}.log"
  kill -USR1 $(cat "/nginx/nginx.pid")
  sleep 1
  gzip "${LOGS_PATH}/${ARCHIVE_YEAR}/access_${ARCHIVE_MONTH}.log"
else
  echo "Nginx might be down"
fi
`
```
### 编写任务

```
# 00 00 0 * * /bin/bash  /home/log/nginx/cut_nginx_logs.sh 
shell> crontab -e # 添加上面的任务
```
### 日志分析