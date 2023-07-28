---
title: Note-Proxy
tags: Proxy Clash
date: 2023-05-11 15:05:10
---


开发+日常向代理配置的踩坑整理，MacOS(Apple Silicon)+zsh
<!--more-->

***

## 代理协议
### HTTP/HTTPS代理
应用层（OSI Application Layer）代理，类似还有ftp代理。部分应用只支持http代理。
### SOCKS代理
会话层（OSI Session Layer）代理，可以代理包括HTTP、HTTPS、POP3、SMTP、FTP、SFTP等各种类型的网络请求。广泛使用的有socks5代理，即socks协议的第五个版本，在支持TCP的基础上新增对UDP、身份验证、服务端域名解析等功能的支持。

## 系统代理
不管什么系统，设置了系统代理不一定就在任何场景下都能使用（几乎一定不）。所谓的“系统代理”更像是系统提供的“默认代理”接口。
很多应用都有自己的代理设置。比如Chrome默认使用系统代理，Firefox、Telegram则默认不使用系统代理（可手动选择系统代理）。大部分命令行终端默认也不使用系统代理，需要手动配置。

## 命令行终端代理
命令行终端想走代理可以将以下格式内容加入shell的配置文件，zsh写入 */.zshrc*，bash写入 */.bashrc*，
```bash
export http_proxy=http://ip:port
export https_proxy=http://ip:port
```
也可以同时设置所有代理（推荐）
```bash
export ALL_PROXY=socks5://127.0.0.1:7890    # Clash默认混合端口
```

### 环境变量
终端代理有以下常用环境变量
| 环境变量 | 介绍 | 取值 |
| ---- | ---- | ---- |
| http_proxy | 使用http协议的网络连接使用该代理 | ip:port http://ip:port https://ip:port socks5://ip:port |
| https_proxy | 使用https协议的网络连接使用该代理 | 同上 |
| ftp_proxy | 使用ftp协议的网络连接使用该代理 | 同上 |
| all_proxy | 所有网络协议的网络连接都使用该代理 | 同上 |
| no_proxy | 无需代理的主机和域名 | localhost, 10.* , *.ad.com, |

all_proxy 指定了全部协议都可以通过这个代理，它的优先级要低于其他变量。
no_proxy 支持通配符，多个主机/域名之间使用逗号间隔。
另外，还有环境变量的大小写问题。有的应用可能会去读取 HTTP_PROXY 等大写的环境变量，此时自然读取不到对应的值。这种情况下，可以同时设置大小写环境变量来保证兼容性，例如
```bash
export http_proxy=http://xx.xx.xx.xx:1234
export HTTP_PROXY=http://xx.xx.xx.xx:1234
```

<b><span style="color:#00FF00;">export</span> <span style="color:#0000FF;">http_proxy</span>=<span style="color:#FF0000;">http:\/\/</span><span style="color:#FF7F00;">ip</span>:<span style="color:#8B00FF;">port</span></b>

以以上格式为例具体说明环境变量和取值中两个<b>http</b>各自的含义
- 环境变量<b><span style="color:#0000FF;">http_proxy</span></b>指明了为哪种网络协议配置代理，这里是http协议。即只有http协议的网络请求会使用该环境变量配置的代理，其它网络协议，sftp等，则不会使用该环境变量配置的代理。
- 取值中的<b><span style="color:#FF0000;">http</span></b>，表示的是代理服务器的服务协议，即系统与代理服务通信时使用的协议，比较常见的代理协议有：
    - <b><span style="color:#FF0000;">http:\/\/</span></b>（绝大部分）
    - <b><span style="color:#FF0000;">https:\/\/</span></b>
    - <b><span style="color:#FF0000;">socks5:\/\/</span></b>（大部分），特别地，用<b><span style="color:#FF0000;">socks5h</span></b>可以让域名解析在代理服务器进行，防止DNS污染
    - 还有一些代理协议需要专用软件支持后转换为<b><span style="color:#FF0000;">http:\/\/</span></b>、<b><span style="color:#FF0000;">socks:\/\/</span></b>等通用协议以供其它软件使用：<b><span style="color:#FF0000;">ss:\/\/</span></b> <b><span style="color:#FF0000;">ssr:\/\/</span></b> <b><span style="color:#FF0000;">vmess:\/\/</span></b>

## 应用配置
### brew
brew支持全局代理，通过在环境变量中设置 all_proxy 代理端口，并在代理软件中启用同样的协议和端口
- 代理软件指定socks5代理端口号后开启系统代理
- 
```bash
export all_proxy=socks5://127.0.0.1:7890    # Clash默认混合端口
```

### git
- http/https代理
```bash
# 设置全局代理
# http
git config --global http.proxy http://127.0.0.1:1080
# https
git config --global https.proxy https://127.0.0.1:1080
# socks5
git config --global http.proxy socks5://127.0.0.1:1080
git config --global https.proxy socks5://127.0.0.1:1080
# 取消全局代理
git config --global --unset http.proxy
git config --global --unset https.proxy

# 只对github.com使用代理
git config --global http.https://github.com.proxy socks5://127.0.0.1:1080
git config --global https.https://github.com.proxy socks5://127.0.0.1:1080
# 取消github代理
git config --global --unset http.https://github.com.proxy
git config --global --unset https.https://github.com.proxy
```

- ssh代理
```bash ~/.ssh/config
# 设置全局代理
ProxyCommand nc -X 5 -x 127.0.0.1:1080 %h %p
# 只对github.com使用代理
Host github.com
    ProxyCommand nc -X 5 -x 127.0.0.1:1080 %h %p
```

### npm
npm原生不支持socks代理

```bash
# 查看
npm config get proxy
npm config get https-proxy
# 设置
npm config set proxy http://server:port
npm config set https-proxy https://server:port
# 重置
npm config set proxy null # or npm config delete proxy
npm config set https-proxy null # or npm config delete https-proxy
```

### yarn
yarn原生不支持socks代理
```bash
# 查看
yarn config get proxy  
yarn config get https-proxy
# 设置
yarn config set proxy http://server:port
yarn config set https-proxy https://server:port
# 删除
yarn config delete proxy  
yarn config delete https-proxy
```

### pip
```bash
pip install pysocks
pip install -r requirements.txt --proxy='socks5://127.0.0.1:1080'
```