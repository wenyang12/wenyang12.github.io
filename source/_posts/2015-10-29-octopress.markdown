---
layout: post
title: "octopress 搭建博客静态网站"
date: 2015-10-29 15:41:51 +0800
comments: true
categories: [octopress, markdown]
tags: [octopress, markdown]
---
## 一、环境配置
- 安装以下软件
    - Git、Ruby、Devkit、MarkdownPad 的安装与配置
        - Git 版本管理工具，将代码托管到GitHub
        - Ruby+Devkit 生成静态网页
        - MarkdownPad windows下Markdown语法编辑器

## 二、开始安装Octopress
- 克隆 Octopress 至本地  可以到[Octopress](http://octopress.org/)官网，也有介绍
```
git clone git://github.com/imathis/octopress.git octopress
```
- 安装依赖项
```
gem install bundler
```
- 安装并使用默认主题
```
rake install
```
- 生成静态页面到public下
```
rake generate
```
- 启动服务器
```
rake preview
```
- 最后在浏览器中预览，输入地址为 localhost:4000 即可看到博客


## 三、生成博客与单页面
- 新建博客
```
rake new_post['title']
```
- 新建单页面（有两种一些形式）
```
rake new_page[pagename]
```
>生成的目录结构 source/pagename/index.markdoown
```
rake new_page[pagename/page.html]
```
>生成的目录结构 source/pagename/page.html

- 使用markdown语法书写博客
    - 工具
        - MarkdownPad 、记事本、Vim Emacs 等都可以

## 四、部署到github上
- 新建仓库
>username.github.io
>必须要以这种格式新建仓库，username即是你在github上注册的用户名

- 与本地Octopress目录绑定
```
rake setup_github_pages
```
>让github与octopress目录绑定

```
rake deploy
```
>把本地代码部署到github上

## 五、博客的自定义配置
- 完善主配置文件
    - 在_config.yml文件中，配置url、title、author等基本信息
- 安装主题
    - 把主题文件复制到.themes下，然后通过命令
    ```
    rake install['主题名字']
    ```
    >注意：安装主题的时候要备份之前的所有文件，因为安装新主题会覆盖掉之前的所有配置。

## 六、自定义网站域名
- 在github上绑定自定义域名
    - 创建 source/CNAME 文件并指定域名
    ```
    echo 'domain.com' >> source/CNAME
    ```
    OR
    ```
    echo 'www.domain.com' >> source/CNAME
    ```
- 解析域名至github上
    - 使用子域名
    >对于子域名（www.domain.com）,创建CNAME记录指向 charlie.github.io

    - 使用顶级域名
    >对于顶级域名（domain.com），使用A记录指向 192.30.252.153（154）


