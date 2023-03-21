---
title: Xcode Command Line Tools
tags:
---


Let's go beyond --install
安装 + 机制 + 深入
<!--more-->
Xcode Command Line Tools包含有整个macOS的sdk，clang编译器，make等对于开发而言最基础的系统工具
而 *xcode-select* 管理着xcode的 *developer directory* ，用户可以在多个xcode版本之间切换开发环境
***


## 安装
```bash
xcode-select -p #检查是否已安装
```
如果未安装，终端会返回报错信息，较新的版本会提提示进行安装，已安装则会根据安装方式不同返回以下路径
```bash
>> /Applications/Xcode.app/Contents/Developer #已通过Xcode安装
```
```bash
>> /Library/Developer/CommandLineTools #已通过命令行安装
```
如果需要手动安装需要使用管理员权限，输入用户密码后确认安装
```bash
sudo xcode-select --install #手动安装
```

## 机制


## 深入
To be continue

