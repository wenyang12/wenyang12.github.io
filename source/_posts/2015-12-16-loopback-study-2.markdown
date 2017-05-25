---
layout: post
title: "Node.js框架lookback学习二"
date: 2015-12-16 15:51:35 +0800
categories: [Node.js框架, loopback, mongodb]
tags: [Node.js框架, loopback, mongodb]
---

## 绑定github账号登陆

**准备**

首先进入自己github的这个目录<https://github.com/settings/developers>

然后注册一个新应用，如下图所示

![注册新应用图]({{ root_url }}/images/loopback-pic-4.png)

第三个应用描述可以不填

注册好后会生成一个"clientID"和 "clientSecret";（用来配置providers.json的）

**开始**

首先下载测试源码到本地
>git clone https://github.com/strongloop/loopback-example-passport

<!--more-->

安装依赖
>$ npm install

>$ npm install passport-github --save

配置providers.json(我们把providers.json.template这个文件复制一份出来，然后去掉后缀template，然后在这个基础上配置)

```
{
  "local": {
    "provider": "local",
    "module": "passport-local",
    "usernameField": "username",
    "passwordField": "password",
    "authPath": "/auth/local",
    "successRedirect": "/auth/account",
    "failureRedirect": "/local",
    "failureFlash": true
  },
  "github-login": {
    "provider": "github",
    "module": "passport-github",
    "clientID": "这里对应填入刚刚在github注册应用时生成的",
    "clientSecret": "这里对应填入刚刚在github注册应用时生成的",
    "callbackURL": "/auth/github/callback",
    "authPath": "/auth/github",
    "callbackPath": "/auth/github/callback",
    "successRedirect": "/auth/account",
    "failureRedirect": "/login",
    "scope": ["email"],
    "failureFlash": true
  }
}
```
写入一个github测试的登陆入口，打开./loopback-example-passport/server/views/pages/login.jade 这个文件，添加一行li，如：

```
block content
  .row
    .col-md-12
      h1 Login:
      ul.list-inline.list-unstyled
        li
          a.btn.btn-primary(href='/local') Login with local account
        li
          a.btn.btn-primary(href="/auth/facebook") Login with Facebook
        li
          a.btn.btn-primary(href="/auth/google") Login with Google
        li
          a.btn.btn-primary(href="/auth/twitter") Login with Twitter
        li
          a.btn.btn-primary(href="/auth/github") Login with github
      br
      if (messages.error)
        p
          span(style="color:red")= messages.error
```

这样就大功告成了，启动应用来测试吧

>node .

打开http://localhost:3000/login 点击`Login with github` 按钮登录。



