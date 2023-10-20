---
title: Linux安装ohmyzsh
categories: [Linux]
tags: [terminal]
---

### Zsh 是什么

- **Zsh** 是一款强大的虚拟终端，既是一个系统的虚拟终端，也可以作为一个脚本语言的交互解析器。

```bash
# 安装 zsh
yum install zsh -y

# 设置默认终端, 切换shell为zsh
chsh -s /bin/zsh

# 打开终端，在终端上输入: 
zsh --version

# 这个命令来查看我们的电脑上是否安装了 Zsh 
# 端查询版本为： zsh 5.8 (x86_64-apple-darwin20.0)
```

- 提示 `chsh: command not found`，是因为系统没有自带 `util-linux-user` 工具包导致的

```bash
yum install util-linux-user
```

- 终端查询版本为： **zsh 5.7.1 (x86_64-apple-darwin18.2.0)**

```bash
# 查看系统当前 shell
cat /etc/shells 
```



------

### 安装 Oh My Zsh

```bash
# 方式一：从Gitee仓库安装：
sh -c "$(curl -fsSL https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)"
或者
sh -c "$(wget -O- https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)"

# 方式二：从清华源安装
git clone https://mirrors.tuna.tsinghua.edu.cn/git/ohmyzsh.git
cd ohmyzsh/tools
REMOTE=https://mirrors.tuna.tsinghua.edu.cn/git/ohmyzsh.git sh install.sh
rm -rf omhmzsh
```



------

### 安装zsh插件

````bash
# 提供了语法高亮显示
git clone https://gitee.com/mo2/zsh-syntax-highlighting.git ${ZSH_CUSTOM:=~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# 根据历史记录和完成情况建议您键入的命令，而且快速/不干扰自动提示
git clone https://gitee.com/yantaozhao/zsh-autosuggestions.git ${ZSH_CUSTOM:=~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# 命令自动补全
git clone https://gitee.com/wangnd/zsh-completions.git ${ZSH_CUSTOM:=~/.oh-my-zsh/custom}/plugins/zsh-completions

# 修改环境变量
vim ~/.zshrc
```
plugins=(git zsh-completions zsh-autosuggestions zsh-syntax-highlighting)
```
# 更新环境变量
source .zshrc
````

------

### 设置主题

```bash
vim ~/.zshrc

# 找到 ZSH_THEME
# robbyrussell 是默认的主题 random 是随机主题， 推荐主题：ys
ZSH_THEME="robbyrussell"

```



------

### 卸载 Oh My Zsh

- 终端输入 ：

```bash
uninstall_oh_my_zsh
Are you sure you want to remove Oh My Zsh? [y/N]  Y
```



