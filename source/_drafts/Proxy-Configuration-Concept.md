---
title: Proxy-Configuration-Concept
tags:
---


## 代理配置
由于国内网络问题，直接使用官方方法安装 Oh My Zsh 和 Homebrew 大概率会失败。解决方法有很多种，如国内镜像脚本安装、换源等等。单个人倾向于直接主要使用代理，因此先配置一个可用的代理是很有必要的。我的方案是 ClashX + 自建ss，代理配置不详细展开，这里的重点是记录配置后的代理端口
{% asset_img clashx_proxy.png %}
ClashX的一个优势是可以混合http和socks5的代理端口，我们可以不用区分所使用的工具到底用的到底是不是http/https，无脑往一个端口塞就行。理论上讲肯定是不好的行为，但暂时还没明显问题，后续因为这个踩到坑了再回来补充说明。ClashX默认的混合代理端口是7890，在当前终端的配置文件中加入
```bash
export ALL_PROXY=socks5://127.0.0.1:7890
```
如果还没有终端的配置文件，可以直接新建
```bash
touch ~/.zshrc #zsh
```
```bash
touch ~/.bash_profile #bash
```
如果是其他区分Http和Socks5端口的工具，这里也先选用Socks5的代理端口以便git和brew使用

Socks5支持TCP/UDP(即用户数据包协议)，工作在传输层上，因此只是简单地传递数据包，而不必关心是何种应用协议(FTP/HTTPs/NNTP)
socks5: 更基础更快，代理范围更广更灵活
http/https: 更安全