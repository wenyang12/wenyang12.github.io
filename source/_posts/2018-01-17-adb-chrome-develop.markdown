---
layout: post
title: "webview的调试方法"
date: 2018-01-17 11:23:19 +0800
comments: true
categories: [adb,调试webview]
tags: [adb,调试webview]
---

## 采用fiddler代理调试
下载fiddler，可以移步[这里](https://www.telerik.com/download/fiddler)

下载安装好后打开tools下的hosts如图

![fiddler操作图示]({{ root_url }}/images/fiddler_1.png)

接着，就可以在hosts输入自己的配置，跟windows本机的hosts配置一样，设置好后点击保存即可，如图

![fiddler操作图示]({{ root_url }}/images/fiddler_2.png)

我这个配置是，127.0.0.1:3000（我这个项目是vue项目`npm run dev`开启的） 这个代表我本地开发开启的node服务地址，
后边的www.demo1.com（随便取，一般会取跟联调环境一样的域名）代表手机要访问的地址
假如通过手机访问这个网址，会通过fiddler代理到我本地开启的127.0.0.1:3000这个地址（而这个地址指向的即是我通过vue开启的）即可在手机端进行调试本地开发代码，本地代码更新，也会在手机上实时体现。

上边提到用手机访问www.demo1.com，就会被fiddler代理到127.0.0.1:3000，手机是如何配置的呢？

首先打开手机的wifi，这个wifi必须连的跟电脑连的是一个局域网。进入wifi设置，有个代理设置，如下图：

![手机操作图示]({{ root_url }}/images/fiddler_3.png)

其中服务器填的ip就是自己电脑的ip，自己不知道可以打开cmd，输入命令ipconfig，即可查看，而端口号，就是fiddler里设置的端口，打开tools下的options即可设置，如下图

![fiddler操作图示]({{ root_url }}/images/fiddler_4.png)
 
完成了以上步骤即可通过fiddler代理手机访问的地址了。手机访问的所有请求，也会被fiddler抓包，假如我在手机访问百度，抓包如图（可以查看请求头，和返回头等信息等等）

![fiddler操作图示]({{ root_url }}/images/fiddler_5.png)

代理不仅可以本地调试，假如你的代码已经发布到测试环境，域名为www.demo2.com 那么你可以通过配置fiddler的hosts，即可利用测试环境来调试本地代码。

所以以后就不用想上测试验证，要把代码部署到测试环境才能验证了，直接本地就可以验证，验证好后，在发测试即可。

此外还可以通过adb连接手机，通过chrome开发者工具调试，即通过 chrome://inspect 访问已启用调试的 WebView 列表，详情可以移步[远程调试 WebView](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/webviews?hl=zh-cn)

