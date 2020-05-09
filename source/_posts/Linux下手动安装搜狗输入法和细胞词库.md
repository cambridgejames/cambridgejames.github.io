---
title: Linux下手动安装搜狗输入法和细胞词库
id: 2020-05-09 21:14:48
date: 2020-05-09 21:14:48
tags:
  - Linux
  - 运维
categories: Linux运维
---

# Linux下手动安装搜狗输入法和细胞词库

## 1. 安装搜狗输入法Linux版

访问搜狗输入法官网[https://pinyin.sogou.com/](https://pinyin.sogou.com/)，点击上方的“输入法Linux版”

![](https://gitee.com/powerinv/picgo-repo/raw/master/img/深度截图_选择区域_20200509222401.png)

按照自己的系统下载对应版本的安装包

![](https://gitee.com/powerinv/picgo-repo/raw/master/img/深度截图_选择区域_20200509222430.png)

双击下载好的`deb`安装包，安装搜狗输入法

## 2. 安装细胞词库

在系统托盘的搜狗拼音输入法图标上`右键->设置搜狗拼音->词库`查看已经安装的词库

![](https://gitee.com/powerinv/picgo-repo/raw/master/img/深度截图_选择区域_20200509223007.png)

![](https://gitee.com/powerinv/picgo-repo/raw/master/img/深度截图_sogou-qimpanel_20200509223945.png)

点击“`下载更多细胞词库`”按钮，前往搜狗拼音官网下载新的细胞词库

![](https://gitee.com/powerinv/picgo-repo/raw/master/img/深度截图_选择区域_20200509224243.png)

双击细胞词库进行安装

## 3. 可能会遇到的问题

### 1. 词库文件已损坏，请重新下载

有时双击`.scel`文件时会遇到“xxx词库文件已损坏，请重新下载！”的提示，如下图：

![](https://gitee.com/powerinv/picgo-repo/raw/master/img/20191231163415190.png)

这时只需要将要安装的词库文件添加至压缩文件（如.zip文件）中，并用归档文件管理器打开，再双击词库文件进行安装，即可成功安装。