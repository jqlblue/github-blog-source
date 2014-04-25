blog [jqlblue](http://jqlblue.github.io)的源码。

## 搭建步骤

    yum install ruby.x86_64
    yum install ruby-devel.x86_64
    git clone git://github.com/imathis/octopress.git octopress
    cd octopress
    gem install bundler
    bundle install
    rake install
    rake setup_github_pages
    rake generate
    rake deploy

## 相关插件

- table样式
  http://programus.github.io/blog/2012/03/07/add-table-data-css-for-octopress/
- Table of Contents
  http://brizzled.clapper.org/blog/2012/02/04/generating-a-table-of-contents-in-octopress/

## 相关使用

rake generate # Generates posts and pages into the public directory
rake watch # Watches source/ and sass/ for changes and regenerates
rake preview # Watches, and mounts a webserver at http://localhost:4000

## octopress文档

Check out [Octopress.org](http://octopress.org/docs) for guides and documentation.
