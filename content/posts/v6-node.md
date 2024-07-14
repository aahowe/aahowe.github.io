+++
title = '搭建IPv6+SSL中转服务器'
date = 2022-11-23
draft = false
categories = ["折腾"]

+++

## 前言

在一年多前我写了一个教程[利用云服务器实现校园网免流](https://howe.wang/2021/01/ipv6server/)，在这篇文章中介绍了如何搭建一个使用ShadowSocks协议的IPv6中转服务器，这种方式存在一些弊端，例如：非常容易被GFW嗅探到、客户端已经停更、一些软件的流量无法被接管...经过研究，我找到了一个目前理论上安全性比较高速度也比较快的解决方案，即基于[Trojan](https://trojan-gfw.github.io/trojan/protocol.html)协议，利用TLS加密并且把我们的中转服务器伪装成一个站点。这种方案从技术上来说基本杜绝了被嗅探的可能，让我们的中转服务器更加的稳定。

## 购买服务器

目前支持IPv6的且价格便宜的云服务商也就一个Vultr了，如果还没有注册点击[这里](https://www.vultr.com/?ref=9015661-8H)注册。

这里选择最普通的服务器就行

![选择服务器类型](https://images.howe.wang/v6-node-1.webp)

地区我选择的是纽约，因为这里有3.5$一个月的套餐

![选择服务器地区](https://images.howe.wang/v6-node-2.webp)

系统镜像选择CentOS 7

![选择服务器系统](https://images.howe.wang/v6-node-3.webp)

我这里选择的是3.5\$一个月的套餐，每个月有500g的流量，如果你的需求比较大可以选择5\$一个月的套餐

![选择服务器套餐](https://images.howe.wang/v6-node-4.webp)

查看服务器信息，用IP地址还有密码进行ssh登录

![服务器信息](https://images.howe.wang/v6-node-5.webp)

刚开通服务器的几分钟内是无法进行ssh连接的，需要等待系统安装完毕

![系统安装中](https://images.howe.wang/v6-node-6.webp)

要使用IPv6地址的话得先去设置里添加一个IPv6地址

![开启IPv6](https://images.howe.wang/v6-node-7.webp)

在域名的DNS解析里添加一条AAAA记录指向刚刚分配的IPv6地址

![设置DNS](https://images.howe.wang/v6-node-8.webp)

## 申请SSL证书

使用Trojan协议需要一个SSL证书，在等待系统安装的时候我们去[SSL For Free](https://www.sslforfree.com/)申请一个。

点击申请一个新的证书

![申请](https://images.howe.wang/v6-node-9.webp)

填入需要申请的域名

![填入需要申请的域名](https://images.howe.wang/v6-node-10.webp)

选择免费的90天证书

![选择免费的90天证书](https://images.howe.wang/v6-node-11.webp)

这个自动填入信息开着就好

![自动填入信息](https://images.howe.wang/v6-node-12.webp)

选择免费的套餐就行

![选择套餐](https://images.howe.wang/v6-node-13.webp)

在这里需要验证域名的所有权，点击通过DNS验证

![验证域名](https://images.howe.wang/v6-node-14.webp)

按照上一步的提示去设置域名的DNS解析

![DNS解析](https://images.howe.wang/v6-node-15.webp)

DNS设置完成后就回到申请页面点击验证

![点击验证](https://images.howe.wang/v6-node-16.webp)

验证通过后就可以点击下载证书把证书给下载下来

![下载证书](https://images.howe.wang/v6-node-17.webp)

将压缩包解压可以得到这几个文件

![证书文件](https://images.howe.wang/v6-node-18.webp)

将证书文件上传至服务器

![上传](https://images.howe.wang/v6-node-19.webp)

## 安装和配置x-ui

[x-ui](https://github.com/vaxilu/x-ui)是一个支持多协议多用户的 xray 面板，支持vmess、vless、trojan、shadowsocks、dokodemo-door、socks、http协议，功能非常强大，我们的trojan服务就是使用它创建和管理的。

### 安装x-ui

在服务器终端输入命令：

```bash
bash <(curl -Ls https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh)
```

之后将会执行脚本自动安装，中途需要自行输入后台用户名密码还有端口号。

![安装完成](https://images.howe.wang/v6-node-20.webp)

安装完成后先停用CentOS的防火墙

```bash
systemctl stop firewalld
```

然后在浏览器访问`服务器IP:你设置的端口号`看看是否能够进入x-ui后台，如果成功进入则表示安装成功。

### 配置x-ui

在面板设置里填入刚刚上传的SSL证书路径，点击重启面板，之后就可以通过`域名：端口号`来访问x-ui面板了

![设置SSL证书](https://images.howe.wang/v6-node-21.webp)

在入站列表添加一个trojan节点，端口填443，公钥和密钥还是填刚刚上传的证书路径，最后点击添加即可

![添加trojan节点](https://images.howe.wang/v6-node-22.webp)

## 使用Clash连接

虽然支持trojan的客户端有很多，但我最喜欢用的还是Clash，因为它的功能特别强大，甚至可以当作一个网关使用。

在x-ui的入站列表中点击刚刚创建的trojan节点，打开二维码选项就可以复制链接了。

![复制节点](https://images.howe.wang/v6-node-23.webp)

单独的trojan节点是无法加入Clash的，这里我们需要一个Clash订阅连接转换工具，地址：https://v2rayse.com/v2ray-clash

更多Clash的操作可以参考：https://docs.cfw.lbyczf.com/

