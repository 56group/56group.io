---
title: nginx配置demo
date: 2018-08-15 08:36:00
tags: [NGINX, 配置]
categories: Nginx
---

### nginx常用配置

```
user www www; 
worker_processes 2; 
error_log /var/log/nginx/nginx_error.log crit; 
pid /usr/local/nginx/nginx.pid; 
worker_rlimit_nofile 51200; 
events 
{ 
	use epoll; 
	worker_connections 51200; 
} 
http 
{ 
	add_header Access-Control-Allow-Origin *;
    add_header 'Access-Control-Allow-Credentials' 'true';
    add_header 'Access-Control-Allow-Headers' 'Content-Type,Accept';
	include mime.types; 
	default_type application/octet-stream; 
	server_names_hash_bucket_size 128; 
	client_header_buffer_size 32k; 
	large_client_header_buffers 4 32k; 
	sendfile on; 
	tcp_nopush on; 
	keepalive_timeout 60; 
	tcp_nodelay on; 
	fastcgi_connect_timeout 300; 
	fastcgi_send_timeout 300; 
	fastcgi_read_timeout 300; 
	fastcgi_buffer_size 64k; 
	fastcgi_buffers 4 64k; 
	fastcgi_busy_buffers_size 128k; 
	fastcgi_temp_file_write_size 128k; 
	gzip on; 
	gzip_min_length 1k; 
	gzip_buffers 4 16k; 
	gzip_http_version 1.0; 
	gzip_comp_level 2; 
	gzip_types text/plain application/x-javascript text/css application/xml; 
	gzip_vary on; 
	log_format access '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for"';
	access_log  /var/log/nginx/access.log  access;
        client_max_body_size 300M;
    server  
    {  
        listen 100;  
        server_name  localhost;  
        index index.php index_first.html index.html index.htm;  
        server_name_in_redirect off;  
        root /usr/local/www/zentaopms/www/;

	location ~ .*\.(php|php5)?$
	{
		fastcgi_pass 127.0.0.1:9000;
		fastcgi_index index.php;
		include fastcgi.conf;
	}

	location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|ico)$
        {
            try_files $uri =404;
            expires      30d;
        }

        location ~ .*\.(js|css)?$
        {
            try_files $uri =404;
            expires      1h;
        }
    }
    server
    { 
        listen 80;
        server_name  lms.56team.com;
        index index.php index_first.html index.html index.htm;
        server_name_in_redirect off;
        root /usr/local/www/wplms/;
	
        location = /favicon.ico {
                log_not_found off;
                access_log on;
        }

        location = /robots.txt {
                allow all;
                log_not_found off;
                access_log off;
        }

	location / {
           try_files $uri $uri/ /index.php?$args; 
	}

        location ~ \.(php|php5)$
        {
                fastcgi_pass 127.0.0.1:9000;
                fastcgi_index index.php;
                include fastcgi.conf;
        }

        location ~* \.(js|css|gif|jpg|jpeg|png|bmp|swf|ico)$
        {
            try_files $uri =404;
            expires      30d;
            log_not_found on;
        }
    }
    server
    {
        listen 80;
        server_name www.56team.com;
        root /usr/local/www/html/;
        
        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|ico)$
        {
            try_files $uri =404;
            expires      30d;
        }

        location / {
            index index.html;
        }
    }
    server
    {
        listen 80;
        server_name project.56team.com;

        location / {
            proxy_pass http://localhost:100;
        }
    }
    server
    {
        listen 80;
        server_name redmine.56team.com;

        location / {
            proxy_pass http://127.0.0.1:3000;
        }
    }
    server
    {
        listen 80;
        server_name gitlab.56team.com;

        location / {
            proxy_pass http://localhost:8086;
        }
    }
    server  
    {  
        listen 80;
        server_name  blog.56team.com;
        index index.php index_first.html index.html index.htm;  
        server_name_in_redirect off;  
        root /usr/local/www/anchor/;

	location / {
             try_files $uri $uri/ /index.php;
        }

	location ~ .*\.(php|php5)?$
	{
		fastcgi_pass 127.0.0.1:9000;
		fastcgi_index index.php;
		include fastcgi.conf;
	}

	location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|ico)$
        {
            try_files $uri =404;
            expires      30d;
        }

        location ~ .*\.(js|css)?$
        {
            try_files $uri =404;
            expires      1h;
        }
    }
    server {
        listen 80;
        server_name nanqu.56team.com;
        rewrite ^(.*) https://$server_name$1 permanent;
    }
    server
    {
        listen 443;
        server_name nanqu.56team.com;
	ssl on;
	ssl_certificate 1_nanqu.56team.com_bundle.crt;
        ssl_certificate_key 2_nanqu.56team.com.key;
	ssl_session_timeout 5m;
        #ssl_protocols TLSv1;
	ssl_protocols TLSv1.2;
	ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers   on;

        location / {
            proxy_pass http://localhost:8080;
            rewrite ^/$ /web last;
        }
    }
    server
    {
        listen 80;
        server_name jenkins.56team.com;

        location / {
            proxy_pass http://localhost:8080;
            rewrite ^/$ /jenkins last;
        }
    }
    server
    {
        listen 80;
        server_name  piwik.56team.com;
        index index.php index_first.html index.html index.htm;
        server_name_in_redirect off;
        root /usr/local/www/piwik/;

        location ~ .*\.(php|php5)?$
        {
                fastcgi_pass 127.0.0.1:9000;
                fastcgi_index index.php;
                include fastcgi.conf;
        }

        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|ico)$
        {
            try_files $uri =404;
            expires      30d;
        }

        location ~ .*\.(js|css)?$
        {
            try_files $uri =404;
            expires      1h;
        }
    }
    server
    {
        listen 80;
        server_name lgbmanage.56team.com;

        location / {
           proxy_pass http://localhost:8080;
           rewrite ^/$ /manage last;
        }
    }
    server
    {
        listen 80;
        server_name signup.56team.com;

        location / {
           proxy_pass http://localhost:8080;
           rewrite ^/$ /signUp last;
        }
    }
    server
    {
        listen 80;
        server_name edu.56team.com;
	root /usr/local/www/lgbedu;
         
	location / {
            index index.html;
        }
    }
    #server {
    #    listen 80;
    #    server_name www.zlin.xin;
    #    rewrite ^(.*) https://$server_name$1 permanent;
    #}
    #server
    #{
    #    listen 443;
    #    server_name www.zlin.xin;
    #    set $mobile_rewrite do_not_perform;
#
    #    if ($http_user_agent ~* "(android|bb\d+|meego).+mobile|avantgo|bada\/|blackberry|blazer|compal|elaine|fennec|hiptop|iemobile|ip(hone|od)|iris|kindle|lge |maemo|midp|mmp|mobile.+firefox|netfront|opera m(ob|in)i|palm( os)?|phone|p(ixi|re)\/|plucker|pocket|psp|series(4|6)0|symbian|treo|up\.(browser|link)|vodafone|wap|windows ce|xda|xiino") {
     #     set $mobile_rewrite perform;
      #  }

       # if ($http_user_agent ~* "^(1207|6310|6590|3gso|4thp|50[1-6]i|770s|802s|a wa|abac|ac(er|oo|s\-)|ai(ko|rn)|al(av|ca|co)|amoi|an(ex|ny|yw)|aptu|ar(ch|go)|as(te|us)|attw|au(di|\-m|r |s )|avan|be(ck|ll|nq)|bi(lb|rd)|bl(ac|az)|br(e|v)w|bumb|bw\-(n|u)|c55\/|capi|ccwa|cdm\-|cell|chtm|cldc|cmd\-|co(mp|nd)|craw|da(it|ll|ng)|dbte|dc\-s|devi|dica|dmob|do(c|p)o|ds(12|\-d)|el(49|ai)|em(l2|ul)|er(ic|k0)|esl8|ez([4-7]0|os|wa|ze)|fetc|fly(\-|_)|g1 u|g560|gene|gf\-5|g\-mo|go(\.w|od)|gr(ad|un)|haie|hcit|hd\-(m|p|t)|hei\-|hi(pt|ta)|hp( i|ip)|hs\-c|ht(c(\-| |_|a|g|p|s|t)|tp)|hu(aw|tc)|i\-(20|go|ma)|i230|iac( |\-|\/)|ibro|idea|ig01|ikom|im1k|inno|ipaq|iris|ja(t|v)a|jbro|jemu|jigs|kddi|keji|kgt( |\/)|klon|kpt |kwc\-|kyo(c|k)|le(no|xi)|lg( g|\/(k|l|u)|50|54|\-[a-w])|libw|lynx|m1\-w|m3ga|m50\/|ma(te|ui|xo)|mc(01|21|ca)|m\-cr|me(rc|ri)|mi(o8|oa|ts)|mmef|mo(01|02|bi|de|do|t(\-| |o|v)|zz)|mt(50|p1|v )|mwbp|mywa|n10[0-2]|n20[2-3]|n30(0|2)|n50(0|2|5)|n7(0(0|1)|10)|ne((c|m)\-|on|tf|wf|wg|wt)|nok(6|i)|nzph|o2im|op(ti|wv)|oran|owg1|p800|pan(a|d|t)|pdxg|pg(13|\-([1-8]|c))|phil|pire|pl(ay|uc)|pn\-2|po(ck|rt|se)|prox|psio|pt\-g|qa\-a|qc(07|12|21|32|60|\-[2-7]|i\-)|qtek|r380|r600|raks|rim9|ro(ve|zo)|s55\/|sa(ge|ma|mm|ms|ny|va)|sc(01|h\-|oo|p\-)|sdk\/|se(c(\-|0|1)|47|mc|nd|ri)|sgh\-|shar|sie(\-|m)|sk\-0|sl(45|id)|sm(al|ar|b3|it|t5)|so(ft|ny)|sp(01|h\-|v\-|v )|sy(01|mb)|t2(18|50)|t6(00|10|18)|ta(gt|lk)|tcl\-|tdg\-|tel(i|m)|tim\-|t\-mo|to(pl|sh)|ts(70|m\-|m3|m5)|tx\-9|up(\.b|g1|si)|utst|v400|v750|veri|vi(rg|te)|vk(40|5[0-3]|\-v)|vm40|voda|vulc|vx(52|53|60|61|70|80|81|83|85|98)|w3c(\-| )|webc|whit|wi(g |nc|nw)|wmlb|wonu|x700|yas\-|your|zeto|zte\-)") {
        #  set $mobile_rewrite perform;
        #}

        #if ($mobile_rewrite = perform) {
          #ewrite ^ http://detectmobilebrowser.com/mobile redirect;
          #break;
        #}

	#root /usr/local/www/zhulin;
        #ssl on;
        #ssl_certificate 1_www.zlin.xin_bundle.crt;
        #ssl_certificate_key 2_www.zlin.xin.key;
        #ssl_session_timeout 5m;
        #ssl_protocols TLSv1;
        #ssl_protocols TLSv1.2;
        #ssl_ciphers  HIGH:!aNULL:!MD5;
        #ssl_prefer_server_ciphers   on;
        
        #location / {
	 #   root /usr/local/www/zhulin;
          #  if ($mobile_rewrite = perform) {
           #     root /usr/local/www/zhulinshouji;
           # }

            #index index.html;
        #}
    #}
    server
    {
        listen 80;
        server_name syz.56team.com;

        location / {
           proxy_pass http://localhost:8080;
           rewrite ^/$ /syz last;
        }
    }
    server
    {
        listen 80;
        server_name hexo.56team.com;

        location / {
            proxy_pass http://localhost:4000;
        }
    }
    server
    {
        listen 80;
        server_name dls.56team.com;

        location / {
            proxy_pass http://localhost:8080;
           rewrite ^/$ /worknote last;
        }
    }
    server
    {
        listen 80;
        server_name dlkd.56team.com;

        location / {
            proxy_pass http://localhost:8080;
           rewrite ^/$ /dlkd last;
        }
    }

    server 
    {
    	listen 80;
        server_name  owncloud.56team.com;
	location ~ \.php
    {    
	 root /usr/local/www/owncloud;
         fastcgi_pass   127.0.0.1:9000;
         fastcgi_index  index.php;
         fastcgi_split_path_info ^(.+\.php)(.*)$;
         fastcgi_param PATH_INFO $fastcgi_path_info; 
         fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
         include        fastcgi.conf;
    }
		 
        location ~ .*\.(php|php5)?$
        {
                fastcgi_pass 127.0.0.1:9000;
                fastcgi_index index.php;
                include fastcgi.conf;
        }
	
   }
   server
   {
      listen 80;
      server_name jindu.56team.com;

      location / {
          proxy_pass http://124.93.200.210:8008;
      } 
   }
   server
   {
       listen 80;
       server_name jinzhou.56team.com;

       location / {
          proxy_pass http://localhost:8080;
          rewrite ^/$ /jinzhou last;
       }
   }
   server
   {
       listen 80;
       server_name jxzl.zhulin.xin;

       location / {
          proxy_pass http://localhost:9080;
          rewrite ^/$ /aos last;
       }
   }
   server
    {
        listen 80;
        server_name yapi.56team.com;

        location / {
            proxy_pass http://localhost:3000;
        }
    }
}

```
