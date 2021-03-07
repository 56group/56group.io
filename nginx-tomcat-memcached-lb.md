### NGINX TOMCAT MEMCACHED 负载均衡

##### 下载

```shell
shell> yum install wget -y
shell> cd /usr/local/src
shell> wget http://www.memcached.org/files/memcached-1.4.29.tar.gz
shell> wget https://mirrors.bfsu.edu.cn/apache/tomcat/tomcat-8/v8.5.63/bin/apache-tomcat-8.5.63.tar.gz
shell> wget https://nginx.org/download/nginx-1.11.3.tar.gz
```

##### TOMCAT INSTALL

```shell
shell> tar -xvf apache-tomcat-7.0.108.tar.gz -C /usr/local/
shell> cd /usr/local
shell> mv apache-tomcat-7.0.108/ tomcat_8080
shell> cd /usr/local/src
shell> tar -xvf apache-tomcat-7.0.108.tar.gz -C /usr/local/
shell> mv apache-tomcat-7.0.108/ tomcat_8081
```

##### /etc/profile.d/tomcat.sh配置

```shell
shell> vi /etc/profile.d/tomcat.sh
shell> chmod u+x /etc/profile.d/tomcat.sh
shell> source /etc/profile.d/tomcat.sh
```

##### /etc/profile.d/tomcat.sh

```
CATALINA1_BASE=/usr/local/tomcat_8080
CATALINA2_BASE=/usr/local/tomcat_8081
CATALINA1_HOME=/usr/local/tomcat_8080
CATALINA2_HOME=/usr/local/tomcat_8081
TOMCAT1_HOME=/usr/local/tomcat_8080
TOMCAT2_HOME=/usr/local/tomcat_8081
export CATALINA1_BASE CATALINA1_HOME TOMCAT1_HOME
export CATALINA2_BASE CATALINA2_HOME TOMCAT2_HOME
```

##### tomcat_8081 Connector端口更改

```shell
shell> cd /usr/local
shell> cd tomcat_8081
shell> vi conf/server.xml
```

##### server.xml

```
<Connector port="8081" protocol="HTTP/1.1"
               connectionTimeout="20000"
               URIEncoding="UTF-8"
               redirectPort="8443" />
```

##### tomcat_8081 AJP端口更改

```
<Connector protocol="AJP/1.3"
               address="::1"
               port="8010"
               redirectPort="8443" />
```

##### tomcat_8081 server端口更改

```
<Server port="8006" shutdown="SHUTDOWN">
```

##### tomcat_8081 catalina.sh

```
# 在# OS specific support.  $var _must_ be set to either true or false下面加
export CATALINA_BASE=$CATALINA2_BASE
export CATALINA_HOME=$CATALINA2_HOME
```

##### ENGINE

```
<Engine name="Catalina" defaultHost="localhost" jvmRoute="jvm2">
```

##### tomcat_8080 catalina.sh

```
# 在# OS specific support.  $var _must_ be set to either true or false下面加
export CATALINA_BASE=$CATALINA1_BASE
export CATALINA_HOME=$CATALINA1_HOME
```

##### ENGINE

```
<Engine name="Catalina" defaultHost="localhost" jvmRoute="jvm1">
```

##### SESSION JAR

```
shell> cd /usr/local/src
shell> mkdir jar
shell> wget https://repo1.maven.org/maven2/de/javakaffee/msm/memcached-session-manager/1.9.7/memcached-session-manager-1.9.7.jar
shell> wget https://repo1.maven.org/maven2/de/javakaffee/msm/memcached-session-manager-tc8/1.9.7/memcached-session-manager-tc8-1.9.7.jar
shell> wget https://repo1.maven.org/maven2/net/spy/spymemcached/2.11.1/spymemcached-2.11.1.jar
shell> wget https://repo1.maven.org/maven2/de/javakaffee/msm/msm-javolution-serializer/1.9.7/msm-javolution-serializer-1.9.7.jar
shell> wget https://repo1.maven.org/maven2/de/javakaffee/msm/msm-xstream-serializer/1.9.7/msm-xstream-serializer-1.9.7.jar
shell> wget https://repo1.maven.org/maven2/com/thoughtworks/xstream/xstream/1.4.9/xstream-1.4.9.jar
shell> wget https://repo.maven.apache.org/maven2/javolution/javolution/5.4.3/javolution-5.4.3.jar
shell> cp *.jar /usr/local/tomcat_8080/lib/
shell> cp *.jar /usr/local/tomcat_8081/lib/
```

##### tomcat_8080 context.xml更改

```
<Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
             memcachedNodes="n1:localhost:11211"
             failoverNodes="n1"
             requestUriIgnorePattern=".*\.(ico|png|gif|jpg|css|js)$"          
	transcoderFactoryClass="de.javakaffee.web.msm.serializer.javolution.JavolutionTranscoderFactory"/>
```

##### tomcat_8080 logging.properties更改

```
de.javakaffee.web.msm.level = FINE
net.spy.memcached.level = WARNING
```

##### tomcat_8080 catalina.sh更改

```
CATALINA_OPTS="-Dnet.spy.log.LoggerImpl=net.spy.memcached.compat.log.SunLogger"
```

##### tomcat_8081 context.xml更改

```
<Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
             memcachedNodes="n1:localhost:11211"
             requestUriIgnorePattern=".*\.(ico|png|gif|jpg|css|js)$"          
	transcoderFactoryClass="de.javakaffee.web.msm.serializer.javolution.JavolutionTranscoderFactory"/>
```

##### tomcat_8081 logging.properties更改

```
de.javakaffee.web.msm.level = FINE
net.spy.memcached.level = WARNING
```

##### tomcat_8081 catalina.sh更改

```
CATALINA_OPTS="-Dnet.spy.log.LoggerImpl=net.spy.memcached.compat.log.SunLogger"
```

##### MEMCACHED INSTALL

```
shell> yum install -y gcc pcre-devel zlib-devel libevent-devel
shell> cd /usr/local/src
shell> tar -xvf memcached-1.4.29.tar.gz
shell> cd memcached-1.4.29
shell> ./configure --prefix=/usr/local/memcached
shell> make
shell> make install
```

##### NGINX INSTALL

```shell
shell> cd /usr/local/src
shell> tar -xvf nginx-1.11.3.tar.gz
shell> cd nginx-1.11.3
shell> ./configure --prefix=/usr/local/nginx
shell> make
shell> make install
```

##### nginx.conf配置

```shell
shell> cd /usr/local/nginx
shell> cd conf
shell> cp nginx.conf nginx.conf.bak
shell> echo "" > nginx.conf
```

##### nginx.conf

```
worker_processes  1;

error_log  logs/error.log;
error_log  logs/error.log  notice;
error_log  logs/error.log  info;

pid        logs/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;
    sendfile        on;

    keepalive_timeout  65;

    upstream localhost {
        server 127.0.0.1:8080;
        server 127.0.0.1:8081;
    }

    server {
        listen       80;
        server_name  localhost;

        charset utf-8;

        location / {
            proxy_pass http://localhost;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

##### webapps/ROOT/index.jsp

```
<html>
  <h1>tomcat8080/8081</h1>
  SessionID:<%=session.getId()%></br>
  SessionIP:<%=request.getServerName()%></br>
</html>
```

##### START

```shell
shell> /usr/local/memcached/bin/memcached -d -m 1024 -u root -c 1024 -p 11211 /tmp/memcached.pid
shell> /usr/local/tomcat_8080/bin/startup.sh
shell> /usr/local/tomcat_8081/bin/startup.sh
shell> /usr/local/nginx/sbin/nginx
```

