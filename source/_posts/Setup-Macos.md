---
title: MacOS Setup
---
基于个人习惯和喜好梳理的MacOS系统基础配(到好用为)置
For macOS Ventura 13.2 & Apple Silicon
<!--more-->

## 命令行设定
### 显示隐藏文件
**推荐** 对于开发而言那必须啊
```
defaults write com.apple.finder AppleShowAllFiles -boolean true
```
```
killall Finder # 重启Finder
```

### 调整鼠标变速
**推荐** Mac的触摸板是爽，但为触摸板调校的鼠标加速度给鼠标用就有点难受了，可以禁用系统的鼠标加速scaling
```
defaults read .GlobalPreferences com.apple.mouse.scaling # 读取当前scaling
```
```
defaults write .GlobalPreferences com.apple.mouse.scaling -1 # 禁用
```

### 禁用翻盖开机
**推荐** 有时候想关机了清洁一下键盘，翻开盖子直接DUANG也会有点不方便
```
sudo nvram AutoBoot=%00 # 禁用
```
```
sudo nvram AutoBoot=%03 # 恢复
```

### IPv6解析
**选用** 仅在需要时使用
```
networksetup -setv6off Wi-Fi # 关闭
```
```
networksetup -setv6automatic Wi-Fi # 恢复auto
```

### 磁盘工具Debug菜单
**选用** 仅在需要时使用
```
defaults write com.apple.DiskUtility DUDebugMenuEnabled 1
```
重启“磁盘工具”，菜单栏里会多出一项“调试”菜单，选中此菜单中的“显示所有分区”菜单项，就会在左侧显示出磁盘的隐藏分区。

## 系统&APP设定
### Finder
**推荐** 尽可能展示有用信息
显示文件扩展名
`Settings` -> `Advanced` -> `Show all filename extensions`
显示路径
`View` -> `Show Path Bar`
显示状态栏
`View` -> `Show Status Bar`

### Dictionary
**推荐** 很多人忽视的系统级功能，在任意界面任意文本词汇上重压触摸板是可以触发词典功能的。在系统语言为美语、简体中文的情况下系统默认开启了许多词典，在此仅选用牛津英汉词典已可以满足日常单词查阅需求了。
`Settings` -> ✅牛津英汉汉英词典

### [Alfred 5](https://www.alfredapp.com/)
**推荐**

### [Mos](https://github.com/Caldis/Mos)
**推荐**

### [Contexts](https://contexts.co/)
**选用**

### [Logi Options+](https://www.logitech.com/en-sg/software/logi-options-plus.html#software-download)
**选用**