---
title: 飞桨安装
date: 2022-03-06 16:04:00
tags: [PADDLE, INSTALL]
categories: PADDLE
---

### 飞桨安装

```
shell> wget https://repo.anaconda.com/archive/Anaconda3-2021.11-Linux-x86_64.sh
shell> chmod u+x Anaconda3-2021.11-Linux-x86_64.sh
shell> ./Anaconda3-2021.11-Linux-x86_64.sh # 安装到/usr/local下
shell> vi /etc/profile.d/env.sh
shell> chmod u+x /etc/profile.d/env.sh
shell> source /etc/profile.d/env.sh
shell> conda create -n paddle_env python=3.7
shell> source activate
shell> conda activate paddle_env
shell> python -c "import platform;print(platform.architecture()[0]);print(platform.machine())"
shell> conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
shell> conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
shell> conda config --set show_channel_urls yes
shell> conda install paddlepaddle==2.2.2 --channel https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/Paddle/
shell> python
python> import paddle
python> paddle.utils.run_check()
```

