---
title: CentOS Milvus install
date: 2024-11-05 11:14:00
tags: [DOCKER, MILVUS, INSTALL]
categories: MILVUS
---

##### CentOS Docker Install

```shell
shell> yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
shell> vi /etc/docker/daemon.json
shell> systemctl restart docker
shell> systemctl docker stop
shell> systemctl stop docker.socket
shell> systemctl daemon-reload
shell> docker start docker
shell> docker run hello-world
```

##### /etc/docker/daemon.json

```json
{
    "registry-mirrors": ["https://hub.uuuadc.top", "https://docker.anyhub.us.kg", "https://dockerhub.jobcher.com", "https://dockerhub.icu", "https://docker.ckyl.me", "https://docker.awsl9527.cn"]
}
```

##### Docker参考地址

[连接]([docker镜像加速源配置，目前可用镜像源列举(10月10日更新最新可用)_docker可用的镜像源-CSDN博客](https://blog.csdn.net/llc580231/article/details/139979603))

##### Docker Compose安装

```shell
# 下载文件看运气,如果下载不了可以用windows下载然后上传后重命名
shell> curl -SL https://github.com/docker/compose/releases/download/v2.30.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
shell> ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
shell> chmod u+x /usr/local/bin/docker-compose
shell> chmod u+x /usr/bin/docker-compose
```

##### 更改milvus.yaml配置文件,开启认证

```shell
shell>  wget https://raw.githubusercontent.com/milvus-io/milvus/v2.4.15/configs/milvus.yaml
```

##### 更改内容

```yaml
...
common:
...
  security:
    authorizationEnabled: true
...
```

##### Milvus安装

```shell
shell> wget https://github.com/milvus-io/milvus/releases/download/v2.4.14/milvus-standalone-docker-compose.yml -O docker-compose.yml
shell> vi docker-compose.yml
# 阅读上面下载的文件根据端口需要调整端口映射关系
shell> docker-compose up -d
# 停止服务 需要在包含上面docker-compose.yml文件目录下
shell> docker-compose down
# 删除服务数据,要注意是当前的目录中的运行服务的数据目录
shell> rm -rf volumes/
```

##### docker-compose.yml修改内容

```yaml
# volumes下加一行
- /root/milvus.yaml:/milvus/configs/milvus.yaml
```

##### 更换milvus版本

```shell
shell> docker-compose down
# 在下面文件中找到milvus的版本,更改为需要的版本
shell> vi docker-compose.yml
shell> docker image rm -f MILVUS相关的image
# 重新安装milvus
shell> docker-compose up -d
```

##### 测试连接

```shell
# 对应的milvus版本连接 https://milvus.io/docs/release_notes.md
# 注意对应的sdk的版本和milvus的版本一定要对应
# 查看安装的版本
shell> docker container ls -a
venv> pip install pymilvus==2.4.9
```

##### python code

```python
import pymilvus
from pymilvus import MilvusClient

if __name__ == '__main__':
    # 打印连接库版本
    print(pymilvus.__version__)

    client = MilvusClient(
        uri='YOUR_URI', 
        token="YOUR_TOKEN"
    )

    if client.has_collection(collection_name="demo_collection"):
        client.drop_collection(collection_name="demo_collection")

    client.create_collection(
        collection_name="demo_collection",
        dimension=768,  # The vectors we will use in this demo has 768 dimensions
    )
```

##### attu安装[连接]([GitHub - zilliztech/attu: The GUI for Milvus](https://github.com/zilliztech/attu))

```shell
# 注意attu的安装版本和milvus的版本一定要对应
shell> docker run -p 8000:3000 -e MILVUS_URL=127.0.0.1:19530 zilliz/attu:v2.4.9
# 访问http://YOUR_IP:8000
```


