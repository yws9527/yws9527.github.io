# WSL2 虚拟网络配置

【！！！适用于win11用户】

### 1. 安装WSL2

```sh
# WSL2安装过程自行百度

wsl --version # 确认是不是 WSL2
```



### 2. 创建外部虚拟交换机

> 1. 开启 Hyper-V 功能，并重启电脑；
>
> 2. 打开 Hyper-V 管理器 ——> 虚拟交换机管理器
>
> 3. 创建外部虚拟交换机
>
>     1.  **[名称]**：WSLBridge；
>
>     2.  **[外部网络]**：选择当前电脑正在使用的网卡: WIFI / 有线网卡；
>
>         勾选 **[允许管理...]**

- 如果无法添加，并提示 ```修改虚拟以太网交换机设置时失败” “以太网端口｛。。。｝绑定失败：灾难性故障```

  解决方案：**<u>在网络连接中去掉Internet共享选项</u>**

  

### 3. 添加全局WSL2启动配置

在Windows用户目录下，创建文件 `.wslconfig`，配置如下：

```ini
[wsl2]
networkingMode = bridged # 桥接模式
vmSwitch = WSLBridge # WSL虚拟网桥
ipv6 = true # 启用 IPv6
```

`wsl --shutdown && wsl` 重启 WSL2



### 4. 检测是否配置成功

```sh
# 在win平台cmd中
ipconfig

# 在wsl子系统命令窗口中
ifconfig
```



### 5. 创建外部虚拟网络交换机环节出错

```sh
# 以管理员权限执行“命令提示符”

# 清理所有的网络设备
netcfg -d

# 重置winsock 或 TCP/UDP堆栈
netsh winsock reset
netsh int ip reset
```

