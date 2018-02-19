---
title:  Nginx静态服务安装
date: 2017-09-30 16:29:00
tags: [Nginx, 静态服务]
categories:  LINUX
---

# nginx-static-resource-with-php

> 安装环境需要nginx-1.8.1.tar.gz和nginx-upload-module-2.2.zip

### Nginx安装

```
# 上传或下载nginx-1.8.1.tar.gz
shell> yum install zip unzip -y
shell> tar zxvf nginx-1.8.1.tar.gz
shell> unzip nginx-upload-module-2.2.zip
shell> cd nginx-1.8.1
shell> yum install zlib pcre zlib-devel pcre-devel openssl openssl-devel gcc -y
shell> ./configure --prefix=/usr/local/nginx --pid-path=/usr/local/nginx/nginx.pid --with-http_ssl_module --with-http_ssl_module --add-module=../nginx-upload-module-2.2
shell> make && make install
shell> mkdir /usr/local/www
shell> useradd -s /sbin/nologin -d /usr/local/www/ www
shell> chown -R www.www /usr/local/www/
```
### libmcrypt安装

```
# 上传和下载libmcrypt
shell> tar zxvf libmcrypt-2.5.7.tar.gz
shell> cd libmcrypt-2.5.7
shell> ./configure && make && make install
shell> /sbin/ldconfig
shell> cd libltdl/
shell> ./configure --enable-ltdl-install && make && make install
```
### termcap安装

```
# 上传或下载termcap
shell> tar zxvf termcap-1.3.1.tar.gz
shell> cd termcap-1.3.1
shell> ./configure && make && make install
```
### PHP安装

```
# 上传或下载php 5.6
shell> tar zxvf php-5.6.31.tar.gz
shell> yum -y install gcc gcc-c++ gcc-g77 autoconf automake fiex libxml libxml-devel libxml2 libxml2-devel ncurses-devel libmcrypt libmcrypt-devel libtool-ltdl-devel gettext gettextdevel bison bison-devel bzip2-devel libcurl-devel curl-devel libjpeg-devel libpng-devel freetype-devel gd gd-devel net-snmp net-snmp-devel
shell> cd php-5.6.31
shell> ./configure --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --with-mysql=mysqlnd --with-mysqli=mysqlnd --with-iconv-dir=/usr/local --enable-fpm --disable-phar --with-fpm-user=www --with-fpm-group=www --with-pcre-regex --with-zlib --with-bz2 --enable-calendar --with-curl --enable-dba --with-libxml-dir --with-gd --with-jpeg-dir --with-png-dir --with-zlib-dir --with-freetype-dir --enable-gd-native-ttf --enable-gd-jis-conv --with-mhash --enable-mbstring --with-mcrypt --enable-pcntl --enable-xml --disable-rpath --enable-shmop --enable-sockets --enable-zip --enable-bcmath --with-snmp --disable-ipv6 --enable-soap --with-iconv-dir
shell> make ZEND_EXTRA_LIBS='-lresolv'
shell> make test
shell> make install
shell> cd /usr/local/php/
shell> cp /usr/local/src/php-5.6.5/sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
shell> cp /root/php-5.6.31/sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
shell> chmod 755 /etc/init.d/php-fpm
shell> cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
shell> chkconfig php-fpm on
shell> service php-fpm restart
shell> netstat -npl | grep 9000
```
### 文件上传配置

```
shell> cd /usr/local/www/
shell> mkdir static
shell> vi upload.php # 添加upload.php内容
shell> cd /usr/local/nginx/conf/
shell> mv nginx.conf nginx.conf.bak
shell> vi nginx.conf # 添加nginx.conf内容
shell> service nginx start
shell> netstat -npl | grep 80
```
### upload.php

```
<?php
    $fileName = $_REQUEST["file_name"];
    $filePath = $_REQUEST["file_path"];
    $fileType = $_REQUEST["file_content_type"];
    $fileSize = $_REQUEST["file_size"] / 1024;
    $fileMd5 = $_REQUEST["file_md5"];
    $newPath = substr($filePath, 0, strrpos($filePath, '/') +1);
	echo "new path : ".$newPath;
	rename($filePath, $newPath.$fileName);
?>
```
### nginx.conf

```
user www;
worker_processes 8;
error_log logs/error.log;
error_log logs/error.log notice;
error_log logs/error.log info;
#pid logs/nginx.pid;
events {
	worker_connections 1024;
}
http {
	include mime.types;
	default_type application/octet-stream;
	log_format access '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"';
	access_log logs/access.log access;
	sendfile on;
	#tcp_nopush on;
	#keepalive_timeout 0;
	keepalive_timeout 65;
	#gzip on;

	server {
		client_max_body_size 100m;
		listen 80;
		server_name localhost;
		root /usr/local/www;
		#charset koi8-r;
		#access_log logs/host.access.log main;
		location / {
			root html;
			index index.html index.htm;
		}

		location ~* \.(js|jpg|png|css|jpeg|gif|bmp) {
			expires 30d;
		}

		location /upload {
			# the location of the php handler
			upload_pass /upload.php;
			# the location where the files are uploaded
			upload_store /usr/local/www/static;
			upload_store_access user:rw;
			upload_state_store /usr/local/www/static/state ;
			# Set specified fields in request body
			upload_set_form_field "${upload_field_name}_name" $upload_file_name;
			upload_set_form_field "${upload_field_name}_content_type" $upload_content_type;
			upload_set_form_field "${upload_field_name}_path" $upload_tmp_path;
			# Inform backend about hash and size of a file
			upload_aggregate_form_field "${upload_field_name}_md5" $upload_file_md5;
			upload_aggregate_form_field "${upload_field_name}_size" $upload_file_size;
			upload_pass_form_field "^submit$|^description$";
			upload_cleanup 400 404 499 500-505;
		}

		location ~ .*\.(php|php5)?$ {
			fastcgi_pass 127.0.0.1:9000;
			fastcgi_index index.php;
			include fastcgi.conf;
		}

		error_page 404 /404.html;
		# redirect server error pages to the static page /50x.html
		#
		error_page 500 502 503 504 /50x.html;
		location = /50x.html {
			root html;
		}
	}
}
```
### Test.html

```
<!DOCTYPE html>
<html>
<head>
<title>Test upload</title>
</head>
<body>
	<h2>Select files to upload</h2>
	<form name="upload" method="POST" enctype="multipart/form-data" action="http://IP/upload">
		<input type="file" name="file"><br>
		<input type="submit" name="submit" value="Upload">
		<input type="hidden" name="test" value="value">
	</form>
</body>
</html>
```
### Test.java
```
public class HttpUploadFile {

	private static final String HTTP_UPLOAD_URL = "http://IP/upload";

	

	public static void main(String[] args) throws IOException {

		File file = new File("c:/hahaha.jpg");

		post(file, HTTP_UPLOAD_URL);

	}



	public static void post(File file, String url) throws IOException {

		FileBody body = null;

		HttpClient httpClient = new DefaultHttpClient();

		HttpPost httpPost = new HttpPost(url);

		if (file != null) {

			body = new FileBody(file);

		}

		MultipartEntityBuilder builder = MultipartEntityBuilder.create();

		builder.setMode(HttpMultipartMode.BROWSER_COMPATIBLE);

		builder.addPart("file", body);

		HttpEntity entity = builder.build();

		httpPost.setEntity(entity);

		HttpResponse response = httpClient.execute(httpPost);

	}

}
```