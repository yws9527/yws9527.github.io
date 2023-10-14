# gitlab卡在ruby_block[wait for redis service socket] action run



#### 解决方案:

###### 方案一：在另外开启一个终端启动如下命令

```bash
sudo /opt/gitlab/embedded/bin/runsvdir-start
```



###### 方案二：在当前的终端窗口中后台执行上述命令

```bash
nohup /opt/gitlab/embedded/bin/runsvdir-start &
```



方案一和方案二之后再执行：

```bash
sudo gitlab-ctl reconfigure
```