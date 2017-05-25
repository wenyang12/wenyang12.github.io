---
layout: post
title: "鼠标进入和离开元素4个方向对应的相关操作"
date: 2016-01-07 10:21:01 +0800
comments: true
categories: [javascript, 鼠标事件]
tags: [javascript, 鼠标事件]
---

## 介绍

这个模块[wen_direction](http://spmjs.io/package/wen_direction)用于判断鼠标是从哪个方向进入和离开元素后的相关回调操作

1、这个对象暴露的run方法，有三个参数：wen_direction.run(id, enterObject, leaveObject)

- id : 元素的id
- enterObject：用于判断鼠标是从哪个方向进入元素后的对象，对象必须含有四个方向的回调方法，即如下

```
enterObjext = {
    left：function(self){//self是对id这个元素的引用
        //这里是鼠标从左边进入元素后的回调
    }，
    right: function(self){
        //这里是鼠标从右边进入元素后的回调
    }，
    top: function(self){
        //这里是鼠标从上边进入元素后的回调
    }，
    bottoom: function(self){
        //这里是鼠标从下边进入元素后的回调
    }
}
```
<!--more-->

- leaveObject：用于判断鼠标是从哪个方向离开元素后的对象，对象必须含有四个方向的回调方法，即如下：

```
leaveObject = {
   left：function(self){//self是对id这个元素的引用
       //这里是鼠标从左边离开元素后的回调
   }，
   right: function(self){
       //这里是鼠标从右边离开元素后的回调
   }，
   top: function(self){
       //这里是鼠标从上边离开元素后的回调
   }，
   bottoom: function(self){
       //这里是鼠标从下边离开元素后的回调
   }
}
```
2、这个对象暴露了的jqRun方法，有2个参数：wen_direction.jqRun(id, e);这个方法是基于jQuery的

- id : 元素的jquery对象的引用
- e ：传入的query的事件对象

## 代码实战

**html**
  
```
<h2>run接口测试：</h2>
<div id="box"></div>
<h2>jqRun接口测试：</h2>
<div id="jqbox">
    <div id="client"></div>
    <div id="hidden"></div>
</div>
```
**css**

```
#box{
    width: 100px;
    height: 100px;
    background: #dddddd;
    margin: 100px;
}
#jqbox{
    position: relative;
    width: 100px;
    height: 100px;
    margin: 100px;
    overflow: hidden;
}
#client , #hidden{
    width:100%;
    height:100%;
    background: #dddddd;
    position: absolute;
}
#client{
    left:0;
    top:0;
}
#hidden{
    background: #6bc30d;
    left:-100%;
    top:-100%;
}
```
**javascript**

```
//采用模块模式来封装代码，可以方便发布成为spm包，方便今后引用。
var wen_direction = (function () {
    var jqDirection;
    function Direction(id) {
        this.id = document.getElementById(id) || id ;
    }
    Direction.prototype.init = function (enterObj, leaveObj) {
        //鼠标滑入元素
        var self = this;
        this.id.addEventListener('mouseenter', function (e) {
            var directionNumber = self.main(e); //返回数字  返回0:上方进入， 返回1:右方进入，返回2：下方进入，返回3：左方进入
            var funArray = [enterObj.top, enterObj.right, enterObj.bottom, enterObj.left];
            funArray[directionNumber](self.id);
        },false);
        this.id.addEventListener('mouseleave', function (e) {
            var directionNumber = self.main(e); //返回数字  返回0:上方离开， 返回1:右方离开，返回2：下方离开，返回3：左方离开
            var funArray = [leaveObj.top, leaveObj.right, leaveObj.bottom, leaveObj.left];
            funArray[directionNumber](self.id);
        },false);
    };
    /*主函数 返回数字来判断从哪个方向进入*/
    Direction.prototype.main = function (e) {
        var w = this.id.scrollWidth;
        var h = this.id.scrollHeight;
        var x = (e.offsetX - (w / 2)) * (w > h ? (h / w) : 1);
        var y = (e.offsetY - (h / 2)) * (h > w ? (w / h) : 1);
        var number = Math.round((((Math.atan2(y, x) * (180 / Math.PI)) + 180) / 90) + 3) % 4;
        return number;
    };
    /*基于jquery的事件对象*/
    Direction.prototype.jqRun=function(e) {
        var directionNumber = this.jqMain(e);
        var obj = {};
        switch(directionNumber)
        {
            case 0://from top
                obj.left = 0;
                obj.top = "-100%";
                break;
            case 1://from right
                obj.left = "100%";
                obj.top = 0;
                break;
            case 2://from bottom
                obj.left = 0;
                obj.top = "100%";
                break;
            case 3://from left
                obj.left = "-100%";
                obj.top = 0;
                break;
        }
        return obj;
    };
    Direction.prototype.jqMain = function (e) {
        var w = this.id.width();
        var h = this.id.height();
        /*计算x和y得到一个角到elem的中心，得到相对于x和y值到div的中心*/
        var x = (e.pageX - this.id.offset().left - (w / 2)) * (w > h ? (h / w) : 1);
        var y = (e.pageY - this.id.offset().top - (h / 2)) * (h > w ? (w / h) : 1);
        /** 鼠标从哪里来 / 角度 和 方向出去顺时针（得出的结果是TRBL 0 1 2 3
         * 首先计算点的角度，
         * 再加上180度摆脱负值
         * 除于90得到的象限（象限，又称象限角，意思就是一个圆之四分之一等份）
         * 加上3，做一个模（求模 求余数）4的象限转移到一个适当的顺时针 得出 TRBL 0 1 2 3（上/右/下/左）
         **/
        var number = Math.round((((Math.atan2(y, x) * (180 / Math.PI)) + 180) / 90) + 3) % 4;
        return number;
    };
    return {
        run: function (id, enterObj, leaveObj) {//这个接口用于原生js
            var directionChild = new Direction(id);
            directionChild.init(enterObj, leaveObj);
        },
        jqRun: function(id, e){//暴露的这个接口是基于jquery的
            if(!jqDirection){
                jqDirection = new Direction(id);
            }
            return jqDirection.jqRun(e);//返回一个样式对象{left：string，top：string}
        }
    }
})();
//测试用例
var enterObject = {
    left: function(self) {
        self.innerHTML = "从左边进入";
        console.log("从左边进入");
    },
    right: function(self) {
        self.innerHTML = "从右边进入";
        console.log("从右边进入");
    },
    top: function(self) {
        self.innerHTML = "从上边进入";
        console.log("从上边进入");
    },
    bottom: function(self) {
        self.innerHTML = "从下边进入";
        console.log("从下边进入");
    }
};
var leaveObject = {
    left: function(self) {
        self.innerHTML = "从左边离开";
        console.log("从左边离开");
    },
    right: function(self) {
        self.innerHTML = "从右边离开";
        console.log("从右边离开");
    },
    top: function(self) {
        self.innerHTML = "从上边离开";
        console.log("从上边离开");
    },
    bottom: function(self) {
        self.innerHTML = "从下边离开";
        console.log("从下边离开");
    }
};
//run接口
wen_direction.run('box',enterObject, leaveObject);
//jqRun接口
var jqbox = $("#jqbox");
var hidden = jqbox.children("#hidden");
jqbox.hover(function(e) {
    hidden.css(wen_direction.jqRun(jqbox,e)).stop(true,true).animate({left: '0', top: '0'},200);
},function(e) {
    hidden.stop(true, true).animate(wen_direction.jqRun(jqbox,e), 200);
});
```
代码的效果可以移步这里<http://docs.spmjs.io/wen_direction/latest/examples/index.html>

以上采用模块模式封装的wen_direction 我已经发布到我的spm上了 地址为：<http://spmjs.io/package/wen_direction>。

怎么发布spm包可以移步这里[构建spm瀑布流模块包](http://wenyang12.github.io/blog/2015/12/03/pubuliu-module-spm/) 有兴趣的可以模仿这个发布一下wen_direction这个我用模块模式封装好的代码

既然发布了，肯定就要涉及到使用这个包，如何使用呢？感兴趣的可以移步这里[Spm瀑布流模块包的使用](http://wenyang12.github.io/blog/2015/12/03/pubuliu-module-spm-use/)

我自己发布wen_direction模块包的源码已经放到我的github上了，地址为：<https://github.com/wenyang12/wen_direction>



