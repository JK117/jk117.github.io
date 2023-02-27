---
title: Shell & Terminal Setup
date: 2023-02-27 16:12:42
tags:
---


基于个人踩坑流程梳理的命令行终端安装及配置流程(国内的网啊)
主要包含homebrew + Oh My Zsh + iTerm + VS Code配置
<!--more-->
注：从macOS Catalina开始，系统使用zsh作为默认登录shell和交互式shell
***

## Xcode Command Line Tools
XCLT包含有整个macOS的sdk，clang编译器，make等对于开发而言最基础的系统工具，以及最被普遍需要的git。而 *xcode-select* 管理着xcode的 *developer directory* ，用户可以在多个xcode版本之间切换开发环境
```zsh 首先检查是否已安装
xcode-select -p
```
如果未安装，终端应该会提提示进行安装，已安装则会根据安装方式不同返回以下路径
```zsh 通过Xcode安装
/Applications/Xcode.app/Contents/Developer
```
```zsh 通过命令行安装
/Library/Developer/CommandLineTools
```
如果需要手动安装
```zsh 手动安装命令
xcode-select —-install
```

## Homebrew
[https://brew.sh/](https://brew.sh/)
### 安装
macOS御用包管理器，开发必备。国内安装可能会遭遇网络问题，解决方案会一并给出。注意需要先装Xcode command line tools
```zsh 官方安装命令
/bin/zsh -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
由于国内网络问题，如果出现time out或443之类的报错，最便捷的方法还是使用国内镜像进行安装：
```zsh 镜像安装命令
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```
来源：[Homebrew国内如何自动安装](https://zhuanlan.zhihu.com/p/111014448)

{% note info no-icon 故障排查 %}
如果在`brew update`时遭遇以下报错
```zsh
Warning: No remote 'origin' in /opt/homebrew/Library/Taps/homebrew/homebrew-cask, skipping update!
Warning: No remote 'origin' in /opt/homebrew/Library/Taps/homebrew/homebrew-core, skipping update!
Warning: No remote 'origin' in /opt/homebrew/Library/Taps/homebrew/homebrew-services, skipping update!
Already up-to-date.
```
执行`brew -v`命令看看是不是有两个提示，你的 *homebrew-core* *homebrew-cask* 和 *homebrew-services* 目录被git认为不是一个安全的目录，需要手动添加
```zsh
git config --global --add safe.directory your_homebrew-core_path
git config --global --add safe.directory your_homebrew-cask_path
git config --global --add safe.directory your_homebrew-services_path
```
[来源](https://gitee.com/cunkai/HomebrewCN/issues/I5A7RV)
{% endnote %}

### 换源
为了平时的正常使用，对Homebrew的四个组成部分进行换源
**brew** Homebrew源码仓库
```zsh 查看brew.git当前源
cd "$(brew --repo)" && git remote -v
# origin    https://github.com/Homebrew/brew.git (fetch)
# origin    https://github.com/Homebrew/brew.git (push)
```
```zsh 设置brew.git为阿里源
git -C "$(brew --repo)" remote set-url origin https://mirrors.aliyun.com/homebrew/brew.git
```
```zsh 重置brew.git为官方源
git -C "$(brew --repo)" remote set-url origin https://github.com/Homebrew/brew.git
```
**brew-core** Homebrew核心软件库
```zsh 查看homebrew-core.git当前源
cd "$(brew --repo homebrew/core)" && git remote -v
# origin    https://github.com/Homebrew/homebrew-core.git (fetch)
# origin    https://github.com/Homebrew/homebrew-core.git (push)
```
```zsh 设置homebrew-core.git为阿里源
git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.aliyun.com/homebrew/homebrew-core.git
```
```zsh 重置homebrew-core.git为官方源
git -C "$(brew --repo homebrew/core)" remote set-url origin https://github.com/Homebrew/homebrew-core.git
```
**brew-bottles** Homebrew预编译二进制软件包
```zsh 查看homebrew-bottles当前源
echo $HOMEBREW_BOTTLE_DOMAIN
```
```zsh zsh设置homebrew-bottles.git为阿里源
echo 'export HOMEBREW_BOTTLE_DOMAIN=http://mirrors.aliyun.com/homebrew/homebrew-bottles' >> ~/.zshrc
```
```bash bash设置homebrew-bottles.git为阿里源
echo 'export HOMEBREW_BOTTLE_DOMAIN=http://mirrors.aliyun.com/homebrew/homebrew-bottles' >> ~/.bash_profile
```
```zsh 重置homebrew-bottles.git为官方源
# 在.zshrc或.bash_profile中注释或删去HOMEBREW_BOTTLE_DOMAIN变量
# export HOMEBREW_BOTTLE_DOMAIN=xxxxxxxxx
```
**brew-cask** macOS应用和大型二进制文件
```zsh 查看homebrew-cask当前源
cd "$(brew --repo homebrew/cask)" && git remote -v
# origin    https://github.com/Homebrew/homebrew-cask.git (fetch)
# origin    https://github.com/Homebrew/homebrew-cask.git (push)
```
```zsh 设置homebrew-cask.git为阿里源
git -C "$(brew --repo homebrew/cask)" remote set-url origin http://mirrors.aliyun.com/homebrew/homebrew-cask.git
```
```zsh 重置homebrew-cask.git为官方源
git -C "$(brew --repo homebrew/cask)" remote set-url origin https://github.com/Homebrew/homebrew-cask
```
{% note info no-icon 源 %}
**官方源**
[https://github.com/Homebrew/brew.git](https://github.com/Homebrew/brew.git)
[https://github.com/Homebrew/homebrew-core.git](https://github.com/Homebrew/homebrew-core.git)
[https://github.com/Homebrew/homebrew-cask.git](https://github.com/Homebrew/homebrew-cask.git)
**阿里源**
[https://mirrors.aliyun.com/homebrew/brew.git](https://mirrors.aliyun.com/homebrew/brew.git)
[https://mirrors.aliyun.com/homebrew/homebrew-core.git](https://mirrors.aliyun.com/homebrew/homebrew-core.git)
[http://mirrors.aliyun.com/homebrew/homebrew-bottles](http://mirrors.aliyun.com/homebrew/homebrew-bottles)
[http://mirrors.aliyun.com/homebrew/homebrew-cask.git](http://mirrors.aliyun.com/homebrew/homebrew-cask.git)
**清华源** [help](https://mirrors.tuna.tsinghua.edu.cn/help/homebrew/)
[https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git](https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git)
[https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git](https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git)
[https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles](https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles)
[https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-cask.git](https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-cask.git)
**中科大源**
[https://mirrors.ustc.edu.cn/brew.git](https://mirrors.ustc.edu.cn/brew.git)
[https://mirrors.ustc.edu.cn/homebrew-core.git](https://mirrors.ustc.edu.cn/homebrew-core.git)
[https://mirrors.ustc.edu.cn/homebrew-bottles](https://mirrors.ustc.edu.cn/homebrew-bottles)
[https://mirrors.ustc.edu.cn/homebrew-cask.git](https://mirrors.ustc.edu.cn/homebrew-cask.git)
{% endnote %}

## Oh My Zsh
[官网](https://ohmyz.sh/) [Doc](https://github.com/ohmyzsh/ohmyzsh/wiki)
```zsh 命令行安装
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## iTerm2
[官网](https://iterm2.com/index.html)

### 安装
```zsh Homebrew安装
brew install iterm2
```

### 配置

### Powerlevel10k 主题配置
[Github](https://github.com/romkatv/powerlevel10k)

### zsh-autosuggestions 自动补全
[Github](https://github.com/zsh-users/zsh-autosuggestions)

### zsh-syntax-highlighting 语法高亮
[Github](https://github.com/zsh-users/zsh-syntax-highlighting)

## VS Code
如果安装了额外字体用于iTerm主题，需要在vscode中添加对应字体以便在vscode的终端中达到同样显示效果
按`Command+,`打开vscode设置，搜索 *terminal.integrated.fontFamily* 配置项，添加iTerm中使用的字体(如p10k安装的'MesloLGS NF')