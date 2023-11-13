---
title: Shell Echo显示颜色
categories: [Linux]
tags: [shell]
---


# shell echo 显示颜色

- shell echo 显示颜色命令格式字体颜色背景颜色黑底彩色字体控制选项示例ref="">tput 命令



## 1. 命令格式

```shell``` 脚本中 ```echo``` 显示内容带颜色显示，格式如下：

```shell
# echo -e "\033[字背景颜色;字体颜色;字体属性m 需要输出的内容 \033[0m"
```

- \033 转义起始符，定义一个转义序列，可以使用 \e 或 \E 代替。
- [ 表示开始定义颜色。
- 字背景颜色 范围 40-47 。
- 字体颜色 范围 30-37 。
- m 转义终止符，表示颜色定义完毕。
- 再次使用 \033[ ，表示再次开启颜色定义，0 表示颜色定义结束，所以 \033[0m 的作用是恢复之前的配色方案。



## 2. 字体颜色

字体颜色：30-37

- 默认=0
- 黑色=30
- 红色=31
- 绿色=32
- 黄色=33
- 蓝色=34
- 紫色=35
- 天蓝色（青色）=36
- 白色=37

```shell
# echo -e "\e[30m 黑色 \e[0m"

# echo -e "\e[31m 红色 \e[0m"

# echo -e "\e[32m 绿色 \e[0m"

# echo -e "\e[33m 黄色 \e[0m"

# echo -e "\e[34m 蓝色 \e[0m"

# echo -e "\e[35m 紫色 \e[0m"

# echo -e "\e[36m 青色 \e[0m"

# echo -e "\e[37m 白色 \e[0m"
```



## 3. 背景颜色

背景颜色：40-47

- 默认=0
- 黑色=40
- 红色=41
- 绿色=42
- 黄色=43
- 蓝色=44
- 紫色=45
- 天蓝色（青色）=46
- 白色=47

```shell
# echo -e "\e[40m 黑底 \e[0m"

# echo -e "\e[41m 红底 \e[0m"

# echo -e "\e[42m 绿底 \e[0m"

# echo -e "\e[43m 黄底 \e[0m"

# echo -e "\e[44m 蓝底 \e[0m"

# echo -e "\e[45m 紫底 \e[0m"

# echo -e "\e[46m 青底 \e[0m"

# echo -e "\e[47m 白底 \e[0m"
```



## 4. 黑底彩色

黑底彩色：90-97

- 黑=90
- 深红=91
- 绿=92
- 黄色=93
- 蓝色=94
- 紫色=95
- 深绿（青色）=96
- 白色=97

```shell
# echo -e "\e[90m 黑底黑字 \e[0m"

# echo -e "\e[91m 黑底红字 \e[0m"

# echo -e "\e[92m 黑底绿字 \e[0m"

# echo -e "\e[93m 黑底黄字 \e[0m"

# echo -e "\e[94m 黑底蓝字 \e[0m"

# echo -e "\e[95m 黑底紫字 \e[0m"

# echo -e "\e[96m 黑底青字 \e[0m"

# echo -e "\e[97m 黑底白字 \e[0m"
```



## 5. 字体控制选项

- \e[0m 关闭所有属性
- \e[1m 设置高亮度
- \e[4m 下划线
- \e[5m 闪烁
- \e[7m 反显，撞色显示，显示为白字黑底，或者显示为黑底白字
- \e[8m 消影，字符颜色将会与背景颜色相同
- \e[nA 光标上移 n 行
- \e[nB 光标下移 n 行
- \e[nC 光标右移 n 行
- \e[nD 光标左移 n 行
- \e[y;xH 设置光标位置
- \e[2J 清屏
- \e[K 清除从光标到行尾的内容
- \e[s 保存光标位置
- \e[u 恢复光标位置
- \e[?25 隐藏光标
- \e[?25h 显示光标

### 5.1. 示例

```shell
#!/bin/bash

# 定义颜色变量

RED='\e[1;31m' # 红
GREEN='\e[1;32m' # 绿
YELLOW='\e[1;33m' # 黄
BLUE='\e[1;34m' # 蓝
PINK='\e[1;35m' # 粉红
RES='\e[0m' # 清除颜色

echo -e "${RED}Red${RES}"
echo -e "${GREEN}Green${RES}"
echo -e "${YELLOW}Yellow${RES}"
echo -e "${BLUE}Blue${RES}"
echo -e "${PINK}Pink${RES}"
echo
echo -e "\e[30m 黑色 \e[0m"
echo -e "\e[31m 红色 \e[0m"
echo -e "\e[32m 绿色 \e[0m"
echo -e "\e[33m 黄色 \e[0m"
echo -e "\e[34m 蓝色 \e[0m"
echo -e "\e[35m 紫色 \e[0m"
echo -e "\e[36m 青色 \e[0m"
echo -e "\e[37m 白色 \e[0m"
echo
echo -e "\e[40m 黑底 \e[0m"
echo -e "\e[41m 红底 \e[0m"
echo -e "\e[42m 绿底 \e[0m"
echo -e "\e[43m 黄底 \e[0m"
echo -e "\e[44m 蓝底 \e[0m"
echo -e "\e[45m 紫底 \e[0m"
echo -e "\e[46m 青底 \e[0m"
echo -e "\e[47m 白底 \e[0m"
echo
echo -e "\e[90m 黑底黑字 \e[0m"
echo -e "\e[91m 黑底红字 \e[0m"
echo -e "\e[92m 黑底绿字 \e[0m"
echo -e "\e[93m 黑底黄字 \e[0m"
echo -e "\e[94m 黑底蓝字 \e[0m"
echo -e "\e[95m 黑底紫字 \e[0m"
echo -e "\e[96m 黑底青字 \e[0m"
echo -e "\e[97m 黑底白字 \e[0m"
```



## 6. tput 命令

```tput``` 命令会利用 ```terminfo``` 数据库中的信息，来控制和更改我们的终端，比如控制光标、更改文本属性、控制屏幕，以及文本涂色。

其中，为文本涂色的方法是：

```shell
tput setab: 用于设置背景色
tput setaf: 用于设置前景色
sgr0: 表示颜色重置
```

颜色定义如下：

**a-draft-node="block" data-draft-type="table" data-size="normal" data-row-style="normal">数值01234567**

### 6.1 示例

```shell
#!/bin/bash
RED=$(tput setaf 1) #红
GREEN=$(tput setaf 2) # 绿
YELLOW=$(tput setaf 3) # 黄
BLUE=$(tput setaf 4) # 蓝
PINK=$(tput setaf 5) # 粉红
RES=$(tput sgr0) # 清除颜色

echo -e "${RED}Red${RES}"
echo -e "${GREEN}Green${RES}"
echo -e "${YELLOW}Yellow${RES}"
echo -e "${BLUE}Blue${RES}"
echo -e "${PINK}Pink${RES}"
```



## 7. 参考地址

shell echo 显示颜色: [https://zhuanlan.zhihu.com/p/181609730](https://zhuanlan.zhihu.com/p/181609730)
