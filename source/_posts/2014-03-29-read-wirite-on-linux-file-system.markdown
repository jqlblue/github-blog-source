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

对于普通文件的存取，的确可以使用上面的方法。但是在linux操作系统上，一切都是文件。除了普通文件，还包括：`目录`，`符号链接`，`IPC Endpoints`（如pipe，unix socket）和`设备文件`。

当然，本文的重点不在于介绍相关操作技巧，而是希望可以探寻操作背后的机制。

我们先从磁盘说起。

### 磁盘物理结构 ###
