---
title: MacOS Setup
---
基于个人习惯和喜好梳理的MacOS系统基础配(到好用为)置
For macOS Ventura 13.2 & Apple Silicon
<!--more-->

***

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

***

## 系统&APP设定
### Finder
**推荐** 尽可能展示有用信息
✅ `Settings - Advanced - Show all filename extensions`
✅ `View` - `Show Path Bar`
✅ `View` - `Show Status Bar`

### Dictionary
**推荐** 很多人忽视的系统级功能，在任意界面任意文本词汇上重压触摸板是可以触发词典功能的。在系统语言为美语、简体中文的情况下系统默认开启了许多词典，在此仅选用牛津英汉词典已可以满足日常单词查阅需求了。
`Settings` - ✅ 牛津英汉汉英词典

### [Alfred 5](https://www.alfredapp.com/)
**推荐** 老面孔了，比较坑的是5开始导出/导入备份的profile变成收费功能了，暂时还没去折腾怎么自己实现，先上常规配置
`General`
- ✅ Launch Alfred at login
- Hotkey建议使用`Command+Space`，替换原系统自带Spotlight的快捷键

`Feature`
- `Web Search` - `Add Custom Search`

| Keyword | Title | Search URL |
| :--- | :--- | :--- |
| amz | Search Amazon for {query} | https://www.amazon.com/s?k={query} |
| bing | Search Bing for {query} | https://cn.bing.com/search?q={query} |
| bl | Search Bilibili for {query} | https://search.bilibili.com/all?keyword={query}&from_source=webtop_search |
| tb | Search Taobao for {query} | https://s.taobao.com/search?q={query}&commend=all |
| zh | Search Zhihu for {query} | https://www.zhihu.com/search?type=content&q={query} |
| tl | Translate {query} from English to Chinese | https://cn.bing.com/translator?ref=TThis&text={query}&from=en&to=zh-Hans |
| tlc | Translate {query} from Chinese to English | https://cn.bing.com/translator?ref=TThis&text={query}&from=zh-Hans&to=en |


<!-- - amz - https://www.amazon.com/s?k={query} - Search Amazon for {query}
- bing - https://cn.bing.com/search?q={query} - Search Bing for {query}
- bl - https://search.bilibili.com/all?keyword={query}&from_source=webtop_search - Search Bilibili for {query}
- tb - https://s.taobao.com/search?q={query}&commend=all - Search Taobao for {query}
- zh - https://www.zhihu.com/search?type=content&q={query} - Search Zhihu for {query}
- tl - https://cn.bing.com/translator?ref=TThis&text={query}&from=en&to=zh-Hans - Translate {query} in English into Chinese
- tlc - https://cn.bing.com/translator?ref=TThis&text={query}&from=zh-Hans&to=en - Translate {query} in Chinese into English -->

### [Contexts](https://contexts.co/)
**推荐** Contexts可以实现通过快捷键切换窗口，包括同一应用的不同窗口。目前阶段依然比Ventura的台前调度好用。重点配置*Command-Tab*项中切换窗口的组合键(习惯使用`Command+Tab`激活切换)，注意勾选*Show windows from: All Spaces*(在所有显示器的窗口间切换)和*Show windows of: All apps*(在所有应用的窗口间切换)。注意*Search*项中的默认组合键`^+Space`可能与其他应用冲突，建议取消勾选
<img src="/images/contexts_tab.png" width="70%" height="70%">
切换效果如图
<img src="/images/contexts_panel.png" width="70%" height="70%">


### [Mos](https://github.com/Caldis/Mos)
**推荐** 一个用于在MacOS上平滑鼠标滚动效果的小工具, 填平mac鼠标控制的坑，让滚轮爽如触控板。注个人设置如下，意最好*关闭*系统和其他第三方鼠标工具(如Logi Options)中的*Smooth Scrolling*(*平滑滚动*)，并设置*Scroll Diraction*为*Natural*
<!-- | ![](\images\mos_general.png) | ![](\images\mos_advanced.png) | -->
| <img src="/images/mos_general.png"> | <img src="/images/mos_advanced.png"> |
| --- | --- |
<!-- <img src="/images/mos_general.png" width="50%" height="50%"> -->
<!-- <img src="/images/mos_advanced.png" width="50%" height="50%"> -->

### [Logi Options+](https://www.logitech.com/en-sg/software/logi-options-plus.html#software-download)
**选用** MX Master yyds啊。鼠标键位配置后非常适合mac使用。个人设置如下，注意如果配合Mos使用的话关闭*Smooth scrolling*，并设置*Scoll direction*为*Natural*
<!-- | ![](\images\logi_options_buttons.png) | ![](\images\logi_options_wheel.png) | -->
| <img src="/images/logi_options_buttons.png"> | <img src="/images/logi_options_wheel.png"> |
| --- | --- |
<!-- <img src="/images/logi_options_buttons.png" width="50%" height="50%"> -->
<!-- <img src="/images/logi_options_wheel.png" width="50%" height="50%"> -->
