---
title:  Python安装
date: 2022-03-06 15:56:00
tags: [PYTHON, INSTALL]
categories:  PYTHON
---

### Python安装

```
shell> yum -y groupinstall "Development tools"
shell> yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
shell> yum install -y libffi-devel zlib1g-dev
shell> yum install zlib* -y
shell> tar -xvf Python-3.7.12.tar.xz
shell> cd Python-3.7.12
shell> ./configure --prefix=/usr/local/python37 --with-ssl --enable-optimizations
shell> make
shell> make install
```

