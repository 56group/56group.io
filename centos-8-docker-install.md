---
title: CentOS 8 DOCKER 安装
date: 2020-12-25 15:35:15
tags: [安装, DOCKER]
categories:  DOCKER
---

### [Docker安装](https://docs.docker.com/engine/install/centos/)

##### Docker Install

```shell
shell> yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
shell> yum install -y yum-utils
shell> yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
shell> yum install docker-ce docker-ce-cli containerd.io
shell> systemctl restart docker
shell> systemctl enable docker
```

##### 阿里云Docker镜像库配置

```shell
shell> mkdir -p /etc/docker 
shell> tee /etc/docker/daemon.json <<-'EOF' 
{ 
  "registry-mirrors": ["https://ws7edg6v.mirror.aliyuncs.com"] 
}
EOF 
shell> systemctl daemon-reload 
shell> systemctl restart docker
```

