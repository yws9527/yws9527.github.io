## Npm 离线 & 在线安装



#### Npm 包的定义

- a) 包含由 package.json 文件描述的程序的文件夹
- b) 一个 gzipped tarball，包含 (a)
- c) 解析为 (b) 的 url
- d) 在注册表上发布的 `<name>@<version>` (c)
- e) 指向 (d) 的 `<name>@<tag>`
- f) 具有满足 (e) 的 "latest" 标签的 `<name>`
- g) 一个 `git` url，在克隆时会导致 (a)。

#### Npm 包的安装

- 在线方式

  ```txt
  1.通过指定服务镜像地址安装，可以是 公服|私服
  npm install pack-name --registry=https://registry.npmmirror.com
  
  2.通过指定git地址安装
  npm install git://github.com/user/project.git
  
  3.通过指定tgz地址安装
  npm install http://xxx/xxx.tgz
  ```

- 离线安装

  ```txt
  1.通过指定文件夹安装
  npm install pack-dir
  
  2.通过指定tgz包安装
  npm install pack-name.tgz
  
  3.通过本地npm缓存安装
  复制本地npm缓存文件夹（通常是`~/.npm`或`%AppData%/npm-cache`）到目标机器的相同位置。
  在目标机器上运行`npm install package-name`来安装包
  
  4.使用离线npm工具安装
  例如`npm-offline`、`cnpm`
  
  5.通过npm link安装
  例如：在联网机器上下载pm2的源码并安装好依赖，拷贝到离线服务器上，最后借助npm link将pm2链接到全局区域
  ```

#### Npm 生成tgz包

```npm
在项目的根目录执行如下npm命令，会在当前目录生成pack-name-version.tgz源码包：
npm pack
```

