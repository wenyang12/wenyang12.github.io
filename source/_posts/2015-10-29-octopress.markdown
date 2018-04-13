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
        - [Git](https://git-scm.com/downloads) 版本管理工具，将代码托管到GitHub 
        - Ruby+Devkit 生成静态网页 这两个文件我已放到百度网盘里，点击[下载](http://pan.baidu.com/s/1civpEI)
        - MarkdownPad windows下Markdown语法编辑器（可不用安装，只是为了方便查看或编辑markdown）
- 配置
    - 安装好ruby，检查是否安装成功可以运行 ```ruby -v```
    - Devkit直接解压缩放到你任意文件夹下即可，我放在了D:/根目录下，不用安装，然后在devkit目录下运行```ruby dk.rb init``` 和 ```ruby dk.rb install ``` 来增强ruby

PS： 安装ruby后，devkit的作用是为了增强ruby的功能。而安装ruby会自动安装了它自身的包管理器gem，即可使用gem命令

## 二、开始安装Octopress
- 克隆 Octopress 至本地  可以到[Octopress](http://octopress.org/)官网，也有介绍
```
git clone git://github.com/imathis/octopress.git octopress
```
- 安装依赖项bundler。 安装了bundler后即可采用bundle命令来安装项目里Gemfile里定义的所有依赖gem，为了不手动gem install [包名] 这样一个个手动安装
```
gem install bundler
```
- 安装Gemfile里定义的所有gem包

```
bundle install
```

要是安装有问题，可以再次更新一下所有依赖项，还是不行，可以到ruby的安装目录下手动删除对应安装失败的包，例如我的目录为：`C:\Ruby22\lib\ruby\gems\2.2.0`下的`gems`和`cache`文件夹下删除
```
bundle update
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

PS： 提示ruby SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: 这个说明ruby没有安装SSL证书，说要https的链接会被服务器拒绝。

> 解决方法：首先在这里下载证书[https://curl.haxx.se/ca/cacert.pem](https://curl.haxx.se/ca/cacert.pem),下载好后，然后再环境变量里设置SSL_CERT_FILE这个环境变量，并把value指向这个文件即可


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
输入以上命令后会出现配置，让你输入github上的对应的仓库链接，输入链接回车即可把本地的资源与GitHub绑定并通过git初始化一个_deploy文件出来，然后可以用```rake deploy```命令把本地生成的_deploy文件夹下的资源都部署到刚配置仓库的master分支上。

本地生成的_deploy下的文件其实是把```rake generate```命令生成到public的所有文件都复制过来而已。所有在使用 ```rake deploy``` 命令前应该先使用命令```rake generate``` 先生成public才行。

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

## 七、Gemfile文件的说明

### 设置Gem的版本
对于一个gem，你最常做的事情就是设置它的版本，如果你不设置版本的话，你也可以说任意的版本都可以。

`gem "my_gem", ">= 0.0"`
这里有7个操作符供你用来设置你的gem

   * = Equal To "=1.0"
   * != Not Equal To "!=1.0"
   * \> Greater Than ">1.0"
   * < Less Than "<1.0"
   * \>= Greater Than or Equal To ">=1.0"
   * <= Less Than or Equal To "<=1.0"
   * ~> Pessimistically Greater Than or Equal To "~>1.0"

**Pessimistically Greater Than or Equal To**

~> 操作能够让你使用这个gem的未来的某个安全的版本。如果你觉得使用一个大的版本更安全，你能够像下面这样声明.

`gem "my_gem", "~> 2.0"`

这能够允许你安装任意的2.x版本的gem，但是3.x版本是不被允许的。或许你对这么宽泛的版本感到不爽，你也可以声明一个更具体的版本，如下

`gem "my_gem", "~> 2.5.0"`

这能够让你使用2.5.0到2.6.0之间的版本。下面的例子能够让你更加理解~> 操作符

   * gem "my_gem", "~> 1.0" –> gem "my_gem", ">= 1.0", "< 2.0"
   * gem "my_gem", "~> 1.5.0" –> gem "my_gem", ">= 1.5.0", "< 1.6.0"
   * gem "my_gem", "~> 1.5.5" –> gem "my_gem", ">= 1.5.5", "< 1.6.0"


## 八、参考
[Gemfile 详解](http://blog.csdn.net/efvn2008/article/details/48392047)

## lfs里遇到push不到远程分支问题
如果提示batch response: Git credentials for https://github.com/wenyang12/wenyang12.github.io.git not found
执行

```
 git remote update
 git lfs install
 git lfs track <file>
 git add .
 git commit -m"add lfs file"
 git config --global credential.helper wincred
 git push --force origin master

```

