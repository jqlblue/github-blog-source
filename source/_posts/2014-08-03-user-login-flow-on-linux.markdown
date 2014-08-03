---
layout: post
title: "user login flow on linux"
description:
keywords:
date: 2014-08-03 09:53
comments: true
categories:
tags:
---
## linux用户登录过程 ##

问题虽然解决了，但是我们还是需要探求背后的机制。

我们先看看linux系统上用户登录的流程：

{% img /images/os/linux-login-flow.jpg linux用户登录流程 %}

linux系统在加载`kernel`之后，会调用`init`(initialization) 进程，当它完成系统属性，如磁盘，显示器，内核模块，文件系统的检测，并执行完成自启动脚本之后，会启动`getty`或`agetty`进程，调用`/bin/login`进行用户登录验证。验证通过之后，会读取如`/etc/profile`等文件中的相关终端设置，为用户启动`shell`，初始化`shell`运行环境。

其中用户登录验证，主要涉及如下文件：
* /etc/passwd

用户登录相关信息，文件格式如下
    rabbitmq:x:991:988:RabbitMQ messaging server:/var/lib/rabbitmq:/bin/bash
分别对应
    登录用户名，密码，uid，gid，GECOS，主目录，用户shell
> GECOS的全称是General electronic comprehensive operating system，相关信息用,分隔。
> 加密的密码使用x替代，相关内容在/etc/shadow中
* /etc/group
rabbitmq:x:988:
* /etc/shadow
rabbitmq:!!:15866::::::
* /etc/securetty
