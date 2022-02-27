---
title: "Oneplus_9p 折腾记录"
date: 2022-02-27T21:11:37+08:00
draft: false
---

2021 年 4 月买了一台一加9Pro手机,在此过程中,在 ColorOS,OxygenOS 之间反复横跳.一会解决垃圾 Oppo,自动的都是什么垃圾,一会 OxygenOS 又不收到系统推送.Oppo 做事情太糙了,Oxygen 里面各种垃圾自带应用.只是没打开而已.

在此过程中,还通过 adb 卸载过内置软件

```
./adb shell pm uninstall -k --user 0 com.heytap.pictorial
./adb shell pm uninstall -k --user 0 com.coloros.securepay
./adb shell pm uninstall -k --user 0 com.coloros.codebook
./adb shell pm uninstall -k --user 0 com.sohu.inputmethod.sogouoem #搜狗
./adb shell pm uninstall -k --user 0 com.finshell.wallet
```

不过这种可能会导致有些地方按钮是无效的.甚至似乎还影响了 Google 的备份.

这两天又再次横跳,直接进行了解锁,刷了 nameless 这个类原生系统.今天用了一天,还算满意.简单介绍一下.先解锁.

```
手机打开调试模式,然后连接上电脑,输入命令运行：adb reboot bootloader

（首次运行注意在手机弹窗上允许计算机进行调试）

手机会重启进入 Fastboot 模式，接着输入命令：fastboot flashing unlock

会自动重启.
```



然后就是下载好要安装的包,https://forum.xda-developers.com/t/rom-official-oneplus-9-pro-12-0-0_r32-nameless-aosp-2022-02-26.4403301/

```
Instructions:
1. Download boot.img in download link 下载里面的两个包.
2. Reboot phone to bootloader 这个重启进入,直接使用上面的命令.
3. Open any terminal like cmd etc, enter "fastboot flash boot boot.img"
4. Reboot phone to recovery and enter sideload mode 这个在手机上,进入 recovery 之后,通过音量键上下,选择 install update 之类的,会有一个 sideload
5. Enter "adb sideload xxx" (xxx means the filename of rom zip you downloaded)
6. Do factory reset in recovery (Mandatory for first time flash) and Reboot.
```

第一次启动,可能失败,这时候,需要在 sideload 一次..

之后,刷了 magisk 面具,使用 mipush https://github.com/MiPushFramework/MiPushFramework/releases,来接收通知,注意,这里要去 coolapk,安装一个 mipush 注册机,完全模拟出来小蜜,之后装了指纹模块,开启微信指纹.

https://github.com/eritpchy/FingerprintPay
