---
title: SSH Note
---

<!--more-->

## 1 安全机制
Secure Shell(SSH) 是由 IETF(The Internet Engineering Task Force) 制定的建立在应用层基础上的安全网络协议。它是专为远程登录会话(甚至可以用Windows远程登录Linux服务器进行文件互传)和其他网络服务提供安全性的协议，可有效弥补网络中的漏洞。通过SSH，可以把所有传输的数据进行加密，也能够防止DNS欺骗和IP欺骗。还有一个额外的好处就是传输的数据是经过压缩的，所以可以加快传输的速度。目前已经成为Linux系统的标准配置。

SSH只是一种协议，存在多种实现，既有商业实现，也有开源实现。本文主要介绍OpenSSH免费开源实现在Ubuntu中的应用，如果要在Windows中使用SSH，需要使用另一个软件PuTTY。

SSH之所以能够保证安全，原因在于它采用了非对称加密技术(RSA)加密了所有传输的数据。

传统的网络服务程序，如FTP、Pop和Telnet其本质上都是不安全的；因为它们在网络上用明文传送数据、用户帐号和用户口令，很容易受到中间人（man-in-the-middle）攻击方式的攻击。就是存在另一个人或者一台机器冒充真正的服务器接收用户传给服务器的数据，然后再冒充用户把数据传给真正的服务器。

但并不是说SSH就是绝对安全的，它本身提供如下两种级别的验证方法

### 1.1 级别一：基于口令的安全验证
只要你知道自己帐号和口令，就可以登录到远程主机。所有传输的数据都会被加密，但是不能保证你正在连接的服务器就是你想连接的服务器。可能会有别的服务器在冒充真正的服务器，也就是受到“中间人攻击”这种方式的攻击。

### 1.2 级别二：基于密钥的安全验证
你必须为自己创建一对密钥，并把公钥放在需要访问的服务器上。如果你要连接到SSH服务器上，客户端软件就会向服务器发出请求，请求用你的密钥进行安全验证。服务器收到请求之后，先在该服务器上你的主目录下寻找你的公钥，然后把它和你发送过来的公钥进行比较。如果两个密钥一致，服务器就用公钥加密“质询”(challenge)并把它发送给客户端软件。客户端软件收到“质询”之后就可以用你的私钥在本地解密再把它发送给服务器完成登录。与第一种级别相比，第二种级别不仅加密所有传输的数据，也不需要在网络上传送口令，因此安全性更高，可以有效防止中间人攻击。

***
## 2 配置

### 2.1 服务器端
**查看**
```
$ sudo systemctl status ssh
```
**安装**
```
$ sudo apt-get install openssh-server
```
**安装加密集**
```
$ sudo ssh-keygen -A
```
**启动/重启**
```
$ sudo systemctl ssh restart
```
**单次开启**
```
$ sudo systemctl start ssh
```
**单次关闭**
```
$ sudo systemctl stop ssh
```
**开机自动启动**
```
$ sudo systemctl enable ssh
```
**关闭开机自动启动**
```
$ sudo systemctl disable ssh
```
**配置**
```
$ sudo nano /etc/ssh/sshd_config
```
若要使用密码口令登陆，修改`PasswordAuthentication`为`yes`

### 2.2 客户端
**生成密钥**
```
$ ssh-keygen [-t rsa] [-b 4096] [-C username@domain]
```
- `-t`选择加密方式
- `-b`选择加密位数
- `-C`添加邮件地址
- 根据提示输入路径和文件名称，默认会在当前目录下生成，建议生成在`~/.ssh/`目录下
- 选择是否使用私钥口令`passphrase`。

**拷贝公钥**
```
$ ssh-copy-id [-i key_name.pub] <username>@<remote_host>
```
执行后通过默认端口22将公钥写到远程主机的 `~/.ssh/authorized_keys`文件中，如果是其他的端口，可以使用命令：
```
$ ssh-copy-id -p <port> <username>@<remote_host>
```
也可使用scp指令将公钥复制到服务器端`.ssh`目录下使用
```
$ cat public_key.pub >> authorized_keys
```

### 2.3 连接服务器
在客户端Shell中使用`ssh`指令
```
$ ssh [-i private_key] <username>@<remote_host>
```
若生成密钥对时使用了自定义文件名则必须通过`-i`指定私钥

***
## 3 技巧

### 3.1 复制文件
**从本地复制到远程服务器**
复制文件
```
$scp local_file remote_username@remote_ip:file_path
```
复制目录
```
$scp -r local_folder remote_username@remote_ip:folder_path
```
**从远程服务器复制到本地**
复制文件
```
$scp remote_username@remote_ip:file_path local_file
```
复制目录
```
$ $scp -r remote_username@remote_ip:folder_path local_folder
```

### 3.2 远程主机不中断运行程序
TBD
