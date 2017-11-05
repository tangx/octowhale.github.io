---
layout: post
title: Linux 下的几个命令行下载工具
categories: [linux, download]
description: "Linux 下的几个命令行下载工具 支持多线程以及 BT 和 磁力"
keywords: keyword1, keyword2
---

# Linux 下的几个命令行下载工具

linux 下最常用的 `wget` 是单线程的。虽然好用，但不够用。例如在下载阿里云的数据库备份文件，单线程最多能达到 10Mbps，即使是走内网。

因此，有必要换一下其他的。


## axel

> https://github.com/axel-download-accelerator/axel

`axel` 相较 `wget` 最大的优点就是支持多线程。

在 `ubuntu 16.04 LST` 上安装非常简单，已经加入了官方源。 CentOS 系列的话，就去 [pkgs.org](https://pkgs.org/download/axel) 上找到对应版本的安装源。

```bash
sudo apt-get install axel
```

**常用参数**

+ `-c` : 断电续传
+ `-n number` : number 为线程数。
+ `-o filename` : 指定保存文件名。
+ `-a` : 将下载进度合成一条。默认为不断刷屏。
+ `-q` : 静默模式，即不现实下载进度。

```bash
axel -c "URL" -o download.zip
axel -q -n 5 -c "URL"
axel -a -n 5 -c "URL"
```

## aria2

> https://github.com/aria2/aria2

`aria2` 是一个非常强大的工具，不仅支持常规的 `URL` 下载，还支持 `BT` 和 `磁力` 下载。现在网上也有很多交织将 aria2 作为替代迅雷的一个下载工具使用。

在 `ubuntu 16.04 LST` 上安装非常简单，已经加入了官方源。 CentOS 系列的话，就去 [pkgs.org](https://pkgs.org/download/aria2) 上找到对应版本的安装源。

需要注意的是，在 CentOS6.x 上，如果你通过 `YUM` 安装的话， aria2 以来的 nettle-devel 版本比较低。最好使用 `pkgs.org` 的方法安装。

```bash
sudo apt-get install aria2
```

**常用参数**

+ `-xN` : 指定为 N 线程下载
+ `-sN` : 为下载指定 N 个下载源，貌似与 `-xN` 冲突
+ `-c`  : 继续下载。

```bash
# 断点续传
aria2c -c "URL"

# 多线程下载， 5 表示指定的线程数。
aria2c -c -x5 "URL"
```

更多下载参数和命令，参考
+ https://aria2.github.io
+ https://linux.cn/article-7982-1.html


### aria2 配置文件

之前说了 aria2 可以作成一个 web 端的下载工具。
[配置文件](/attachments/2017/aria2.conf)如下:

```bash
## '#'开头为注释内容, 选项都有相应的注释说明, 根据需要修改 ##
## 被注释的选项填写的是默认值, 建议在需要修改时再取消注释  ##

## 文件保存相关 ##

# 文件的保存路径(可使用绝对路径或相对路径), 默认: 当前启动位置
dir=F:\Downloads
# 启用磁盘缓存, 0为禁用缓存, 需1.16以上版本, 默认:16M
#disk-cache=32M
# 文件预分配方式, 能有效降低磁盘碎片, 默认:prealloc
# 预分配所需时间: none < falloc ? trunc < prealloc
# falloc和trunc则需要文件系统和内核支持
# NTFS建议使用falloc, EXT3/4建议trunc, MAC 下需要注释此项
#file-allocation=none
# 断点续传
continue=true

## 下载连接相关 ##

# 最大同时下载任务数, 运行时可修改, 默认:5
#max-concurrent-downloads=5
# 同一服务器连接数, 添加时可指定, 默认:1
max-connection-per-server=5
# 最小文件分片大小, 添加时可指定, 取值范围1M -1024M, 默认:20M
# 假定size=10M, 文件为20MiB 则使用两个来源下载; 文件为15MiB 则使用一个来源下载
min-split-size=10M
# 单个任务最大线程数, 添加时可指定, 默认:5
split=10
# 整体下载速度限制, 运行时可修改, 默认:0
#max-overall-download-limit=0
# 单个任务下载速度限制, 默认:0
#max-download-limit=0
# 整体上传速度限制, 运行时可修改, 默认:0
#max-overall-upload-limit=0
# 单个任务上传速度限制, 默认:0
#max-upload-limit=0
# 禁用IPv6, 默认:false
#disable-ipv6=true
# 连接超时时间, 默认:60
#timeout=60
# 最大重试次数, 设置为0表示不限制重试次数, 默认:5
#max-tries=5
# 设置重试等待的秒数, 默认:0
#retry-wait=0

## 进度保存相关 ##

# 从会话文件中读取下载任务
input-file=aria2.session
# 在Aria2退出时保存`错误/未完成`的下载任务到会话文件
save-session=aria2.session
# 定时保存会话, 0为退出时才保存, 需1.16.1以上版本, 默认:0
save-session-interval=5

## RPC相关设置 ##

# 启用RPC, 默认:false
enable-rpc=true
# 允许所有来源, 默认:false
rpc-allow-origin-all=true
# 允许非外部访问, 默认:false
rpc-listen-all=true
# 事件轮询方式, 取值:[epoll, kqueue, port, poll, select], 不同系统默认值不同
#event-poll=select
# RPC监听端口, 端口被占用时可以修改, 默认:6800
#rpc-listen-port=6800
# 设置的RPC授权令牌, v1.18.4新增功能, 取代 --rpc-user 和 --rpc-passwd 选项
#rpc-secret=<TOKEN>
# 设置的RPC访问用户名, 此选项新版已废弃, 建议改用 --rpc-secret 选项
#rpc-user=<USER>
# 设置的RPC访问密码, 此选项新版已废弃, 建议改用 --rpc-secret 选项
#rpc-passwd=<PASSWD>
# 是否启用 RPC 服务的 SSL/TLS 加密,
# 启用加密后 RPC 服务需要使用 https 或者 wss 协议连接
#rpc-secure=true
# 在 RPC 服务中启用 SSL/TLS 加密时的证书文件,
# 使用 PEM 格式时，您必须通过 --rpc-private-key 指定私钥
#rpc-certificate=/path/to/certificate.pem
# 在 RPC 服务中启用 SSL/TLS 加密时的私钥文件
#rpc-private-key=/path/to/certificate.key

## BT/PT下载相关 ##

# 当下载的是一个种子(以.torrent结尾)时, 自动开始BT任务, 默认:true
#follow-torrent=true
# BT监听端口, 当端口被屏蔽时使用, 默认:6881-6999
listen-port=51413
# 单个种子最大连接数, 默认:55
#bt-max-peers=55
# 打开DHT功能, PT需要禁用, 默认:true
enable-dht=false
# 打开IPv6 DHT功能, PT需要禁用
#enable-dht6=false
# DHT网络监听端口, 默认:6881-6999
#dht-listen-port=6881-6999
# 本地节点查找, PT需要禁用, 默认:false
#bt-enable-lpd=false
# 种子交换, PT需要禁用, 默认:true
enable-peer-exchange=false
# 每个种子限速, 对少种的PT很有用, 默认:50K
#bt-request-peer-speed-limit=50K
# 客户端伪装, PT需要
peer-id-prefix=-TR2770-
user-agent=Transmission/2.77
# 当种子的分享率达到这个数时, 自动停止做种, 0为一直做种, 默认:1.0
# seed-ratio=1
# 强制保存会话, 即使任务已经完成, 默认:false
# 较新的版本开启后会在任务完成后依然保留.aria2文件
#force-save=false
# BT校验相关, 默认:true
#bt-hash-check-seed=true
# 继续之前的BT任务时, 无需再次校验, 默认:false
bt-seed-unverified=true
# 保存磁力链接元数据为种子文件(.torrent文件), 默认:false
bt-save-metadata=true
```
