---
layout: post
title: "构建spm瀑布流模块包"
date: 2015-12-03 14:17:50 +0800
comments: true
categories: [spm, 瀑布流, 模块包]
tags: [spm, 瀑布流, 模块包]
---
## 前言
构建自己的一个spm瀑布流模块包以方便自己在图片运用到瀑布流效果的时候，自己可以在spm环境下引入代码，方便js编写，方便模块管理。
构建好之后可以发布到<http://spmjs.io/>,下次想要用直接输入
>$ spm install <包文件名> --save

>即 spm install moving-water --save

即可给自己项目添加了一个包，然后接着，在自己js里边引用

>var movingWater = require('<包文件名>')

>即 var movingWater = require('moving-water');
 // use movingWater

## 构建包开始
<!--more-->
### 安装spm
>$ npm install -g spm

### 建一个文件夹(即包名)
>$ mkdir  moving-water

### 初始化spm环境
>$ npm init

### 编辑index.js

```javascript
var movingWater;
movingWater =(function() {
    /*单例模式*/
    var addEvent=(function() {//返回兼容旧浏览器的事件监听
        var instance;
        function init() {
            function temp(target,event,fn) {
                if(window.addEventListener){
                    return target.addEventListener(event, fn);
                }else if(window.attachEvent) {
                    return target.attachEvent('on'+event, fn);
                }
            }
            return temp
        }
        return{
            getInstance: function() {
                if(!instance){
                    instance = init();
                }
                return instance;
            }
        }
    }());
    function init(parent,child,callback){
        var event = addEvent.getInstance();
        event(window,'load',function() {
            var parentEle = document.querySelector(parent);
            var childEle = document.querySelectorAll(child);
            //设置布局宽度
            var docWidth = document.documentElement.offsetWidth || document.body.offsetWidth; //获取可视文档的宽度
            var imgBoxWidth = childEle[0].offsetWidth;//获取图片盒子的宽度
            var cols = Math.floor(docWidth / imgBoxWidth);//一行能放几张图片的个数
            parentEle.style.width = cols * imgBoxWidth + 'px';//设置布局宽度
            main(cols, child);
            scrollAjax(cols, child, parentEle, callback);
        });
    }
    //核心函数
    function main(cols,child) {
        //设置除了第一行图片，往后的图片的布局，计算出第一行图片当中的最小高度，然后把往后的图片用绝对定位摆放在其下边，依次类推。
        var imgHeightArr =[],imgMinHeight, imgMinHeightIndex;
        childEle = document.querySelectorAll(child);
        for(var i = 0,len = childEle.length; i < len; i++) {
            if(i < cols) {
                imgHeightArr[i]  = childEle[i].offsetHeight;
            }else{
                imgMinHeight = Math.min.apply(null, imgHeightArr); //获取第一行图片的最小高度
                imgMinHeightIndex = getImgMinIndex(imgHeightArr, imgMinHeight);//获取第一行图片最小高度对应的位置，即索引值
                //设置接下来除了第一行图片的所有图片布局
                childEle[i].style.position = 'absolute';
                childEle[i].style.top = imgMinHeight + 'px';
                childEle[i].style.left = childEle[imgMinHeightIndex].offsetLeft+'px';
                imgHeightArr[imgMinHeightIndex] = imgMinHeight + childEle[i].offsetHeight;//重新设置最小高度
            }
        }
    }
    //获取第一行图片最小高度对应的位置，即索引值
    function getImgMinIndex(imgHeightArr, imgMinHeight) {
        for(var i in imgHeightArr) {
            if(imgHeightArr[i] === imgMinHeight) {
                return i;
            }
        }
    }
    function scrollAjax(cols,child,parent,callback) {
        var event = addEvent.getInstance();
        event(window,'scroll',function() {
            if(scrollFlag(child)){
                callback(parent);
                main(cols,child);
            }
        })
    }
    //设置滚动条滚动到什么位置，加载图片
    function scrollFlag(child) {
        childEle = document.querySelectorAll(child);
        var lastImgOffsetTop = childEle[childEle.length - 1].offsetTop;
        var lastImgHeight = childEle[childEle.length - 1].offsetHeight;
        var scrollTop = document.documentElement.scrollTop || document.body.scrollTop;//获取滚动条向下滚滚去的高度
        var docHeight = document.documentElement.clientHeight || document.body.clientHeight;//获取文档可视区域的高度
        console.log(lastImgOffsetTop, scrollTop, docHeight,lastImgHeight);
        if(lastImgOffsetTop < scrollTop+docHeight-lastImgHeight) {//滚动条拉动显示最后一张图片底部时加载
            return true;
        }else{
            return false;
        }
    }
    return {
        init:init
    }
}());
module.exports = movingWater;
```
### 编辑`./examples/index.md`(即在这里编辑写入这个包的demo，这里有一个值得注意的就是它会自动解析里边用md编辑的代码，可以看到测试用例demo的效果)

### 编辑根目录下边的`./README.md` 来教大家如何使用你的包。

### 最后本地调试
执行 spm doc 开启一个文档服务 127.0.0.1:8000 ：

>$ spm doc

然后在浏览器里打开 http://127.0.0.1:8000/examples/ 即可看到结果。[这里](http://docs.spmjs.io/moving-water/latest/)是我的

### 发布

> $ spm login //username 是你的 github 账号，而 authkey 可以在你登陆后在 <http://spmjs.io/account> 找到。

>spm publish

**发布文档**

>$ spm doc publish

## 发布好后即可使用了
在我的[github](https://github.com/wenyang12/bupuliu/tree/v1)上有瀑布流的测试用例

## 参考质料

[spm组件入门](https://github.com/spmjs/docs/blob/master/zh-cn/package/get-started.md)
