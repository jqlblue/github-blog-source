---
layout: post
title: "analysis android applications performance"
description: "android app profile tools performance 安卓 应用 渲染 性能 分析 工具"
date: 2013-11-22 18:03
comments: true
categories: [mobile]
---
如果一个安卓应用打开时比较慢，或者使用起来比较卡。这个可能是客户端代码有待优化，也可能是服务端性能比较挫。对一个客户端开发者而言，在客户端代码中增加相关debug日志，即可比较准确地定位问题。但这活要落到一个服务端开发人员手里，要怎么办？

本文将在没有apk源码的情况下，以服务端开发人员的视角进行客户端app性能的分析。

在分析之前，我们先补充点android基础知识。
### android基础知识 ###

##### 1.android平台架构 #####
{% img /images/mobile/android_architecture.png 'android architecture images' %}

android操作系统在软件层面上主要分为下面四个部分：

    Applications - 默认的安卓应用，如浏览器，照相机等

    Application framework - 为android应用提供了一些允许与android系统交互的API

    Libraries and runtime - 供Application Framework调用的类库，以及android运行时环境（包括运行android应用的Dalvik虚拟机以及核心的java类库）

    Linux kernel - 这层用于与设备硬件通讯
##### 2.什么是android #####
android是一个为移动设备（手机，平板，智能手表）开发的操作系统，它基于linux操作系统，并开放源代码。使用linux内核与设备的硬件进行交互。
Android is an operating system used to develop mobile operating system.  
android is an open source operating system developed on linux.  android uses the kernel of linux to to access the hardware.



at present android support java language for programming. later on there may be a chance of android programming in c or c++.


to start development you need to use eclipse as editor and download the android sdk from android official website.  although you can use other editor it will be easy to configure and develop application in eclipse.
before starting development you must have knowledge of java programming language.
##### 3.什么是apk #####
使用java
http://www.oschina.net/question/923224_85777
apk已经是编译好的class以及其他资源的压缩包了

adb install 只是把apk上传到设备/模拟器, 并安装.

把apk用winrar解压软件解压, class.dex就是所有的类文件, 不过是加密的.
##### 4.Android程序执行流程图 #####
http://www.cnblogs.com/taobox/articles/3405931.html
说明安卓app是什么？
* android执行流程

### apk启动速度 ###

### 页面渲染性能 ###
### 过渡绘制 ###
### 使用systrace进一步分析 ###
### 使用tcpdump分析接口性能 ###

reference：
http://www.vogella.com/articles/AndroidTools/article.html 
