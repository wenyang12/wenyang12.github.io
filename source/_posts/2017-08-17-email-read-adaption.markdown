---
layout: post
title: "邮件读信页的自适应方案"
date: 2017-08-17 14:25:10 +0800
comments: true
categories: [自适应, email]
tags: [自适应, email]
---

## 背景介绍

邮件的读信页，要分享到别的地方，别的地方可以通过一个连接即可打开查看。
那么问题了来了，在终端或者pc端，查看内容的时候，因为设备不一样，屏幕尺寸也都不同，那就会碰到，有些邮件内容看起来就很难看，因为邮件内容都是第三方发送过来的，都没有做自适应适配，只能是读信这边来做自适应，让它展示起来效果都高保真发件者发过来的邮件即可。此外我们会经常收到一些推广的邮件，里边的邮件就像一个小页面一样展示，这种邮件一般有个名字叫emd邮件，即“电子邮件营销”，很多都不符合标准，会带上自己的一些样式规则，而css样式不是用符合edm规范的的内联样式，这就导致了，读信页里的内容样式，影响到展示它的网页样式，通常处理这种印象，有些会把style标签过滤掉，不过，一般成熟的邮件方案是使用iframe方案插入邮件，让他自己有自己的一个空间，就可以解决影响到外部样式问题，而也高保真了发送过来的邮件展示问题。

## edm是什么？

上边提到的edm邮件营销，即可以理解为，通过邮件方式，插入自己的网页源代码，然后发送给对方，对方查看邮件的时候，就会看到邮件内容跟一张网页一样，可以浏览，也可以点击调转，从而起到营销引流的作用，把流量导入自己的真实网站中，最常见的就是购物网站的edm邮件了。你去某网站购物，注册了，然后留了自己的邮箱地址，购物网站会根据你平时浏览过的，或购买过的，进行精准的邮件edm营销，定期发送一封邮件给你。
emd邮件如图所示：


## iframe方案

邮件的展示，我们采用iframe来展示从后台返回的邮件内容，里边的样式就不会影响到引入它的这个外部window窗口的样式。

```
    <iframe id="email_iframe"></iframe>
    <script>
        let email = {
            // response 从后台获取到的邮件内容
            _renderIframe: function(response) {
                let iframeNode = document.getElementById('email_iframe');
                iframeNode.contentDocument.body.innerHTML = response;
            }
            ...
        }
    </script>
```

给iframe插入了内容后，问题又来了，它的高度是不会自适应插入的内容的高度的，它默认会出现滚动条。这不是我们想要的，我们想要的页面看起来，就跟不是插入iframe一样，看起来就相当于在一个页面上的内容，所有必须采用高度自适应方案来做高度适应。

### 首先获取iframe内容的页面高度

    ```
    let email = {
        ...
        //获取iframe内容的页面高度
        getIframeHeight: function(){
            let iframeBody = iframeNode.contentDocument.body,
                iframeHeight;
            iframeBody.style.position = 'absolute';
            iframeHeight = iframeBody.offsetHeight; //包含padding+border的高度
        }
        ...
    }
    ```

    获取高度需要注意，这里必须要先设置body为绝对定位或相对定位，目的是为了让内容脱离文档流，才能获取到真实的内容高度，不受外围设置了高度为100%的影响。

    问题又来了，当你获取iframe页面高度时，里边要是包含有图片，那么你获取到的高度会少了图片的高度，因为你获取的时候图片并没有完全加载。所有应该等全部图片加载完成后，在取获取高度即可。

### 怎么知道iframe内容里的所有图片是否加载完了呢？这里用到了图片元素的onload事件和onerror事件，和根据属性complete来判断

    ```
    let email = {
        ...
        /**
         * 图片加载完成回调
         * @param imgs           传入的所有图片元素数组
         * @param callback fn    所有图片加载完成后的回调
         */
        _imgLoadeAll:function(imgs, callback) {
            let len = imgs && imgs.length || 0; 
            //转为真实的数组
            if(!len){return;}
            imgs =  Array.prototype.slice.call(imgs);
            imgs.forEach(function(item){
                if(item.complete){ //为true代表图片加载完成了，一般一开始这个就为true代表图片有缓存，所有立马加载完了。
                    len--;
                    len || callback && callback(); //当len为0 即加载完成
                }
                item.onload = function(){
                    len--;
                    if(len > 0) {return;}
                    callback && callback();
                }
                item.onerror = function(){
                    len--;
                    if(len > 0) {return;}
                    callback && callback();
                }
            })
        }
        ...
    }
    ```
    到了这步，我们基本上做到了iframe高度自适应了,接下来就是要处理设备不一样，或宽度不一样的iframe内容展示适应了。

### iframe 内容宽度自适应。

这里，需要先设置iframe的宽度为100%，然后在设置iframebody里的所有子元素的宽度为100%，来做到，放大或缩小屏幕，都会只适应宽度，里边的元素，我们会真的一般edm邮件用到的表格元素来进行对应的样式设置来实现只适应效果。直接上代码吧：

```
    let email = {
        ...
        /**
         * 页面自适应
         * @param iframeBody                iframe body元素
         * @param iframeBodyWidth number    获取的iframe的内容宽度
         */
        _pageAdaption: function (iframeBody, iframeBodyWidth) {
            var tableNodes = iframeBody.getElementsByTagName('table'),
                tdNodes = iframeBody.getElementsByTagName('td'),
                divNodes = iframeBody.getElementsByTagName('div'),
                thNodes = iframeBody.getElementsByTagName('th'),
                childrenNodes = body.childNodes;
            this.css(childrenNodes, {
                width: "100%",
                maxWidth: iframeBodyWidth + 'px',
                margin: "0 auto"
            });
            this._css(tableNodes, {
                width: "100%",
                maxWidth: iframeBodyWidth + 'px',
                margin: "0 auto"
            });
            this._css(tdNodes, {
                whiteSpace: "normal",
                height: "auto"
            });
            this._css(thNodes, {
                whiteSpace: "normal"
            });
            this._css(divNodes, {
                whiteSpace: "normal",
                width: "auto"
            })
        },
        /**
         * 设置样式
         * @param nodes             元素
         * @param options object    样式对象
         */
        _css: function(nodes, options){
            var slice = Array.prototype.slice;
            nodes = slice.call(nodes);
            nodes.forEach(function(item){
                if(item.nodeType == 1 && item.innerHTML !== ''){
                    for(var key in options){
                        item.style[key] = options[key];
                    }
                }
            });
        }
        ...
    }
```

### 终端的自适应

需要设置meta name为viewport 里的值。

```
    let email = {
        ...
        /**
         * 判断是否是什么设备
         * @retrun object
         */
        _device: function() {
            let device = {
                ios: false,
                android: false,
                pc: false,
                version: 0
            }
            let android, ios;
            let ua = window.navigator.userAgent;
            if ((android = ua.match(/(Android);?[\s\/]+([\d.]+)?/))) {
                device.android = true;
                device.version = android[2];
                return device;
            }
            if ((ios = ua.match(/(iPhone|iPod|iPad).*OS\s([\d_]+)/))) {
                device.ios = true;
                device.version = ios[2].replace(/_/g, '.');
                return device;
            }
            device.pc = true;
            return device;         
        },

        /**
         * 获取iframe内容的宽高
         * @return object
         */
        _getIframeWidthAndHeight: function () {
            var iframeBody = this.iframe.contentDocument.body;
            return {
                width: iframeBody.offsetWidth,
                height: iframeBody.offsetHeight
            }
        },

        /**
         * 手机自适应
         * 
         */
        _mobileAdaption:function() {
            if(this._device.pc){renture;}
            var me = this,
                iframeWidthAndHeight= me.getIframeWidthAndHeight(),
                scale = 1,
                head = document.getElementsByName('viewport')[0],
                pageWidth = document.body.offsetWidth;
            // 当实际的iframe内容宽度，大于父window页面的宽度的时候，即装不下了，通过设置meta来按比例缩小页面，来达到显示全部的效果。
            if (iframeWidthAndHeight.width > pageWidth) {
                scale = (pageWidth/iframeWidthAndHeight.width).toFixed(2); 
                head.content = `width=device-width, user-scalable=no, initial-scale=${scale}, maximum-scale=${scale}, minimum-scale=${scale}`;
            }
        }
        ...
    }
```

为什么要针对终端来设置meta来按比例缩放呢，主要是考虑到edm邮件的整体效果，要是采用跟pc端类似的自适应方案，那就不是高保真了，看到的是只适应的换行效果，终端屏幕又那么小，就做不到高保真，看到用户所发过来的真是页面效果。

查看[整体源码](https://github.com/wenyang12/email-read-adaption.git)





    






