---
layout: post
title: "再话tcpcopy的胞弟gryphon"
description: "tcpcopy的胞弟gryphon的介绍，安装，测试和使用"
keywords: "tcpcopy gryphon tcpdump netlink"
date: 2014-05-28 15:57
comments: true
categories: "devops"
tags: "devops, linux"
toc: true
---
# gryphon简介 #
gryphon是由网易研发，能够模拟千万级别并发用户进行压力测试的一个软件，可用于网络消息推送服务方面的压力测试和传统web服务的压力测试。目前由网易的 [王斌](http://weibo.com/tcpcopy)进行维护。更多介绍可参见[A powerful tool to simulate millions of concurrent users for loading testing](https://github.com/wangbin579/gryphon)
<!-- more -->
gryphon的安装和使用方法分为`传统架构方式`和`高级架构方式`。
# 相关角色说明 #
* gryphon客户端（/usr/local/bin/gryphon）
* intercept拦截进程（/usr/local/bin/intercept）


测试时，gryphon客户端读取录制的pcap文件（可以通过tcpdump产生），通过`Raw Socket`修改请求并发送到测试机。因为这些请求中的来源ip可能是模拟的，为了连接的正常关闭，需要拦截测试机的响应包。

    在传统架构下：
    1 netfilter-iptables在协议栈将数据包交给内核中的`ip_queue`模块
    2 使用intercept在用户态通过netlink socket接收内核传来的数据报文
    3 将处理后的报文以及对报文的处理意见（ACCEPT，DROP等）传递给内核协议栈

{% codeblock communication/tc_socket.c lang:c %}
tc_raw_socket_out_init()
{
    int fd, n;

    n = 1;

    /*
     * On Linux when setting the protocol as IPPROTO_RAW,
     * then by default the kernel sets the IP_HDRINCL option and
     * thus does not prepend its own IP header.
     */
    fd = socket(AF_INET, SOCK_RAW, IPPROTO_RAW);
{% endcodeblock %}

    在高级架构下，是通过在测试机上配置静态路由规则，将响应路由到辅助服务器进行处理。

{% img /images/gryphon/raw_socket.jpg raw socket %}
# 传统架构下安装并使用 #
在传统架构下，`intercept拦截进程`需要运行在测试机上，用于拦截测试机的响应包。

## 安装与使用 ##
### 在测试机安装intercept拦截进程 ###
    git clone git://github.com/wangbin579/tcpcopy.git
    cd tcpcopy
    sh autogen.sh
    ./configure --prefix=/usr/local/tcocopy --enable-single
    make
    sudo make install
### 安装gryphon客户端 ###
    git clone git://github.com/wangbin579/gryphon.git
    cd gryphon
    sh autogen.sh
    ./configure --prefix=/usr/local/gryphon --enable-single
    make
    sudo make install
### 使用方法 ###
以`intercept拦截进程`安装在测试机**10.16.15.118**，应用端口是**80**，`gryphon客户端`安装在**10.16.15.117**为例，进行gryphon在传统架构下的基本使用方法。

* tcpdump抓包录制pcap文件

可以在线上服务器抓包录制，并拷贝到`gryphon客户端`所在的服务器**10.16.15.117**

    tcpdump -i eth0 port 80 -s 0 -w xxx.pcap

* 拦截响应包

在测试机**10.16.15.118**上操作
    modprobe ip_queue
    iptables -I OUTPUT -p tcp --sport 80 -j QUEUE
    sudo /usr/local/tcpcopy/bin/intercept

> 如果内核版本3.5，可以使用nfqueue

* 发送测试请求

在**10.16.15.117**上操作
    /usr/local/gryphon/bin/gryphon -x 80-10.16.15.118:80 -f ./xxx.pcap -s 10.16.15.118 -u 1000 -a 2 -c 10.17.15.*

上述命令，从xxx.pcap抓包文件中提取出访问80端口的用户会话过程，模拟1000个用户，将请求复制到测试机**10.16.15.118**的**80**端口中去。其中用户ip地址的范围是10.17.15.*。

> -a参数用于加快数据包的分送速度。-a 2代表将数据包之间的发送间隔缩短一半，相当于加速2倍

gryphon拦截进程中各个基本参数的释义如下：
    gryphon -x historyServerPort-targetServerIP:targetServerPort -f <pcapfile,> -s <intercept address> -u <user num> -c <ip range,>

## 调试步骤 ##
`gryphon客户端`和`intercept拦截进程`运行时，会在当前工作目录生成`error_gryphon.log`，`error_intercept.log`日志文件，里面有相关运行信息。当测试过程不能正常工作时，可优先查看相关日志。

其次就是在测试机或者`gryphon客户端`所在的服务器上进行抓包。确保`gryphon客户端`发出请求，并且没有收到测试机的响应包，如：
{% img /images/gryphon/gryphon_tcpdump.png gryphon tcpdump %}
# 高级架构下安装并使用 #
在高级架构下，`intercept拦截进程`需要运行在辅助服务上，用于拦截测试机的响应包。

## 安装与使用 ##
### 在测试机安装intercept拦截进程 ###
    git clone git://github.com/wangbin579/tcpcopy.git
    cd tcpcopy
    sh autogen.sh
    ./configure --prefix=/usr/local/adv-tcpcopy --enable-single  --enable-pcap --enable-advanced
    make
    sudo make install
### 安装gryphon客户端 ###
    git clone git://github.com/wangbin579/gryphon.git
    cd gryphon
    sh autogen.sh
    ./configure --prefix=/usr/local/adv-gryphon --enable-single --enable-advanced
    make
    sudo make install
### 使用方法 ###
