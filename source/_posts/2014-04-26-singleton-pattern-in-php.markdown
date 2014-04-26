---
layout: post
title: "php中的单例模式面面观"
description: "php语言中单例设计模式的几种使用场景，静态局部变量，静态成员变量"
keywords: "php 设计模式 pattern 单例 singleton 使用场景"
date: 2014-04-26 20:47
comments: true
categories: [php]
tags: [php, pattren]
toc: true
---
urls
https://www.ibm.com/developerworks/library/os-php-designptrns/
http://www.phptherightway.com/pages/Design-Patterns.html
http://blog.csdn.net/jungsagacity/article/details/7618587
http://www.whatsnoodle.com/creating-a-singleton-class-in-php/

<!-- more -->
vld
http://derickrethans.nl/more-source-analysis-with-vld.html
http://rancoud.com/read-phps-opcode/
http://graphviz.org/
http://www.phppan.com/2011/05/vld-extension/
http://www.php.net/manual/en/internals2.opcodes.is-equal.php
http://blog.csdn.net/iefreer/article/details/8927045
http://www.php-internals.com/book/?p=chapt03/03-04-static-var

# 何为单例模式 #
作为对象的创建模式，单例模式确保某一个类只有一个实例，而且自行实例化并向整个系统全局地提供这个实例。它不会创建实例副本，而是会向单例类内部存储的实例返回一个引用。
# 示例代码 #
xxxx
## 使用静态局部变量实现单例 ##
{% codeblock lang:php %}
<?php
abstract class Singleton
{
    public static function get()
    {
        static $instance = null;
        if ($instance == null) {
            echo "1\n";
            $instance = new StdClass();
        }
        return $instance;
    }
}

class Foo Extends Singleton
{
    public static function go()
    {
        var_dump(self::get());
    }
}
class Bar Extends Singleton
{
    public static function go()
    {
        var_dump(self::get());
    }
}
Foo::go();
Bar::go();
{% endcodeblock %}
## 使用静态成员变量实现单例 ##
{% codeblock lang:php %}
<?php
abstract class Singleton
{
    public static function get()
    {
        static $instance = null;
        if ($instance == null) {
            echo "1\n";
            $instance = new StdClass();
        }
        return $instance;
    }
}

class Foo Extends Singleton
{
    public static function go()
    {
        var_dump(self::get());
    }
}
class Bar Extends Singleton
{
    public static function go()
    {
        var_dump(self::get());
    }
}
Foo::go();
Bar::go();
{% endcodeblock %}
# 编写一个单例的基类 #
如果我们的项目中有个缓存类，还有数据库类，还有个文件操作类。想让这三个类都是单例的话，我们需要实现三个单例。
所以我们可以创建一个单例的基类，要实现单例的类继承这个基类即可。代码如下

可以使用 静态延迟绑定
http://www.songlin51.com/archives/766.html
http://www.nowamagic.net/librarys/veda/detail/382
{% codeblock lang:php %}
<?php
abstract class baccarat_singleton
{
    protected function __construct()
    {
    }

    final public static function getInstance()
    {
        static $aoInstance = array();
        $calledClassName = get_called_class();
        if(!isset($aoInstance[$calledClassName]))
        {
            $aoInstance[$calledClassName] = new $calledClassName();
        }
        return $aoInstance[$calledClassName];
    }
    public function __isset($name)
    {
        return isset($this->$name);
    }
    public function __get($name)
    {
        if(isset($this->$name))
        {
            return $this->$name;
        }
    }
    public function __set($name, $value)
    {
        $this->$name = $value;
    }
    public function __set_state($array)
    {
    }
    public function __call($funName, $args)
    {
        if(!isset($this->$funName))
        {
            throw new Exception($funName . '方法不存在');
            return;
        }
    }
}
{% endcodeblock %}
