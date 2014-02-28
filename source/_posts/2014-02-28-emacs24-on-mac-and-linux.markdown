---
layout: post
title: "在mac或者linux上使用emacs24打造web开发环境"
description: "emacs24 emacs24.3 linux mac web develop environment"
date: 2014-02-28 14:16
comments: true
categories: [emacs]
---

### 安装emacs24.3 ###

##### Linux系统上安装 #####

可以通过如下地址下载到最新的emacs安装文件。
    http://www.gnu.org/software/emacs/
    
*安装步骤*
    wget http://mirror.bjtu.edu.cn/gnu/emacs/emacs-24.3.tar.gz
    tar zxvf emacs-24.3.tar.gz
    cd emacs-24.3
    ./autogen.sh
    ./configure --prefix=/usr/local/emacs24.3 \
    --with-x-toolkit=gtk \
    --enable-gcc-warnings \
    --enable-link-time-optimization \
    --enable-profiling \
    --with-x
    make bootstrap -j4
    make
    sudo make install
    
##### Mac系统上安装 #####

mac系统上，在安装了gcc和autoconf之后，也可以通过下载源代码编译的方式进行安装。不过mac有`Homebrew`，可以简化这一过程。

*安装步骤*
    ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"
    cd /usr/local/Library && git stash && git clean -d -f
    brew install emacs --cocoa

顺利的话，最新版的emacs就安装在mac了。如果中途遇到问题，按照提示解决下就好。

有可能下载地址被墙，这时通过通过如下方式解决：
    brew edit softname，如 brew edit emacs
    修改其中的url，保存退出
    
### 配置emacs ###

由于emacs24已经自带了包管理系统。只需几个简单的步骤，即可通过[Emacs Prelude](https://github.com/bbatsov/prelude)或者[goblin-emacs](https://github.com/jqlblue/goblin-emacs)体验emacs的魅力。步骤如下：

    cd /somepath/
    git clone https://github.com/jqlblue/goblin-emacs
    ln -s /somepath/goblin-emacs ~/.emacs.d

启动emacs后，会自动下载需要的扩展，完成后即可体验。

{% img /images/emacs/startup.png 'emacs start up %}
