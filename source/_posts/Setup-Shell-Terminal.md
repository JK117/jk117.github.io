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
XCLT包含有整个macOS的sdk，clang编译器，make等对于开发而言最基础的系统工具。而 *xcode-select* 管理着xcode的 *developer directory* ，用户可以在多个xcode版本之间切换开发环境
```zsh 首先检查是否已安装
xcode-select -p
```
如果未安装，终端有时会提提示进行安装，已安装则会根据安装方式不同返回以下路径
```zsh 通过Xcode安装
/Applications/Xcode.app/Contents/Developer
```
```zsh 通过命令行安装
/Library/Developer/CommandLineTools
```
如果需要手动安装需要使用管理员权限，输入用户密码后确认安装
```zsh 手动安装命令
sudo xcode-select —-install
```

## Homebrew
[https://brew.sh/](https://brew.sh/)
### 安装
macOS御用包管理器，开发必备。国内安装可能会遭遇网络问题，解决方案会一并给出。注意需要先装Xcode command line tools
```bash 官方安装命令
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
由于国内网络问题，如果出现time out或443之类的报错，最便捷的方法还是使用国内镜像进行安装：
```zsh 镜像安装命令
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```
由于权限管理方式不同，brew在ARM(Apple Silicon)和Intel机型上的安装位置不一样，Intel的在 */usr/local/Homebrew* 下，而ARM(M1/M2)在 */opt/Homebrew* 下，在配置和使用中可能需要注意区别
来源：[Homebrew国内如何自动安装](https://zhuanlan.zhihu.com/p/111014448)

{% note info no-icon 故障排查 %}
如果在`brew update`时遭遇以下报错
```zsh
Warning: No remote 'origin' in /opt/homebrew/Library/Taps/homebrew/homebrew-cask, skipping update!
Warning: No remote 'origin' in /opt/homebrew/Library/Taps/homebrew/homebrew-core, skipping update!
Warning: No remote 'origin' in /opt/homebrew/Library/Taps/homebrew/homebrew-services, skipping update!
Already up-to-date.
```
执行`brew -v`命令看看是不是有两个提示，你的 *homebrew-core* *homebrew-cask* 和 *homebrew-services* 目录被git认为不是一个安全的目录，需要手动添加，且intel和arm机型上路径不一样注意区别
```zsh Intel Mac
git config --global --add safe.directory /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core
git config --global --add safe.directory /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask
git config --global --add safe.directory /usr/local/Homebrew/Library/Taps/homebrew/homebrew-services
```
```zsh Arm Mac(Apple Silicon M1/M2)
git config --global --add safe.directory /opt/homebrew/Library/Taps/homebrew/homebrew-core
git config --global --add safe.directory /opt/homebrew/Library/Taps/homebrew/homebrew-cask
git config --global --add safe.directory /opt/homebrew/Library/Taps/homebrew/homebrew-services
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
{% note info no-icon 可选源 %}
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

### 其它
brew安装完成后建议用brew安装新的git替换原系统自带的git。在Ventura上

## Oh My Zsh
[官网](https://ohmyz.sh/) [Doc](https://github.com/ohmyzsh/ohmyzsh/wiki)
由于国内网络问题，建议安装前关闭所有代理，并将DNS设为 *8.8.8.8*
```zsh 命令行安装
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
安装完成后用户目录下生成 *.zshrc* 文件，之后的shell配置都可以在其中进行。初始配置中有效的配置项仅有：`export ZSH="$HOME/.oh-my-zsh"`，`ZSH_THEME="robbyrussell"`，`plugins=(git)`， 以及`source $ZSH/oh-my-zsh.sh`

## iTerm2
[官网](https://iterm2.com/index.html)
差生文具多，管我代码行不行，命令行终端逼格必须拉满，先上个效果图
{% asset_img iterm_demo.png %}

### 安装
```zsh Homebrew安装
brew install iterm2
```
完成安装后就可以和自带Terminal say goodbye了

### 配置
iTerm安装完成后已经可以开箱即用了，而且鉴于可进行的配置项相当多，建议有时间有心情了再去 *Settings* 中将各个选项卡浏览一遍已获得更好的个性化体验。在这里不展开具体的外观和功能配置，仅介绍几个个人觉得肥肠有用小技巧
1. 热键(Hotkey)
在 *键位(Keys)* 选项卡中，除了常规的快捷键配置，可以在 *热键(Hotkey)* 中设置一个全局唤醒iTerm窗口的热键，我用的是`Option+Space`
{% asset_img mid-size-image iterm_hotkey.png %}
除此以外还可以通过 *Create a Dedicated Hotkey Window* 另外设一个独立窗口热键，我用`Option+/`设置了一个悬浮窗
{% asset_img iterm_dedicated_config.png %}
可以在任意界面唤出而不必切换到主桌面，效果如下
{% asset_img iterm_dedicated_demo.png %}
2. 布局(Arrangement)
我希望每次打开iTerm窗口都能以固定大小显示桌面的固定位置，可以直接调整iTerm窗口的大小和位置后在 *布局(Arrangement)* 中将当前的状态通过左下角 *Set Default* 设为默认状态
3. 导入/导出Profile
辛辛苦苦在 *Profiles* 里折腾一番后如果想分享给别人或者自己留个备份的话可以在左下角展开菜单里选择导出为JSON，日后想复用时直接导入即可

### Powerlevel10k 主题配置
[Github](https://github.com/romkatv/powerlevel10k)
p10k是个脚本化的zsh主题配置工具，基本能以最便捷的方式呈现最成熟的效果，Github主页给出了完成度很高的文档，个人推荐使用homebrew安装，可以自动将所需的环境变量写入 *.zshrc* 。完成安装后终端会自动提示开始选择配置，如果没有自动提示，或者想重新覆写配置
```zsh 启动p10k配置
p10k configure
```
p10k使用 *MesloLGS NF* 字体，配置过程中会提示询问是否安装，墙裂建议安装。并在配置完成后在 *iTerm Settings - Profiles - Text* 中将 *Font* 设置为 *MES咯LGS NF* 。如果使用vscode的话，也需要在vscode设置中修改 *terminal.integrated.fontFamily* 配置项，添加 *'MesloLGS NF'*
另外在 *.zshrc* 中也有针对zsh主题的配置字段 *ZSH_THEME(默认为robbyrussell)* ，注释掉以防冲突

### zsh-autosuggestions 自动补全
[Github](https://github.com/zsh-users/zsh-autosuggestions)
zsh的自动填充插件，实现Tab一时爽，一直Tab一直爽。Github主页给出了文档及安装说明，鉴于已经安装了Oh My Zsh，最简便的插件安装方式是直接clone到 *.oh-my-zsh/custom/plugins* 目录下
```zsh
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```
然后plugin manager载入，即在 *~/.zshrc* 的 *plugins* 中添加 *zsh-autosuggestions*

### zsh-syntax-highlighting 语法高亮
[Github](https://github.com/zsh-users/zsh-syntax-highlighting)
zsh语法高亮插件，看着更舒服点。安装流程同上
```zsh
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```
在 *~/.zshrc* 的 *plugins* 中添加 *zsh-syntax-highlighting* ， 注意必须添加在 *plugins* 的最后
至此基本完成iTerm美化
