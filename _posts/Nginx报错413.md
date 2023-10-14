# Nginx报错413 Request Entity Too Large



**报错信息：413 Request Entity Too Large**

1.修改 Nginx 的配置文件（**代理服务器**和**真实服务器**都要配置）

```
server {  
    ...  
    client_max_body_size 100m;  
    ...  
}
```

2.重启 Nginx 服务即可。

附：

```
nginx上传大文件，413错误解决
在nginx里增加了配置。
client_max_body_size 100m;
proxy_connect_timeout    600;
proxy_read_timeout       600;
proxy_send_timeout       600;
只用修改client_max_body_size就可以解决问题。
#
其它几个，设置了并未起作用。其参数意义如下：
client_max_body_size
限制请求体的大小，若超过所设定的大小，返回413错误。
client_header_timeout
读取请求头的超时时间，若超过所设定的大小，返回408错误。
client_body_timeout
读取请求实体的超时时间，若超过所设定的大小，返回413错误。
proxy_connect_timeout
http请求无法立即被容器(tomcat, netty等)处理，被放在nginx的待处理池中等待被处理。此参数为等待的最长时间，默认为60秒，官方推荐最长不要超过75秒。
proxy_read_timeout
http请求被容器(tomcat, netty等)处理后，nginx会等待处理结果，也就是容器返回的response。此参数即为服务器响应时间，默认60秒。
proxy_send_timeout
http请求被服务器处理完后，把数据传返回给Nginx的用时，默认60秒。
```