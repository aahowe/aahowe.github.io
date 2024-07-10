+++
title = '利用云服务器实现校园网免流'
date = 2021-01-02T23:37:54+08:00
draft = false
categories = ["折腾"]
+++

学校的宽带每个月限流60g的问题困扰我很久了。虽然下载基本都去pt站，但是看视频没有办法免流，因此有了自己做一个ipv6节点用来免流的想法

* * *

首先是找vps服务器，国内绝大部分都没有分配ipv6地址，并且宽带小的可怜，所以决定使用国外的服务器，这样带来的另一个好处是可以同时实现科学上网。最终我选择了vultr的东京数据中心，在国内的延迟尚可接受，并且最低5usd一个月的价格也不错

*   如果你所在学校分配了edu邮箱，可以申请[GitHub Education](https://education.github.com/)项目，省去服务器费用
*   服务器的购买和连接参考[https://zhuanlan.zhihu.com/p/112198146](https://zhuanlan.zhihu.com/p/112198146)
*   购买服务器是注意是否支持ipv6地址

我使用的操作系统是CentOS 7，理论上Ubuntu/Debian都可以

* * *

进入控制台后使用秋水逸冰大神的[一键安装脚本](https://teddysun.com/486.html)安装shadowsocks，在控制台输入如下代码

```bash
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1  tee shadowsocks-all.log
```

选择Shadowsocks-Python版本，期间会询问密码，加密方式，端口等，等待安装完毕，最终得到类似下面这样的提示

```
ongratulations, shadowsocks install completed!
Your Server IP:your_server_ip
Your Server Port:4396
Your Password:your_password
Your Local IP:127.0.0.1
Your Local Port:1080
Your Encryption Method:aes-256-cfb

Welcome to visit:http://teddysun.com/486.html
Enjoy it!
```

因为我们需要使用ipv6所以需要对shadowsocks的配置作一点简单的修改

vim 打开配置文件

```bash
vim /etc/shadowsocks-python/config.json
```

按i进入编辑模式在第一行增加ipv6监听(此方法在新python版本ss中会拒绝IPv4的访问)

```
{
  "server":"::",

...
...
...
}
```

**重点是server那里的`::`这样可以同时监听ipv4和ipv6**

ctrl+c退出编辑模式 输入 “:x” （英文冒号+x）保存并退出

重启 shadowsocks

```
/etc/init.d/shadowsocks-python restart
```

使用方法

```
/etc/init.d/shadowsocks-python start # 开启
/etc/init.d/shadowsocks-python stop # 关闭
/etc/init.d/shadowsocks-python restart # 重启
/etc/init.d/shadowsocks-python status # 状态
```

至此，服务端的shadowsocks搭建完毕

* * *

接下来安装谷歌的BBR(BBR是谷歌开源的一款网络防阻塞算法，目的是更好的利用网络资源，实测带宽提升效果非常显著，从2000kbps提升到20000kbps)

仍然使用秋水逸冰大神的[一键安装脚本](https://github.com/teddysun/across)，在控制台输入如下代码

```bash
wget --no-check-certificate -O /opt/bbr.sh https://github.com/teddysun/across/raw/master/bbr.sh
chmod 755 /opt/bbr.sh
/opt/bbr.sh
```

安装过程中会让你选择内核，一般选择最新的版本即可。安装完成后可能会提示重启(如果安装了最新内核),重启后输入一些命令检查是否安装成功

查看内核版本，如果返回内核版本则安装成功

```bash
uname -r
```

查返回值有 tcp\_bbr 模块即说明 bbr 已启动

```bash
lsmod  grep bbr
```

其他验证方法及常见问题：[https://teddysun.com/489.html](https://teddysun.com/489.html)

至此，服务端的安装就基本完成了，接下来是配置本地的[shadowsocks](https://shadowsocks.org/en/download/clients.html)客户端



![这里以Windows10的客户端为例](https://cdn.jsdelivr.net/gh/aahowe/image@main/v610.jpeg)

在编辑服务器中新增一个服务器，填上你的服务器ipv6地址和你刚刚安装shadowsocks时填的端口和密码，然后退出校园网登录，开启代理，起飞

![速度还是很快的](https://cdn.jsdelivr.net/gh/aahowe/image@main/v69.jpeg)

* * *

虽然现在可以使用网站了，但是本地的程序并不能走代理，所以我们要用到另一个软件[Proxifier](https://www.proxifier.com/)(激活码：5EZ8G-C3WL5-B56YG-SCXM9-6QZAP)

![点击服务器设置](https://cdn.jsdelivr.net/gh/aahowe/image@main/v61.jpeg)

![填上本地的shadowsocks服务端口，点击OK](https://cdn.jsdelivr.net/gh/aahowe/image@main/v62.jpeg)

注意要选择version 5

![shadowsocks开启代理](https://cdn.jsdelivr.net/gh/aahowe/image@main/v611.png)

现在你电脑上的任何程序都可以通过代理服务器访问互联网了，尽情enjoy学校的免费ipv6网络吧！

* * *

**以上教程仅供学习参考，切勿用于违法活动。**