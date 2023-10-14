## Nginx 配置



##### 1. 常规反代

> ```nginx
> location / {
> 
>   proxy_redirect off;
> 
>   proxy_set_header Host $host;
> 
>   proxy_set_header X-Real-IP $remote_addr;
> 
>   proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
> 
>   proxy_pass https://119.23.209.19:8122;
> 
> }
> 
> ```



##### 2. 长连接反代

> ```nginx
> location ^~ /tapi/ {
>   proxy_cache         off;
>   proxy_buffering     off;
>   proxy_http_version  1.1;
>   chunked_transfer_encoding off;
>   proxy_set_header    Connection "";
>   proxy_pass   http://119.23.209.19:8084/;
> }
> 
> ```



##### 3. 虚拟主机SSL 证书配置

> ```nginx
> # 虚拟主机配置
> server {
>   listen 80;
>   server_name  school-test.ptweb.shjkkc.com;    #监听的URL
>   rewrite ^(.*)$ https://${server_name}$1 permanent;
> }
> 
> server {
>   listen 443 ssl;
>   server_name  school-test.ptweb.shjkkc.com;    #监听的URL
>   client_max_body_size     400m;
>   proxy_connect_timeout    600;
>   proxy_read_timeout       600;
>   proxy_send_timeout       600;
>   ssl_certificate   /home/ssl/school/school-test.ptweb.shjkkc.com.pem;
>   ssl_certificate_key  /home/ssl/school/school-test.ptweb.shjkkc.com.key;
>   location / {
>    proxy_redirect off;
>    proxy_set_header Host $host;
>    proxy_set_header X-Real-IP $remote_addr;
>    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
>    proxy_pass https://120.79.6.11:8121;
>   }
>   location ^~ /tapi/ {
>     proxy_cache         off;
>     proxy_buffering     off;
>     proxy_http_version  1.1;
>     chunked_transfer_encoding off;
>     proxy_set_header    Connection "";
>     proxy_pass   http://120.79.6.11:8084/;
>   }
> }
> 
> # 具体服务配置
> server {
>     listen       	443 ssl;
>     server_name  	school-test.ptweb.shjkkc.com;
> 
>     # ssl  on;
>     ssl_certificate  /home/ssl/school-test.ptweb.shjkkc.com.pem;
>     ssl_certificate_key /home/ssl/school-test.ptweb.shjkkc.com.key;
>     ssl_session_timeout 5m;
>     ssl_session_cache shared:SSL:1m;
>     ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
>     ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
>     ssl_prefer_server_ciphers on;
>       
>     #charset koi8-r;
> 
>     #access_log  logs/host.access.log  main;
>     
>     client_max_body_size     400m;
>     proxy_connect_timeout    600;
>     proxy_read_timeout       600;
>     proxy_send_timeout       600;
> 
>     location / {
>       root		/usr/share/nginx/html;
>       index       index.html index.htm;
>       try_files	$uri $uri/ /index.html;
>     }
>     
>     location ^~ /api/ {
>       proxy_pass   http://120.79.6.11:8081/;
> 
>       proxy_set_header  Host $host;
>       proxy_set_header  X-Real-IP        $remote_addr;
>       proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
>       proxy_set_header  X-NginX-Proxy    true;
>     }
>     
>     location ^~ /oapi/ {
>       proxy_pass   http://120.79.6.11:8082/;
> 
>       proxy_set_header  Host $host;
>       proxy_set_header  X-Real-IP        $remote_addr;
>       proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
>       proxy_set_header  X-NginX-Proxy    true;
>     }
> 
>     location ^~ /kapi/ {
>       proxy_pass   http://120.79.6.11:8083/;
> 
>       proxy_set_header  Host $host;
>       proxy_set_header  X-Real-IP        $remote_addr;
>       proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
>       proxy_set_header  X-NginX-Proxy    true;
>     }
> 
>     location ^~ /tapi/ {
>       proxy_pass   http://120.79.6.11:8084/;
> 
>       proxy_cache         off;
>       proxy_buffering     off;
>       proxy_http_version  1.1;
>       chunked_transfer_encoding off;
>       proxy_set_header    Connection "";
>     }
> 
>     #error_page  404              /404.html;
> 
>     # redirect server error pages to the static page /50x.html
>     #
>     # error_page   500 502 503 504  /50x.html;
>     # location = /50x.html {
>     #    root   html;
>     # }
> 
>     # proxy the PHP scripts to Apache listening on 127.0.0.1:80
>     #
>     #location ~ \.php$ {
>     #    proxy_pass   http://127.0.0.1;
>     #}
> 
>     # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
>     #
>     #location ~ \.php$ {
>     #    root           html;
>     #    fastcgi_pass   127.0.0.1:9000;
>     #    fastcgi_index  index.php;
>     #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
>     #    include        fastcgi_params;
>     #}
> 
>     # deny access to .htaccess files, if Apache's document root
>     # concurs with nginx's one
>     #
>     #location ~ /\.ht {
>     #    deny  all;
>     #}
>   }
> 
> ```



##### 4. 限流配置

> ```nginx
> # http 配置
> http {
>   ...
>   #说明：区域名称为one（自定义），占用空间大小为10m，平均处理的请求频率不能超过每分钟20次。
>   limit_req_zone $binary_remote_addr zone=grade:10m rate=20r/m;   
>   #限流返回状态码
>   limit_req_status 503;
>   ...
> }
> 
> # server 配置
> server {
>   ,,,
>   location = /api/inner/student/score {
>    	  #启用限流功能
>       limit_req zone=grade;
>    	  #反向代理的api接口
>       proxy_pass        http://120.79.6.11:8083/inner/student/score;
>       proxy_set_header  Host $host;
>       proxy_set_header  X-Real-IP        $remote_addr;
>       proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
>       proxy_set_header  X-NginX-Proxy    true;
>    }
>    ,,,
> }
> 
> # 返回的状态码配置
> error_page 503 /503;
> 
> # 503限流返回默认值
> location /503 {
>   default_type application.json;
>   add_header Content-Type 'application/json; charset-utf-8';
>   return 200 '{"succ": false, "code": "5503", "data": null, "msg": "当前查询人数较多，请稍候再试！"}';
> }
> ```
>
> 