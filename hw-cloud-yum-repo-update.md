---
title: 华为云CentOS更改yum源
date: 2022-03-05 15:42:00
tags: [CENTOS, YUM]
categories: 云
---

### 华为云CentOS更改yum源

```
shell> yum upgrade -y
shell> yum repolist
shell> mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
shell> cd /etc/yum.repos.d/
shell> ls
shell> mv CentOS-Linux-BaseOS.repo CentOS-Linux-BaseOS.repo.bak
shell> wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo
shell> mv CentOS-Base.repo CentOS-Linux-BaseOS.repo
shell> yum clean all && yum makecache
shell> cat CentOS-Linux-AppStream.repo
shell> mv CentOS-Linux-AppStream.repo CentOS-Linux-AppStream.repo.bak
shell> yum clean all && yum makecache
shell> yum upgrade -y
```



