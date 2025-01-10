---
layout: post
title: "在Linux安装Clash"
author: "Changyi Li"
categories: sample
tags: [post,sample]
image: clash.png
---

# 在Linux安装Clash

笔者因为常开VMWare的Ubuntu虚拟机，并且会下载外网的资源，需要在Ubuntu安装Clash来保证下载。

本篇post源于[chh213](https://chh3213.github.io/2023/11/12/linux%E7%B3%BB%E7%BB%9F%E9%85%8D%E7%BD%AEclash-gui%E7%95%8C%E9%9D%A2/)的博客，写在这里仅供自用方便。

## 下载clash

[备份仓库连接](https://github.com/CHH3213/clash-for-windows-backup/releases/tag/v0.20.39)
下载免安装压缩包
``` sh
wget https://github.com/CHH3213/clash-for-windows-backup/releases/download/v0.20.39/Clash.for.Windows-0.20.39-x64-linux.tar.gz
```
然后解压至想要的位置，并将文件夹更名为`clash`

## 配置桌面图标

进入桌面图标文件夹
``` sh
cd ~/.local/share/applications
```
创建Clash的桌面图标文件
``` sh
vim clash_gui.desktop
```
输入以下内容
```
[Desktop Entry]
Name=clash for linux
Icon=/path/to/clash.png
Exec=/path/to/clash/cfw
Type=Application
```
设置权限
```
chmod a+x clash_gui.desktop
```
## 开启代理

在网络设置中，点开`网络代理`，设置为手动，前两行都是127.0.0.1：7890，倒数第二行是127.0.0.1：7891
## yaml导入
自行处理
