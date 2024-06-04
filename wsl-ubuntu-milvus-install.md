```
title: Windows WSL Ubuntu Milvus
date: 2024-06-04 21:56:00
tags: [MILVUS, INSTALL]
categories: LLM
```

### WSL Ubuntu Milvus Install with docker

```shell
shell> sudo apt-get update
shell> sudo apt-get install ca-certificates curl
shell> sudo install -m 0755 -d /etc/apt/keyrings
shell> sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
shell> sudo chmod a+r /etc/apt/keyrings/docker.asc
shell> echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
shell> sudo apt-get update
shell> sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
shell> sudo docker container ls -a
shell> curl -sfL https://raw.githubusercontent.com/milvus-io/milvus/master/scripts/standalone_embed.sh > standalone_embed.sh
shell> chmod u+x standalone_embed.sh
shell> bash standalone_embed.sh start
# stop
shell> bash standalone_embed.sh stop
# find milvus container's IP
shell> sudo docker container inspect YAO_DOCKER_CONTAINER_ID
shell> sudo docker run -p 8000:3000 -e MILVUS_URL=YOUR_MILVUS_CONTAINER_IP:19530 zilliz/attu:v2.4.0
# acess with broswer http://localhost:8000/#/
```


