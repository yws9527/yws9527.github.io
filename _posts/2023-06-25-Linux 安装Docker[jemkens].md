---
title: Linux安装docker
categories: [Docker]
tags: [docker]
---

### 1. 安装Docker-ce

参考：[清华源安装Docker](https://mirrors.tuna.tsinghua.edu.cn/help/docker-ce/)



### 2. 安装nvm-cn

```sh
# 安装
bash -c "$(curl -fsSL https://gitee.com/RubyKids/nvm-cn/raw/main/install.sh)"

# 卸载
bash -c "$(curl -fsSL https://gitee.com/RubyKids/nvm-cn/raw/main/uninstall.sh)"

# 换源
npm config set registry https://registry.npmmirror.com

# 安装最新lts版nodejs
nvm install --lts

# 使用最新lts版nodejs
nvm use --lts

```



### 3. Docker查看容器的初始启动命令参数

参考：[查看容器的初始启动命令参数](https://blog.51cto.com/knifeedge/5667766)

```dockerfile
# 推荐 【get_command_4_run_container】
docker pull cucker/get_command_4_run_container

# 命令格式如下：
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock cucker/get_command_4_run_container [容器名称]/[容器ID]

# 封装别名，使命令简化：
echo "alias get_run_command='docker run --rm -v /var/run/docker.sock:/var/run/docker.sock cucker/get_command_4_run_container'" >> ~/.bashrc \
&& \
. ~/.bashrc

# 简化后：
get_run_command [容器名称]/[容器ID]
```



### 4. Docker 拉取镜像

```dockerfile
docker pull jenkins/jenkins:lts
docker pull nginx:1.22.0
```



### 5. 启动jenkins、nginx容器

```docker
docker run \
	--name jenkins_jkkc\
	--restart=always\
  -p 8081:8080\
  -p 50000:50000\
  -v /home/jenkins_home:/var/jenkins_home\
  -d jenkins/jenkins:lts

# nginx 容器启动
mkdir /home/projects /home/jkkc-ui /home/jkkc-ui/conf /home/jkkc-ui/logs /home/jkkc-ui/html /home/jkkc-ui/ssl

docker run --name nginx_test -p 80:80 -d nginx:1.22.0

docker cp nginx_test:/etc/nginx /home/jkkc-ui/.ng

cp /home/jkkc-ui/.ng/nginx.conf /home/jkkc-ui/conf

cp -r /home/jkkc-ui/.ng/conf.d /home/jkkc-ui/conf

docker run \
 --name nginx-ui \
 -p 80:80 \
 -p 443:443 \
 --restart=always \
 --stop-signal SIGQUIT \
 -v /home/jkkc-ui/ssl:/home/ssl \
 -v /home/jkkc-ui/logs:/var/log/nginx \
 -v /home/jkkc-ui/html:/usr/share/nginx/html \
 -v /home/jkkc-ui/conf/conf.d:/etc/nginx/conf.d \
 -v /home/jkkc-ui/conf/nginx.conf:/etc/nginx/nginx.conf \
 -d nginx:1.22.0

```

- **当目录映射提示权限不足时**

  ```sh
  # 查看容器的日志：
  docker logs -f 容器名/容器id
  
  # 修改映射的目录权限：
  sudo chown -R 1000:1000 /home/docker/jenkins
  ```

- **设置docker容器开机自启**

  ```sh
  docker update --restart=always <containerName>
  ```

  

### 6. jenkins 换国内源

1. 直接更改: [jenkins的工作路径]/updates/default.json

   https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

2. shell脚本更改: cd [jenkins的工作路径]/updates

   ```sh
   sudo sed -i 's#updates.jenkins.io/download/plugins#mirrors.tuna.tsinghua.edu.cn/jenkins/plugins#g' default.json &&
   sudo sed -i 's#www.google.com#www.baidu.com#g' default.json
   ```



### 7. jenkens安装插件

- <u>安装推荐插件</u>
- <u>安装**Nodejs**、[**Gitee**]、**Publish Over SSH**</u>
