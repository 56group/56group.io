---
title: SVN服务安装
date: 2017-07-21 14:39:00
tags: [SVN, 安装]
categories: Linux
---

### [SVN INSTALL](http://subversion.apache.org)

```
wget http://apache.fayea.com/subversion/subversion-1.9.5.tar.gz
tar zxvf subversion-1.9.5.tar.gz
yum install apr apr-devel apr-util apr-util-devel -y
cd subversion-1.9.5
wget http://www.sqlite.org/sqlite-amalgamation-3071501.zip
unzip sqlite-amalgamation-3071501.zip
mv sqlite-amalgamation-3071501 sqlite-amalgamation
./configure --prefix=/usr/local/subversion
make && make install

# create repo
svnadmin create YOUR_REPO_NAME

# config repo
vi conf/auth
vi conf/passwd
vi conf/svnserve.conf

# start svn
/usr/local/subversion/bin/svnserve -d -r YOUR_REPO_FULL_PATH
```
