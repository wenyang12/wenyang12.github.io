---
layout: post
title: "把wordpress里的文章迁移到octopress中"
date: 2015-11-23 10:20:01 +0800
comments: true
categories: [wordpress, octopress]
tags: [wordpress, octopress]
---

## 从wordpress里导出所有文章,导出的格式为.xml后缀
- wordpress里进入自己的后台，进入`./wp-admin/export.php`这个目录下导出自己的所有文章
- 然后把导出的文件复制到octopress根目录下重命名为`wordpress.xml`
- 然后需要下载一个把xml文件编译成markdown文件并放到你./source/_post/这个目录下的ruby文件，[下载地址](https://github.com/wenyang12/wenyang12.github.io/blob/source/wordpress.rb);
- 最后把下载好的这个wordpress.rb文件也放到你的octopress根目录下

## 执行命令即可完成迁移
执行命令前，要安装编译用到的依赖包open_gem、[jekyll(一款非常出色的静态博客网站生成工具，它是用Ruby开发而成的)](http://jekyll.bootcss.com/) 、[sequel(用于 Ruby 编程语言的一个简单灵活强大的数据库访问工具包，提供了线程安全、连接池以及 DSL 语言用来构造查询和表模型)](https://github.com/jeremyevans/sequel) 、[hpricot(一个HTML解释的库)](https://github.com/hpricot/hpricot)即4个依赖
>gem install open_gem jekyll sequel hpricot

然后接着输入以下命令即可
>ruby -r "./wordpress.rb" -e "Jekyll::WordpressDotCom.process"

完成以上命令后，你会发现你的`./source/_post/`目录下有了wordpress的所有文章，而且都是`.markdown`后缀的,然后在这个基础上，修改里边的内容让其符合markdown语法的文章即可大功告成
##参考质料
><http://justcoding.iteye.com/blog/1954653>

><http://blog.dayanjia.com/2012/04/migration-to-octopress-from-wordpress/>
