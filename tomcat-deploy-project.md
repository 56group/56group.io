---
title:  Linux系统Tomcat项目部署
date: 2018-02-26 10:22:00
tags: [TOMCAT, 部署]
categories:  Tomcat
---

# Tomcat项目简单部署

### Tomcat安装

```
# 解压安装 jdk, tomcat, 配置环境变量
shell> tar zxvf jdk-VERSION-linux-x64.tar.gz -C /usr/local/
shell> ln -s /usr/local/jdkVERSION /usr/local/java
shell> tar zxvf apache-tomcat-VERSION.tar.gz -C /usr/local
shell> ln -s apache-tomcat-VERSION /usr/local/tomcatN # N是7, 8, 9
#配置环境变量
shell> vi /etc/profile.d/env.sh # 添加变量配置
shell> source /etc/profile.d/env.sh
```

### 环境变量配置

```
export JAVA_HOME=/usr/local/java
export CATALINA_HOME=/usr/local/tomcat
export PATH=$JAVA_HOME/bin:$CATALINA_HOME/bin:$PATH
```

### 项目简单部署

```
# 把项目war包上传到/usr/local/tomcatN/webapps目录,重启tomcat
shell> cd /usr/local/tomcatN
shell> bin/shutdown.sh
shell> bin/startup.sh
```

# Tomcat项目程序和环境分离部署

> Tomcat的服务安装目录和项目程序的发布目录不在同一个目录下,原来的简单发布中项目程序的发布目录为Tomcat安装目录的webapps目录下,这样存在的问题为Tomcat升级麻烦,把环境目录的发布目录分离,Tomcat的升级不受程序发布的war包影响

### Tomcat安装

```
# 安装JDK和Tomcat但是不配置Tomcat的环境变量,只配置JDK的环境变量
shell> tar zxvf jdk-VERSION-linux-x64.tar.gz -C /usr/local/
shell> ln -s /usr/local/jdkVERSION /usr/local/java
shell> tar zxvf apache-tomcat-VERSION.tar.gz -C /usr/local
shell> ln -s apache-tomcat-VERSION /usr/local/tomcatN # N是7, 8, 9
#配置环境变量
shell> vi /etc/profile.d/env.sh # 添加变量配置
shell> source /etc/profile.d/env.sh
```

### 环境变量配置

```
export JAVA_HOME=/usr/local/java
export PATH=$JAVA_HOME/bin:$PATH
```

### 配置项目发布服务目录

```
# 在/usr/local下建立一个warservices目录,这个目录里面以文件夹方式区分每个服务,即一个服务一个文件夹
shell> mkdir -p /usr/local/warservices/YOUR_SER # 下面假设YOUR_SER为testser
shell> cd /usr/local/warservices/testser
shell> cd /usr/local/tomcatN
shell> cp -r conf logs temp webapps work /usr/local/warservices/testser
shell> cd /usr/local/warservices/testser
shell> vi tomcat.sh # 添加如下tomcat.sh内容
# 上传testser.war到testser目录下的webapps目录下
shell> chmod u+x tomcat.sh
shell> ./tomcat.sh restart
```

### tomcat.sh

```
#!/bin/bash 
export JAVA_HOME=/usr/local/java
export CATALINA_HOME=/usr/local/tomcat8
export CATALINA_BASE="`pwd`"

case $1 in
	start)
	$CATALINA_HOME/bin/catalina.sh start
		echo start success !
	;;
	stop)
		$CATALINA_HOME/bin/catalina.sh stop
		echo stop success !
	;;
	restart)
		$CATALINA_HOME/bin/catalina.sh stop
			echo stop success !
			sleep 2 
		$CATALINA_HOME/bin/catalina.sh start
		echo start success !
	;;
	esac
exit 0
```

# Tomcat产品级发布,支持版本记录和回滚

> Tomcat发布项目上面的方式可以让程序和环境分离,但是还是不能达到要求,产品发布需要记录不同的版本和同一个版本可能发布多次,为了满足产品级发布,除了是环境和程序分离外还需要添加额外的一些操作

### Tomcat安装

```
# 安装JDK和Tomcat但是不配置Tomcat的环境变量,只配置JDK的环境变量
shell> tar zxvf jdk-VERSION-linux-x64.tar.gz -C /usr/local/
shell> ln -s /usr/local/jdkVERSION /usr/local/java
shell> tar zxvf apache-tomcat-VERSION.tar.gz -C /usr/local
shell> ln -s apache-tomcat-VERSION /usr/local/tomcatN # N是7, 8, 9
#配置环境变量
shell> vi /etc/profile.d/env.sh # 添加变量配置
shell> source /etc/profile.d/env.sh
```

### 环境变量配置

```
export JAVA_HOME=/usr/local/java
export PATH=$JAVA_HOME/bin:$PATH
```

### 配置项目发布服务目录

```
# 在/usr/local下建立一个warservices目录,这个目录里面以文件夹方式区分每个服务,即一个服务一个文件夹
shell> mkdir -p /usr/local/warservices/YOUR_SER # 下面假设YOUR_SER为testser
shell> cd /usr/local/warservices/testser
shell> cd /usr/local/tomcatN
shell> cp -r conf logs temp webapps work /usr/local/warservices/testser
shell> cd /usr/local/warservices/testser
shell> vi tomcat.sh # 添加如上tomcat.sh内容
shell> chmod u+x tomcat.sh
shell> vi deploy.sh # 添加如下deploy.sh内容
shell> chmod u+x deploy.sh
# 需要注意的是war的获取方式,在这次测试中war上上传的,通常war是通过网络获取的,例如wget活curl
shell> ./deploy.sh
```

### deploy.sh

```
#!/bin/bash

# 下面这个地址是描述了tomcat的context可以如何配置,是这个脚本的理论基础

# https://tomcat.apache.org/tomcat-8.5-doc/config/context.html

app_name=$1
app_version=$2
deploy_time=`date "+%Y_%m_%d_%H_%M_%S"`

war_packages_dir=`pwd`/warPackages
#/usr/bin/mkdir -p $war_packages_dir
war=$war_packages_dir/${app_name}_${app_version}.war
echo $war

deploy_app() {
    target_dir=$war_packages_dir/$app_name-$app_version-$deploy_time
    
    if [ ! -f $war ]; then
        echo "App war file not exist !"
        exit 1
    fi
    
    /usr/bin/unzip -q $war -d $target_dir
    /usr/bin/cp -r `pwd`/appConf/* $target_dir/WEB-INF/classes/
    /usr/bin/rm -rf `pwd`/appwar
    /usr/bin/ln -sf $target_dir appwar
    if [ -f deploy.sh ]; then
        `pwd`/tomcat.sh stop   
    fi
    
    target_ln=`pwd`/appwar
    /usr/bin/echo '<?xml version="1.0" encoding="UTF-8" ?> <Context docBase="'$target_ln'" allowLinking="true"></Context>' > `pwd`/conf/Catalina/localhost/ROOT.xml
    #/usr/bin/echo -ne "#!/bin/bash -e\npom_a=${pom_a}\npom_v=${pom_v}" > current_deploy.sh
    #/usr/bin/echo -ne "${target_d}" > current_deploy_dir
    /usr/bin/chmod +x deploy.sh
    `pwd`/tomcat.sh start
}

deploy_app
```