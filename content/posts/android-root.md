+++
title = 'Android系统获取root权限教程'
date = 2022-03-18T20:03:07+08:00
draft = false
categories = ["折腾"]
+++

众所周知Android系统在获取root权限之后可以解锁各种各样的玩法(超频、删温控、刷模块、修改系统配置...)没有root权限的安卓机是没有灵魂的。现在的主流方法是用magisk修补boot镜像来获取root权限，操作十分简单。

## 详细的视频教程和相关链接

> 《玩机必看！带你入坑安卓刷机，小白也能看懂的ROOT基础指南来啦！》
>
> https://www.bilibili.com/video/BV1BY4y1H7Mc
>
> 各种玩机地址/工具/命令汇总：http://wanji.jamcz.com/
>
> Android SDK（ADB和Fastboot电脑端工具）：https://developer.android.google.cn/studio/releases/platform-tools?hl=zh-cn 
>
> ADB/Fastboot驱动：https://cz-jam.lanzouj.com/iZICY02v2k8j 
>
> TWRP：https://twrp.me/ 
>
> Magisk：https://github.com/topjohnwu/Magisk 
>
> 小米解锁工具：https://www.miui.com/unlock/index.html 
>
> 小米ROM下载：https://xiaomirom.com/series/ 
>
> 小米刷机工具：https://cdn.alsgp0.fds.api.mi-img.com/micomm/MiFlash2020-3-14-0.rar 
>
> 一加ROM下载：https://www.oneplus.com/cn/support/softwareupgrade 
>
> 三星刷机工具：https://odindownload.com/ 
>
> 三星ROM下载：https://www.sammobile.com/firmwares/ 
>
> 魅族ROOT链接：https://mroot.flyme.cn/ 
>
> OPPO解锁：https://www.oppo.cn/thread-397164526-1 
>
> OPPO ROM：https://www.coloros.com/rom 
>
> Realme解锁：https://www.realmebbs.com/post-details/1275426081138028544 
>
> Realme刷机工具：https://www.realmebbs.com/post-details/1271082970484060160 
>
> Realme ROM：https://www.realme.com/support/software-update 
>
> 索尼解锁：https://developer.sony.com/develop/open-devices/get-started/unlock-bootloader 
>
> 索尼ROM：https://xperifirm.com/ 
>
> MOTO解锁：https://motorola-global-portal.custhelp.com/app/standalone/bootloader/unlock-your-device-a 
>
> MOTO ROM：https://mirrors.lolinet.com/firmware/motorola/ 
>
> payload-dumper解包工具：https://mrzzoxo.lanzouw.com/iR65zpaueyd 
>
> 酷安（玩机社区）：https://www.coolapk.com/ 
>
> XDA（海外玩机论坛）：https://forum.xda-developers.com/

## 解锁bootloader

手机厂商会出场默认锁定bootloader(用来防止系统启动文件被替换)，刷入magisk的第一步就是把这个bootloader解开，每家厂商的解锁方法不尽相同，这里放出一个b站教程。

> 『解BL锁』B站最强 之 一个视频解决你手机的BL锁困扰
> 
> [https://www.bilibili.com/video/BV1tT4y1d7ao](https://www.bilibili.com/video/BV1tT4y1d7ao)

## 获取系统镜像中的boot.img文件

刷入magisk获取root的核心步骤就是要利用magisk对boot.img进行修补然后替换掉原厂的boot文件，所以第一步就是要获取原厂镜像的boot.img。这里以**Google Pixel 4**为例，下拉状态栏查看当前系统版本。

<img src="https://cdn.jsdelivr.net/gh/aahowe/image@main/root1.png" alt="如图版本为 SP2A.220305.012" style="zoom:25%;" />

进入手机镜像官网下载该版本的刷机包。

![下载完成后解压该系统镜像可以看到里面还有一个zip压缩文件](https://cdn.jsdelivr.net/gh/aahowe/image@main/root2.png)

![解压这个image-flame-sp2a.220305.012.zip](https://cdn.jsdelivr.net/gh/aahowe/image@main/root3.png)

把它解压，我们想要的boot.img就在这里，将它传入待root手机

![图上第二行boot.img](https://cdn.jsdelivr.net/gh/aahowe/image@main/root4.png)

## 使用magisk修补boot.img

下载并安装magisk：[https://github.com/topjohnwu/Magisk/releases](https://github.com/topjohnwu/Magisk/releases)，此时超级用户选项还无法进入

<img src="https://cdn.jsdelivr.net/gh/aahowe/image@main/root5.png" alt="我们可以看到目前是“无法获取”状态" style="zoom:25%;" />

点击安装-选择并修补一个文件-选择刚刚传入手机的boot.img-开始

<img src="https://cdn.jsdelivr.net/gh/aahowe/image@main/root6.png" alt="选择修补文件" style="zoom:25%;" />

<img src="https://cdn.jsdelivr.net/gh/aahowe/image@main/root7.png" alt="修补boot" style="zoom:25%;" />

完成后在/Download目录中会出现一个被修补好的boot文件，将它传回电脑

<img src="https://cdn.jsdelivr.net/gh/aahowe/image@main/root8.png" alt="图中名为magisk\_patched-24300\_95wLm.img" style="zoom:25%;" />

## 刷入boot.img

手机进入开发者模式，开启USB调试功能连接电脑

> 如果不会配置adb环境和驱动这里推荐使用酷安晨钟酱开发的《搞机工具箱》
> 
> [https://jamcz.com/gjgjx/](https://jamcz.com/gjgjx/)

进入bootloader

```
adb reboot bootloader
```

由于Pixel系列在Android12直接刷入修补后的boot.img会开不了机(我在写这篇博客的时候就翻车了，其他手机保险起见也可以用下列方法，头铁直接fastboot flash刷入也行)所以先不刷入，使用修补后的boot开机。**如果你不小心直接刷入无法开机，可以Google一下有没有关闭verify的方法，或者把各个分区单独刷回官方固件，如果都无法启动，只能丢失数据重刷系统了。**

```
fastboot boot /你的boot.img存放路径/修改后的boot.img
```

启动后打开magisk可以看到超级用户已经可以打开了，但是重启后root就没了，还差最后一个步骤

<img src="https://cdn.jsdelivr.net/gh/aahowe/image@main/root9.png" alt="magisk已开启" style="zoom:25%;" />

点击安装，比刚才多了一个“直接安装”的选项，选择后点击开始刷入

<img src="https://cdn.jsdelivr.net/gh/aahowe/image@main/root10.png" alt="直接安装" style="zoom:25%;" />

刷入完成后重启设备

<img src="https://cdn.jsdelivr.net/gh/aahowe/image@main/root11.png" alt="刷入完成" style="zoom: 25%;" />

到此Android系统的root就大功告成了，重启后root不丢

