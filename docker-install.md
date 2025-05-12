---
title: CentOS 7 Docker安装
date: 2020-03-20 10:26:00
tags: [DOCKER, INSTALL]
categories: DOCKER
---

# Docker Install

```shell
shell> yum install -y yum-utils device-mapper-persistent-data lvm2
shell> yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
shell> yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
shell> yum install docker-ce docker-ce-cli containerd.io
shell> systemctl start docker
shell> curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
shell> chmod +x /usr/local/bin/docker-compose
shell> systemctl enable docker
shell> mkdir -p /etc/docker 
shell> tee /etc/docker/daemon.json <<-'EOF' 
{ 
  "registry-mirrors": ["https://ws7edg6v.mirror.aliyuncs.com"] 
}
EOF 
shell> systemctl daemon-reload 
shell> systemctl restart docker
```
