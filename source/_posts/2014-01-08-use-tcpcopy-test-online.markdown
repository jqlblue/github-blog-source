---
layout: post
title: "使用tcpcopy导入线上流量进行功能和压力测试"
description: "tcpcopy install configure test online feature"
date: 2014-01-08 17:29
comments: true
categories: [devops]
---
假设我们要上线一个两年内不会宕机的先进架构。在上线前，免不了单元测试，功能测试，还有使用ab，webbench等等进行压力测试。

但这些步骤都是模拟的，非生产环境下正式用户的行为。或许你会想到灰度上线，但毕竟可能会影响到部分用户，这怎么对得起我们两年内不宕机的承诺呢？

好在网易的 [王斌](http://weibo.com/tcpcopy) 开发了[tcpcopy](https://github.com/wangbin579/tcpcopy)， 可以导入线上流量进行功能和压力测试。

### tcpcopy介绍 ###

`tcpcopy`是一种请求复制工具。可以将线上流量拷贝到测试机器，实时的模拟线上环境。在不影响线上用户的情况下，使用线上流量进行测试，以尽早发现bug。也可以通过放大流量，进行压力测试，评估系统承载能力。

`tcpcopy`可以从线上服务器的`IP`层抓取在线请求的数据包，修改相关属性，利用`row socket output`技术（`packet injection` 技术之一）将其发送给测试服务器进行测试。

发送到测试服务器的数据包会在`TCP/IP`协议栈被识别，最终进入到测试服务器的上层应用（如nginx），上层应用在处理完请求之后，将响应传递给测试服务器的`TCP/IP`协议栈。

测试服务器上启用`ip_queue`模块，使用`iptables`在`IP`层将响应结果数据包发往QUEUE（`ip_queue`）。

测试服务器上运行在`用户空间`的拦截程序（intercept进程），通过打开`netlink`的socket接受内核通过`ip_queue`所传递来的网络数据包（即上层应用的响应内容）进行裁定，将结果返回内核，进行出队列的操作。intercept进程默认会丢弃上层应用的响应内容，返回ip header，以释放tcp连接。

intercept进程也可以通过`-x`（passlist）参数，不drop指定ip lists发出请求的响应内容。默认drop是为了：
    1 减少出口带宽占用，节约成本
    2 不影响客户端（线上服务）的`TCP/IP`协议栈
    3 不会在互联网上产生ghost数据包

### tcpcopy工作流程 ###

以nginx作为前端说明tcpcopy的工作流程
{% img /images/tcpcopy_flow.png 'tcpcopy flow' %}

上图中左边是线上前端机，右边是测试前端机。线上前端机开启tcpcopy客户端（tcpcopy进程），测试前端机开启tcpcopy服务端（intercept进程），且两台机器上都启动了nginx服务。

Tcpcopy拷贝一次流量访问的步骤如下：

    ①　一个访问到达线上前端机；
    ②　socket包在ip层被拷贝了一份传给tcpcopy进程；
    ③　tcpcopy修改包的目的及源地址，发给测试前端机；
    ④　拷贝的包到达测试前端机；
    ⑤　测试前端机的nginx处理访问，并返回结果；
    ⑥　返回结果在ip层被截获、丢弃，由intercpetion拷贝返回结果的ip header返回；
    ⑦　ip header被发送给线上前端机的tcpcopy进程。

> 这段内容摘自[淘宝技术博客](http://www.searchtb.com/2012/05/using-tcpcopy-to-simulate-traffic.html)

### 安装和配置 ###
iptables -I OUTPUT -p tcp --sport 80 -j QUEUE

### 相关使用 ###


urls

http://cxw06023273.iteye.com/blog/866888
http://hi.baidu.com/yacker/item/e6bd5b287fe5a3f150fd8731
http://nano-chicken.blogspot.com/2010/05/netlink-introduction.html
http://daydreamer.idv.tw/rewrite.php/read-47.html

http://hi.baidu.com/zhumoqing/item/88fee21d921a2c0cd0d66d29

http://blog.csdn.net/wangbin579/article/details/6822015

http://www.searchtb.com/2012/05/using-tcpcopy-to-simulate-traffic.html

http://blog.csdn.net/dog250/article/details/5303430

reference：

[^1] http://www.searchtb.com/2012/05/using-tcpcopy-to-simulate-traffic.html

[^2] http://nano-chicken.blogspot.com/2010/05/netlink-introduction.html

[^3] http://cxw06023273.iteye.com/blog/866888

[^4] https://github.com/wangbin579/tcpcopy

[^5] http://hi.baidu.com/yacker/item/e6bd5b287fe5a3f150fd8731
