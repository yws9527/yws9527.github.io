Nginx 配置

#### 1. 整体结构

```nginx
.
├── agent											# 代理商web服务器
│   ├── html										# 代理商web服务目录
│   ├── logs										# 日志目录
│   └── nginx										# nginx配置目录
│       ├── conf									# nginx子server目录
│       ├── nginx.conf								# nginx配置文件
├── nginx-ori										# nginx原始目录
│   ├── conf.d										# nginx原始子server目录
│   │   └── default.conf
│   ├── fastcgi_params
│   ├── mime.types
│   ├── modules -> /usr/lib/nginx/modules
│   ├── nginx.conf
│   ├── scgi_params
│   └── uwsgi_params
├── nginx-proxy										# 总入口web服务器
│   ├── conf										# 总入口中的各个server配置目录
│   │   ├── agent.conf								# 代理商子server
│   │   ├── default.conf							# 默认server
│   │   └── school.conf								# 学校子server
│   ├── logs										# 日志目录
├── school											# 学校web服务器
│   ├── html										# 学校web服务目录
│   ├── logs										# 日志目录
│   └── nginx										# nginx配置目录
│       ├── conf									# nginx子server目录
│       ├── nginx.conf								# nginx配置文件
└── ssl												# SSL证书目录
    └── agent										# 代理商服务器SSL证书目录
        ├── 8460356_agent.ptweb.shjkkc.com.key
        └── 8460356_agent.ptweb.shjkkc.com.pem

```



#### 2. Nginx 配置多个二级域名

> 1. 代理入口
>
>    <u>在nginx的conf.d目录下创建如下文件</u>
>
>    ```nginx
>    # agent.config
>    
>    server {
>     	listen       80;         #监听的端口
>     	server_name  agent.ptweb.shjkkc.com;    #监听的URL
>      
>     	location / {
>        	proxy_redirect off;
>         	proxy_set_header Host $host;
>         	proxy_set_header X-Real-IP $remote_addr;
>         	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
>         	proxy_pass http://119.23.209.19:8102; # 通过对应的域名转发访问对应的端口
>     	}
>    }
>    
>    # school.config
>    
>    server {
>     	listen       80;         #监听的端口
>     	server_name	school.ptweb.shjkkc.com;    #监听的URL
>     	location / {
>        	proxy_redirect off;
>         	proxy_set_header Host $host;
>         	proxy_set_header X-Real-IP $remote_addr;
>         	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
>         	proxy_pass http://119.23.209.19:8101; # 通过对应的域名转发访问对应的端口
>     	}
>    }
>    
>    ```
>
> 2. 通过 nginx中（nginx.conf） 的默认配置加载子server
>
>    ``` nginx
>    
>    user  nginx;
>    worker_processes  auto;
>    
>    error_log  /var/log/nginx/error.log notice;
>    pid        /var/run/nginx.pid;
>    
>    
>    events {
>        worker_connections  1024;
>    }
>    
>    
>    http {
>        include       /etc/nginx/mime.types;
>        default_type  application/octet-stream;
>    
>        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
>                          '$status $body_bytes_sent "$http_referer" '
>                          '"$http_user_agent" "$http_x_forwarded_for"';
>    
>        access_log  /var/log/nginx/access.log  main;
>    
>        sendfile        on;
>        #tcp_nopush     on;
>    
>        keepalive_timeout  65;
>    
>        #gzip  on;
>    	
>        # !!!!加载多个子server的关键（school、agent）
>        include /etc/nginx/conf.d/*.conf;
>    }
>    
>    ```
>
>    



#### 3. Nginx 配置 SSL 证书

```nginx
server {
    listen 80;
    server_name agent.ptweb.shjkkc.com;
    rewrite ^(.*) https://$server_name$1 permanent; # 监听80将http重定向到Https
}

server {
    listen       	443 ssl;
    server_name  	agent.ptweb.shjkkc.com;
	
    # 监听443端口，并配置SSL证书xxx.pem，xxx.key
    ssl_certificate  /home/ssl/8460356_agent.ptweb.shjkkc.com.pem;
    ssl_certificate_key /home/ssl/8460356_agent.ptweb.shjkkc.com.key;
    ssl_session_timeout 5m;
    ssl_session_cache shared:SSL:1m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    #charset koi8-r;

    #access_log  logs/host.access.log  main;

    location / {
        root		  /usr/share/nginx/html;
        index     index.html index.htm;
        try_files	$uri $uri/ /index.html;
    }

    location ^~ /api/ {
        proxy_pass   http://119.23.209.19:8081/;

        proxy_set_header  Host $host;
        proxy_set_header  X-Real-IP        $remote_addr;
        proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_set_header  X-NginX-Proxy    true;
    }

    location ^~ /oapi/ {
        proxy_pass   http://119.23.209.19:8083/;

        proxy_set_header  Host $host;
        proxy_set_header  X-Real-IP        $remote_addr;
        proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_set_header  X-NginX-Proxy    true;
    }
}
```



#### 4. Nginx 配置 Http 重定向到Https

```nginx
server {
    listen 80;
    server_name agent.ptweb.shjkkc.com;
    rewrite ^(.*) https://$server_name$1 permanent; # 监听80将http重定向到Https
}
```



#### 5. 注意点

> 1. 配置Https证书，服务器一定要对外开放443（Https默认）端口
> 2. 如果在Docker容器中开启Https，需要将容器的443端口映射到外部宿主的443端口