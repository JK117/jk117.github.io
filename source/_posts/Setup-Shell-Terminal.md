---
title: Shell & Terminal Setup
date: 2023-02-27 16:12:42
tags:
---


基于个人踩坑流程梳理的命令行终端安装及配置流程(国内的网啊)
主要包含 Xcode Command Line Tools + Oh My Zsh + homebrew + iTerm
<!--more-->
注：从macOS Catalina开始，系统使用zsh作为默认登录shell和交互式shell
本篇所有流程中 **Xcode Command Line Tools** 必须最先安装，其余推荐配置顺序为
**代理配置** -> **Oh My Zsh** -> **homebrew** -> **iTerm**
开始写了以后发现将所有内容写入一篇文章有点冗长，所以后续会把各个部分拆分为独立文章，并在此链接
<!-- {% post_link Xcode-Cmd-Line-Tools 'Xcode Command Line Tools'%} -->
***

## Xcode Command Line Tools
XCLT包含有整个macOS的sdk，clang编译器，make等对于开发而言最基础的系统工具。而 `xcode-select` 管理着xcode的 *developer directory* ，用户可以在多个xcode版本之间切换开发环境
```zsh 检查是否已安装
xcode-select -p
```
如果未安装，终端有时会提提示进行安装，已安装则会根据安装方式不同返回以下路径
```zsh 已通过Xcode安装
# /Applications/Xcode.app/Contents/Developer
```
```zsh 已通过命令行安装
# /Library/Developer/CommandLineTools
```
如果需要手动安装需要使用管理员权限，输入用户密码后确认安装
```zsh 手动安装
sudo xcode-select --install
```

## 代理配置
由于国内网络问题，直接使用官方方法安装 Oh My Zsh 和 Homebrew 大概率会失败。解决方法有很多种，如国内镜像脚本安装、换源等等。单个人倾向于直接主要使用代理，因此先配置一个可用的代理是很有必要的。我日常使用ClashX开启代理，具体配置不详细展开，这里的重点是记录配置后的代理端口
{% asset_img clashx_proxy.png %}
ClashX的一个优势是可以混合http和socks5的代理端口，我们可以不用区分所使用的工具到底用的到底是不是http/https，无脑往一个端口塞就行。理论上讲肯定是不好的行为，但暂时还没明显问题，后续因为这个踩到坑了再回来补充说明。ClashX默认的混合代理端口是7890，在当前终端的配置文件中加入
```zsh
export ALL_PROXY=socks5://127.0.0.1:7890
```
如果还没有终端的配置文件，可以直接新建
```zsh zsh
touch ~/.zshrc
```
```bash bash
touch ~/.bash_profile
```
如果是其他区分Http和Socks5端口的工具，这里也先选用Socks5的代理端口以便git和brew使用

## Oh My Zsh
[官网](https://ohmyz.sh/) [Doc](https://github.com/ohmyzsh/ohmyzsh/wiki)
在国内建议打开socks5代理进行安装。如果不行的话可以尝试关闭所有代理，注释掉 *.zshrc* 中相关的proxy字段，并将DNS设为 `8.8.8.8` 进行安装
```zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" #命令行安装
```
安装完成后用户目录下生成 *.zshrc* 文件，之后的shell配置都可以在其中进行。如果在这之前已经自写了 *.zshrc* ，旧文件中的内容会被写入 *.zshrc.pre-oh-my-zsh* 作为备份，所以需要把之前写过的配置重新写入新 *.zshrc* 。初始配置如下
```zsh
export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="robbyrussell"
plugins=(git)
source $ZSH/oh-my-zsh.sh
```
主题和插件等事项建议在装完iTerm之后再进一步配置

## Homebrew
[官网](https://brew.sh/)
macOS御用包管理器，开发必备。要求先装Xcode command line tools
由于权限管理方式不同，brew在ARM(Apple Silicon)和Intel机型上的安装位置不一样，Intel的在 */usr/local/Homebrew* 下，而ARM(M1/M2)在 */opt/Homebrew* 下，在配置和使用中可能需要注意区别
此外因为国内网络问题，直接使用官方安装命令多半会出现time out或443之类的报错。在此有两种方案，一是设置socks5代理安装(推荐，体验远优于换源)，二是使用国内镜像进行安装

### 代理安装
按照之前章节打开socks5代理后，直接使用官方安装即可
```zsh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 镜像安装
使用大佬分享的gitee镜像安装可以规避网络问题，安装过程中集成了换源选项
```zsh
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```
如果想手动换源或重置源，可以参照后文换源部分
来源：[Homebrew国内如何自动安装](https://zhuanlan.zhihu.com/p/111014448)

### 验证&故障排查
安装后如果能正常使用`brew update`则pass，如果遭遇以下报错(镜像安装大概率会遇到)
```zsh
Warning: No remote 'origin' in /opt/homebrew/Library/Taps/homebrew/homebrew-cask, skipping update!
Warning: No remote 'origin' in /opt/homebrew/Library/Taps/homebrew/homebrew-core, skipping update!
Warning: No remote 'origin' in /opt/homebrew/Library/Taps/homebrew/homebrew-services, skipping update!
Already up-to-date.
```
执行`brew -v`命令看看是不是有两个提示，你的 *homebrew-core* *homebrew-cask* 和 *homebrew-services* 目录被git认为不是一个安全的目录，需要手动添加，且intel和arm机型上路径不一样注意区别
{% note info no-icon Intel Mac %}
```zsh
git config --global --add safe.directory /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core
git config --global --add safe.directory /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask
git config --global --add safe.directory /usr/local/Homebrew/Library/Taps/homebrew/homebrew-services
```
{% endnote %}
{% note info no-icon Arm Mac(Apple Silicon M1/M2) %}
```zsh
git config --global --add safe.directory /opt/homebrew/Library/Taps/homebrew/homebrew-core
git config --global --add safe.directory /opt/homebrew/Library/Taps/homebrew/homebrew-cask
git config --global --add safe.directory /opt/homebrew/Library/Taps/homebrew/homebrew-services
```
{% endnote %}
[来源](https://gitee.com/cunkai/HomebrewCN/issues/I5A7RV)

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
```zsh bash设置homebrew-bottles.git为阿里源
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
brew安装完成后建议用brew安装新的git替换原系统自带的git(在Ventura及更新系统上会自动替换，重启终端session后生效)


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
p10k是个脚本化的zsh主题配置工具，基本能以最便捷的方式呈现最成熟的效果，Github主页给出了完成度很高的文档，有多种安装方式，个人推荐通过omz自带主题管理进行安装
```zsh
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```
克隆完成后在 *.zshrc* 中修改主题
```zsh
ZSH_THEME="powerlevel10k/powerlevel10k"
```
保存，终端会自动提示开始选择配置，如果没有自动提示，或者想重新覆写配置，可以通过`p10k configure`指令进行
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
