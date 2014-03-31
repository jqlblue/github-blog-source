---
layout: post
title: "linux文件系统如何进行文件存取"
description: "深入linux文件系统中文件存取的机制"
keywords: "linux fs inode dentry"
date: 2014-03-29 13:32
comments: true
categories: [linux]
tags: [linux,fs]
---
乍一看，这个题目好像有点小儿科。

写文件嘛
    echo hello > /tmp/foo

读文件嘛
    cat /tmp/foo

对于普通文件的存取，的确可以使用上面的方法。但是在linux操作系统上，一
切都是文件。除了`普通文件`，还包括：`目录`，`符号链接`，`IPC Endpoints`（如pipe，unix socket）和`设备文件`(块设备，字符设备)。

当然，本文的重点不在于介绍相关操作技巧，而是希望可以探寻操作背后的机制。

我们要存取的文件，一般都是保存在普通的磁盘上，通过电磁变换来实现文件的读取和写入。所以我们先从磁盘说起。

### 磁盘物理结构 ###

*磁盘的机械结构如下图*：

{% img /images/os/disk-structure.png 'disk strucrure %}

如上图，一块磁盘中有好几块`盘片`（platter），一个盘片上的盘面被划分成若干个同心圆（即`磁道`（track））。
将磁盘上`机械臂`的`磁头`径向移动到盘面的相关位置（磁道）后，通过盘片在磁头下方的高速旋转，就可以读取并写入所在`柱面`（cylinder）上的数据。

*盘面的示意图如下*：

{% img /images/os/disk-structure-2.gif 'disk strucrure %}

磁盘是个块设备，它的最小存储单位是`扇区`（sector），每个扇区存取512字节。

为了提升文件的读取效率，文件系统会以`块`（block）为单位进行文件读取。块是linux系统上文件存取的最小单位，一个块一般为连续的8个扇区，即4K。

直接对磁盘等硬件进行操作，是很困难的。不过操作系统的主要任务就是隐藏硬件，呈现良好，清晰，优雅，一致的抽象。linux文件系统对文件的处理进行了良好的抽象。
### Linux文件系统体系结构 ###
{% img /images/os/fs-structure.gif 'linux file system strucrure %}
### 文件读取流程 ###

### 文件存取流程 ###
