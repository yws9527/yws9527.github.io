# 备份导出Windows系统环境变量



#### 1. 导出

```sh
# 方法1
set >> d:\environment.txt

#方法2 【地址栏输入】

# 系统环境变量：
计算机\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment 

# 用户环境变量
计算机\HKEY_CURRENT_USER\Environment

分别选择右键导出
```



#### 2. 导入

```shell
# 方法2
双击导出方法2中的reg注册表文件即可。
```

