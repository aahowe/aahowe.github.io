+++
title = 'Hackintosh-黑苹果'
date = 2022-08-02
draft = false
categories = ["折腾"]

+++

## 什么是黑苹果

根据[Stack Overflow在2021年的统计](https://insights.stackoverflow.com/survey/2021/)中显示：在专业的开发者中有30.04%的人选择使用macOS操作系统。

![Stack Overflow 统计数据](https://images.howe.wang/hackintosh-1.webp)

可以看到包括macOS在内，类Unix操作系统的使用占比接近60%，这个统计结果足以说明大部分的专业程序员是更加偏向于与类Unix系统的。原因也很好解释，在各种开发过程中不可避免的要使用各式各样的命令行工具、配置各种环境，Windows操作系统的命令行使用体验实在谈不上优秀。

但是为什么大家不直接使用Linux作为主力开发操作系统呢？原因是各大常用软件基本上都把Linux视作孤儿，例如“国民级软件”qq和微信到现在都无法在Linux上正常使用。

然而macOS就很好的平衡了这些需求，有强大的终端、与Linux师出同门很多的命令可以通用、各种常用软件都有这与Windows同等级的支持...由于本质上是属于闭源的定制系统，所以可以很好的和苹果其他设备例如：iPhone、iPad、AirPods或者另一台Mac设备协同使用。我觉得macOS相较于Windows的高分辨率屏幕体验要强太多，在4k甚至更高的分辨率下绝大多数软件仍然有着优秀的使用体验，但是Windows这边一旦开启了150%以上的缩放，很多常用软件都会变得模糊不堪。大体上我们可以把macOS当作一个深度定制的Linux发行版使用，但是代价是你必须花大价钱购入一台Mac电脑。

那么我们有没有可能把macOS当作普通的操作系统一样安装在自己的电脑上呢？答案是肯定的。

2005年6月的[苹果全球开发者大会](https://zh.wikipedia.org/wiki/苹果全球开发者大会)（WWDC 2005），当时苹果宣布他们将把其个人电脑从[PowerPC](https://zh.wikipedia.org/wiki/PowerPC)架构转向[英特尔](https://zh.wikipedia.org/wiki/英特爾)架构。所以从2005年开始一直到2020年苹果推出的最后一台基于英特尔架构的电脑从理论上都是可以运行在我们所熟知的英特尔x86架构PC上的。

## 如何打造一台自己的Mac

目前最流行的制作黑苹果方案是使用[OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg)来引导进入macOS。

![OpenCore bootloader 项目](https://images.howe.wang/hackintosh-2.webp)

既然想做那就一定要做一台完美的Mac，我们的目标不仅仅是要能运行macOS，而是要实现一台Mac电脑所能实现的所有功能(Wi-Fi、蓝牙、隔空投送、随航、通用控制...)

因为在macOS中无法自行安装驱动，所有的硬件驱动都是直接写入系统中的，所以我们需要使用已经在系统中写好了驱动的程序，俗称“免驱设备”。[在这里查看支持macOS的硬件设备](https://dortania.github.io/OpenCore-Install-Guide/macos-limits.html)

购买到合适的硬件后，根据OpenCore的文档定制属于自己硬件的引导程序就大功告成啦(实际上中间有很多的坑和困难，感兴趣的同学可以自行搜索教程)

## 我的Mac Pro Mini

经过不断的折腾和捣鼓，我自己打造了一台Mac Pro Mini。

![我的Mac Pro Mini](https://images.howe.wang/hackintosh-3.webp)

它的具体配置如下：

![系统信息](https://images.howe.wang/hackintosh-4.webp)

| 硬件       | 型号                                                         |
| ---------- | ------------------------------------------------------------ |
| **主板**   | [ROG STRIX B460-I GAMING](https://www.asus.com.cn/Motherboards/ROG-STRIX-B460-I-GAMING/) |
| **处理器** | [Intel® Core™ i9-10900](https://www.intel.cn/content/www/cn/zh/products/sku/199328/intel-core-i910900-processor-20m-cache-up-to-5-20-ghz/specifications.html) |
| **显卡**   | [蓝宝石 RX580 8G D5 白金版 OC](https://www.sapphiretech.com/zh-cn/consumer/pulse-rx-580-8g-g5-oc_c) |
| **网卡**   | [BCM94352Z（fenvi）](https://cn.fenvi.com/product_detail_39.html) |
| **内存**   | [芝奇 Ripjaws V 16Gx2](https://gskill.com/cn/product/204/218/1535687484/F4-3200C16S-16GVK) |
| **硬盘**   | [西数 WD_BLACK™ SN750 500G](https://shop.westerndigital.com/zh-cn/products/internal-drives/wd-black-sn750-nvme-ssd#WDS500G3X0C) |

基于OpenCore和以上硬件定制的EFI引导程序我已经开源至GitHub，欢迎各位有兴趣的同学来抄作业、提交issues。本项目持续更新，如果喜欢的话也可以给个star。

[ROG-B460i_Hackintosh_OpenCore_EFI](https://github.com/aahowe/ROG-B460i_Hackintosh_OpenCore_EFI)
