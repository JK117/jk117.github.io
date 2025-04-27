---
title: Note-MacOS-Hostname
tags:
---

macOS hostname相关不完全分析整理
<!--more-->

***

## 情况就是这么个情况
目前手里在用的M2 MBP使用的是Sonoma 14.1.1系统，印象中只在新系统配置时设置了电脑名称为JK-MBP，没有进一步设置过hostname。平时连接公司wifi时terminal中显示的主机名是JK-MBP.local。某次带回家后连接家庭wifi时hostname变成了局域网本地ip。虽然知道hostname会根据网络环境变化而变化，但并没有深入研究过。查询了一些资料也是新旧混杂。虽然还是没完全搞清楚，不过结合测试也八九不离十了，且当做个整理笔记吧。

## ComputerName
### 定义
ComputerName较为简单直观，就是在用户账户层面是最直观的用以表示本机名称的字段，在系统偏好设置的UI界面中即可设置：*系统偏好设置 - 通用 - 关于 - 名称* 
ComputerName为macOS特有字段，也是在Apple ID管理界面的设备列表中所展示的也是对应设备的这个字段。
### 配置项
*/Library/Preferences/SystemConfiguration/preferences.plist: System -> System -> ComputerName*
```zsh
scutil --get ComputerName xxx #查看ComputerName
scutil --set ComputerName xxx #设置ComputerName
```

## hostname
部分较老的资料中显示，终端显示的主机名是环境变量$HOSTNAME的值，但实测目前是$HOST的值。这个变量也是`hostname`命令在显示和修改时访问的对象
```zsh case 1
# 
```

```zsh case 2
test
```

```zsh case 3
```

## HostName, LocalHostName & ComputerName
### HostName
首先明确，在终端中显示的hostname，或主机名，是环境变量$HOSTNAME的值，更准确地说是其第一个句点前的值。这个变量也是`hostname`指令显示和修改的对象。这点在Linux和macOS上是一致的。
这个值在比较旧的macOS版本，如OS X中是按以下顺序来查找的：
1. 从以下文件中读取：/etc/hostconfig
2. 从以下系统配置项读取：/Library/Preferences/SystemConfiguration/preferences.plist中的System ▸ System ▸ HostName
3. 由本机IP地址的反向DNS查询获取
4. 从以下系统配置项读取：/Library/Preferences/SystemConfiguration/preferences.plist中的System ▸ Network ▸ HostNames ▸ LocalHostName
5. 如果以上方法都没获取到，就简单地定为“localhost”
但在较新的版本，如我目前的Ventura中，以上顺序已失效，总结来说有以下新特性：
1. 当路由器的DNS使用默认的 192.168.1.1 或 192.168.0.1 的时候 Terminal 里的计算机名会变成localhost或局域网ip地址
2. 当路由器的DNS使用自定义的，例如运营商的DNS或者公共DNS的时候，Terminal里的计算机名会变成你设置的名字。

### LocalHostName

### ComputerName

## 更改