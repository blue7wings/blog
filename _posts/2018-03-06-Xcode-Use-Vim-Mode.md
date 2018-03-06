---
layout: post
title: "Xcode 安装 vim 插件"
date: 2018-03-06
author: LiamHsia
category: Tutorial
finished: true
---

# Xcode 安装 Vim 插件
Xcode 没有自带Vim模式，我们需要安装第三方插件，来实现此功能。该文章基本翻译自该插件的文档，插件的地址：https://github.com/XVimProject/XVim2

## 系统要求
该插件满足Xcode 9 ，如果你使用的是9以下的版本，请访问[GitHub - XVimProject/XVim: Xcode plugin for Vim keybindings](https://github.com/XVimProject/XVim)，下面是我的系统配置的相关信息，仅供参考。
- MacOS版本： 10.13.3 (17D102)
- Xcode版本： 9.2 (9C40b)

## 重新对Xcode进行签名
Xcode 8以上就已经不再支持第三方插件，我们需要重新对Xcode进行重新签名来实现。

1. 关闭Xcode
2. 准备代码签名证书
- 打开`钥匙串`选择`登录`选项，在菜单栏选择`证书助手`，创建证书
![](http://ooyc2y4k2.bkt.clouddn.com/2018-03-06-Keychain1.png)

- 在弹出的创建窗口中，输入`XcodeSigner`，证书类型选择`Code Signing`，然后点击创建。
![](http://ooyc2y4k2.bkt.clouddn.com/2018-03-06-Keychain2.png)

3. 对Xcode进行重新签名
打开命令行，输入如下代码：
`sudo codesign -f -s XcodeSigner /Applications/Xcode.app`
等待重新签名完成，这可能需要很长一段时间，不要以为是程序卡住而关闭命令行程序。

## 安装插件
1. 下载插件的源码
`git clone https://github.com/XVimProject/XVim2.git`

2. 确认 `xcode-select`是指向Xcode
在命令行中输入`xcode-select -p`，会返回`/Applications/Xcode.app/Contents/Developer`，如果没有显示该路径，请使用`xcode-select -s`命令进行设置。

3. 进入插件源码目录，进行编译。
在源码目录下，一条`make`命令即可，稍作等待，编译完成。
![](http://ooyc2y4k2.bkt.clouddn.com/2018-03-06-build-succeeded.png)
出现如下`Build Succeeded`的编译信息即表示已经编译成功，如果出现`XVim hasn't confirmed the compatibility with your Xcode, Version X.X
Do you want to compile XVim with support Xcode Version X.X at your own risk?`的提示信息，请输入`y`来确认。

4.  重新打开Xcode，会提示是否加载`XVim`插件，点击`是`即可。如果，错误点击了`否`则无法加载插件，此时需要卸载该插件，在终端中输入如下命令：
`defaults delete  com.apple.dt.Xcode DVTPlugInManagerNonApplePlugIns-Xcode-X.X`，`X.X`是你的Xcode版本号，在Xcode的菜单栏，点击`关于Xcode`即可看到。
![](http://ooyc2y4k2.bkt.clouddn.com/2018-03-06-xcode-version.png)

5. 卸载XVim
如果想要卸载该插件，进入该源码目录，`make uninstall`即可。

## 捐助 & 其他
如果你觉得这个插件非常有用，可以为作者进行捐助，捐助地址是：[Bountysource](https://www.bountysource.com/teams/xvim)，想要了解更多相关信息，可以访问该项目Github主页，[XVim](https://github.com/XVimProject/XVim2)
