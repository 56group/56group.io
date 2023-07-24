---
title: Flask项目发布
date: 2023-07-20 18:50:00
tags: [INSTALL,FLASK]
categories: FLASK

---

### Flask项目发布

##### 安装OpenSSL 1.1.1[下载](https://www.openssl.org/source/openssl-1.1.1u.tar.gz)

```shell
# linux自带的openssl版本太低
shell> yum remove openssl openssl-devel
shell> tar -xvf openssl-1.1.1u.tar.gz
shell> cd openssl-1.1.1u
shell> ./config
shell> make
shell> make install
```

##### 安装Python 3.10[下载](https://www.python.org/ftp/python/3.10.10/Python-3.10.10.tar.xz)

```shell
shell> yum -y groupinstall "Development tools"
shell> yum -y install zlib-devel bzip2-devel openssl11 openssl11-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
shell> yum install -y libffi-devel zlib1g-dev
shell> yum install zlib* -y
shell> tar -xvf Python-3.10.10.tar.xz
shell> cd Python-3.10.10
shell> ./configure --prefix=/usr/local/python310 --with-ssl
shell> make
shell> make install
```

##### Gunicorn安装 [官网文档](https://docs.gunicorn.org/en/stable/settings.html#config)

```shell
# 上传代码到服务器 打包的项目代码中不包含python的虚环境
shell> unzip YOUR_PROJECT.zip
shell> cd YOUR_PROJECT_DIR
shell> /usr/local/python310/bin/virtualenv -p /usr/local/python310/bin/python3
shell> source venv/bin/activate
shell> pip3 install -r requirement.txt
shell> pip3 install gunicorn greenlet eventlet gevent gthread
shell> vi gunicorn_config.py
shell> gunicorn -c gunicorn_config.py app:app
shell> netstat -npl | grep YOUR_PORT
shell> ps aux | grep YOUR_PROC
```

##### supervisor安装[官网](https://github.com/Supervisor/supervisor)

```shell
shell> /usr/local/python310/bin/pip3 install supervisor
shell> /usr/local/python310/bin/supervisorctl --help
shell> mkdir /etc/supervisor
shell> /usr/local/python310/bin/echo_supervisord_conf > /etc/supervisor/supervisord.conf
shell> cat /etc/supervisor/supervisord.conf
shell> vi /etc/supervisor/supervisord.conf
shell> mkdir /var/log/supervisor
shell> touch /var/log/supervisor/supervisord.log
shell> mkdir /etc/supervisor/conf.d
shell> vi /usr/lib/systemd/system/supervisor.service
shell> systemctl enable supervisor
shell> vi /etc/supervisor/conf.d/supervisor_ddi.conf
shell> systemctl start supervisor
shell> ps aux | grep supervisor
shell> ps aux | YOUR_PROC
```

##### 配置nginx

```roboconf
shell> cd /usr/local/nginx/conf
shell> cp nginx.conf nginx.conf.bak_YYYYMMDDHHMM
shell> vi nginx.conf
shell> /usr/local/nginx/sbin/nginx -s reload
# 通过域名访问服务
```



##### gunicorn_config.py

```txt
"""
gunicorn wsgi服务配置文件
"""
import os
import logging
import logging.handlers
from logging.handlers import WatchedFileHandler
import multiprocessing

# 应用绑定的IP和端口
bind = 'YOUR_IP:10050'
# 设置超时时间
timeout = '30'
# 设置日志目录
if not os.path.exists('log/'):
    os.makedirs('log/')

loglevel = 'debug'

# 设置日志格式,错误日志不能设置
access_log_format = '%(t)s %(p)s %(h)s "%(r)s" %(s)s %(L)s %(b)s %(f)s" "%(a)s"'

"""
其每个选项的含义如下：
h          remote address
l          '-'
u          currently '-', may be user name in future releases
t          date of the request
r          status line (e.g. ``GET / HTTP/1.1``)
s          status
b          response length or '-'
f          referer
a          user agent
T          request time in seconds
D          request time in microseconds
L          request time in decimal seconds
p          process ID
"""

errorlog = 'log/error.log'
accesslog = 'log/access.log'
pidfile = 'log/gunicorn.pid'

# 设置工作模式 默认sync模式,设置为gevent模式
worker_class = 'gevent'
# 设置工作(worker)进程数 推荐值: CPU数量 * 2 + 1
workers = multiprocessing.cpu_count() * 2 + 1
# 最大连接数
worker_connections = 1000
# 是否守护进程运行, 如果启动时候存在问题,把值调整为False,在启动控制台看错误信息,调整错误配置后把值调整为True
daemon = True
# 进程名
proc_name = 'yunzhu_ddi_gunicorn.proc'
# 持久连接秒数
keeplive = 3
# python环境路径
pythonpath = '/root/yunzhu/dingwei-draw-image/venv/bin/python3'
# 等待连接最大数量
backlog = 2048
```

##### /etc/supervisor/supervisord.conf可自定义项

```conf
[unix_http_server]
file=/tmp/supervisor.sock
[supervisord]
;logfile=/tmp/supervisord.log
logfile=/var/log/supervisord.log
;pidfile=/tmp/supervisord.pid
pidfile=/var/run/supervisord.pid
[supervisorctl]
serverurl=unix:///tmp/supervisor.sock
[include]
files = /etc/supervisor/conf.d/*.conf
```

##### /usr/lib/systemd/system/supervisor.service

```conf
[Unit] 
Description=Supervisor daemon 
[Service] 
Type=forking
ExecStart=/usr/local/python310/bin/supervisord -c /etc/supervisor/supervisord.conf 
ExecStop=/usr/local/python310/bin/supervisorctl shutdown 
ExecReload=/usr/local/python310/bin/supervisorctl reload 
KillMode=process
Restart=on-failure 
RestartSec=42s 
[Install] 
WantedBy=multi-user.target
```

##### /etc/supervisor/conf.d/supervisor_ddi.conf

```roboconf
# 服务名称
[program:ddi]
# 项目路径,先切换到项目目录
directory = /root/yunzhu/dingwei-draw-image
# 启动服务命令
command = /root/yunzhu/dingwei-draw-image/venv/bin/python3 /root/yunzhu/dingwei-draw-image/venv/bin/gunicorn -c /root/yunzhu/dingwei-draw-image/gunicorn_config.py app:app
# 服务在supervisor启动后启动
autostart = true
# 服务挂掉后自动重启
autorestart = true
# 服务的启动用
user=root
# 将stderr定向到stdout
redirect_stderr = true
# 服务启动多少秒后状态是running表示启动成功
startsecs = 5
# 启动失败后尝试再次启动的次数
startretries = 5
# 服务日志输出,None表示不输出
stdout_logfile = None
stderr_logfile = None
```

##### nginx.conf

```roboconf
server {
    listen 80;
    server_name ddi.zhulin.xin;
    return  301 htps://$server_name$request_uri;
}

server {
    listen      443 ssl;
    server_name  ddi.zhulin.xin;
    ssl_certificate      /usr/local/nginx/conf/ssl/ddi.zhulin.xin.pem;
    ssl_certificate_key  /usr/local/nginx/conf/ssl/ddi.zhulin.xin.key;

    ssl_session_timeout  5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers  on;

    location / {
        proxy_pass http://localhost:10050;
    }
}
```
