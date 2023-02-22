---
title: MacOS Setup
---
基于个人习惯和喜好梳理的MacOS系统基础配(到好用为)置
For macOS Ventura 13.2 & Apple Silicon
<!--more-->

## 命令行设定
### 显示隐藏文件 (Recommanded)
```
defaults write com.apple.finder AppleShowAllFiles -boolean true
```
```
killall Finder # 重启Finder
```

### 调整鼠标变速 (Recommanded)
```
defaults read .GlobalPreferences com.apple.mouse.scaling # 读取当前scaling
```
```
defaults write .GlobalPreferences com.apple.mouse.scaling -1 # 禁用
```

### 禁用翻盖开机 (Recommanded)
```
sudo nvram AutoBoot=%00 # 禁用
```
```
sudo nvram AutoBoot=%03 # 恢复
```

### IPv6解析 (Optional)
```
networksetup -setv6off Wi-Fi # 关闭
```
```
networksetup -setv6automatic Wi-Fi # 恢复auto
```

### 磁盘工具Debug菜单 (Optional)
```
defaults write com.apple.DiskUtility DUDebugMenuEnabled 1
```
重启“磁盘工具”，菜单栏里会多出一项“调试”菜单，选中此菜单中的“显示所有分区”菜单项，就会在左侧显示出磁盘的隐藏分区。

## 系统&APP设定
### Finder
显示文件扩展名
`Settings` -> `Advanced` -> `Show all filename extensions`
显示路径
`View` -> `Show Path Bar`
显示状态栏
`View` -> `Show Status Bar`

### Dictionary
`Settings` -> ✅牛津英汉汉英词典

### Alfred 5
Download

### MOS
Download

### Contexts
Download

### Logi Options+
Download