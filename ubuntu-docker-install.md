```
title: Ubuntu Docker install
date: 2024-08-13 14:18:00
tags: [DOCKER, INSTALL]
categories: DOCKER
```

### Ubuntu Docker Install

##### 安装docker

```shell
shell> sudo apt-get update
shell> sudo apt-get install ca-certificates curl
shell> sudo install -m 0755 -d /etc/apt/keyrings
# 下面的下载如果下载不了,就是用自己的浏览器下载,然后把内容放到服务器里面
shell> sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
shell> sudo vi /etc/apt/keyrings/docker.asc
shell> sudo chmod a+r /etc/apt/keyrings/docker.asc
# 上面的三个操作可以换成国内源的 推荐更换
shell> sudo curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
shell> sudo chmod a+r /etc/apt/keyrings/docker.asc
shell> echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu \$(. /etc/os-release && echo "$VERSION_CODENAME") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
shell> sudo apt-get update
shell> sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
shell> sudo vi /etc/docker/daemon.json
shell> sudo systemctl daemon-reload
shell> sudo service docker restart
shell> sudo docker run hello-world
```

##### daemon.json

```
{
    "registry-mirrors": ["https://hub.uuuadc.top", "https://docker.anyhub.us.kg", "https://dockerhub.jobcher.com", "https://dockerhub.icu", "https://docker.ckyl.me", "https://docker.awsl9527.cn"]
}
```


