# Docker 镜像导出导入

- **Docker 导出**

  ```docker
  # 将id为c30b3d62f844的文件导出到压缩包
  docker save [镜像ID] > [镜像名称].rar
  # exp：docker save c30b3d62f844 > jkkc-pt-operate-web-image-v2.rar
  ```

  

- **Docker 导入**

  ```docker
  # 导入image文件
  docker load < [镜像名称].rar
  # exp：docker load < jkkc-pt-operate-web-image-v2.rar
  
  # 给这个导入的image打个标签，这样后续再有导入的docker images就不容易弄混了
  docker tag [镜像id] [镜像名称]:[版本]
  # exp：docker tag c30b3d62f844 jkkc-pt-operate-web-image:v2
  ```

  