```
title: Windows WSL Ubuntu ChatGLM
date: 2024-06-01 11:16:00
tags: [ChatGLM, INSTALL]
categories: LLM
```

### Windows WSL Ubuntu ChatGLM

### Install Python

```shell
shell> wget https://www.python.org/ftp/python/3.11.9/Python-3.11.9.tar.xz
shell> tar -xvf Python-3.11.9.tar.xz
shell> sudo apt install build-essential  zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev
shell> cd Python-3.11.9/
shell> ./configure --prefix=/home/hellolnd/python3119 --enable-optimizations
shell> make
shell> make install
shell> cd ..
shell> rm -rf Python-3.11.9
shell> rm -rf Python-3.11.9.tar.xz
shell> /home/hellolnd/python3119/bin/pip3 install virtualenv
```

### Clone ChatGLM code and model

```shell
shell> curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh
shell> sudo apt install git-lfs
shell> git lfs install
# code
shell> git clone https://github.com/THUDM/ChatGLM3.git
# model
shell> git clone https://www.modelscope.cn/ZhipuAI/chatglm3-6b.git
shell>  cd ChatGLM3/
shell> /home/hellolnd/python3119/bin/virtualenv -p /home/hellolnd/python3119/bin/python3 venv
shell> source venv/bin/activate
# install will be long time
shell> pip3 install -r requirements.txt
# test
shell> python basic_demo/cli_demo.py
```

### Q&A

##### _bz2

```shell
shell> sudo find /usr -name '*_bz2*'
shell> cp /usr/lib/python3.10/lib-dynload/_bz2.cpython-310-x86_64-linux-gnu.so /home/hellolnd/python3119/lib/python3.11/lib-dynload/
shell> cp /usr/lib/python3.10/lib-dynload/_bz2.cpython-310-x86_64-linux-gnu.so /home/hellolnd/llm/ChatGLM3/venv/lib/python3.11/site-packages/
shell> cd venv/lib/python3.11/site-packages/
shell> mv _bz2.cpython-310-x86_64-linux-gnu.so _bz2.cpython-311-x86_64-linux-gnu.so
```
