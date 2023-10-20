---
title: Docker中Jenkins升级
categories: [Linux]
tags: [docker]
---

## Docker 中 Jenkins升级

#### 1. 进入容器

```sh
# 进入容器，记得一定用root用户
docker exec -it -u root [container_id] /bin/bash
```



#### 2. 下载最新版Jenkins包

```sh
# 进入jenkins包所在目录
cd /usr/share/jenkins/

# 备份旧版本
mv jenkins.war jenkins.war.old.bak

# 下载最新的jenkins包
## 方式1 - wget:
wget https://updates.jenkins.io/download/war/2.428/jenkins.war

## 方式2 - curl:
curl -O -L https://updates.jenkins.io/download/war/2.428/jenkins.war

```



#### 3. 重启容器

```sh
# 执行完步骤2之后，退出容器，并重启
docker restart [container_id]
```

