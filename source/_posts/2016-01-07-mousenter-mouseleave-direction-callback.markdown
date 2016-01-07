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

这个对象暴露了一个run方法，有三个参数：wen_direction.run(id, enterObject, leaveObject)

- id : 元素的id
- enterObject：用于判断鼠标是从哪个方向进入元素后的对象，对象必须含有四个方向的回调方法，即如下

```javascript
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

```javascript
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

## 代码实战

**html**
  
```html
<div id="box"></div>
```
**css**

```css
#box {
    width: 100px;
    height: 100px;
    background: cadetblue;
}
```
**javascript**

```javascript
//采用模块模式来封装代码，可以方便发布成为spm包，方便今后引用。
    var wen_direction = (function () {
        function Direction(id) {
            this.id = document.getElementById(id);
        }
        Direction.prototype.init = function (enterObj, leaveObj) {
            //鼠标滑入元素
            var self = this;
            this.id.addEventListener('mouseenter', function (e) {
                var directionNumber = self.main(e); //返回数字  返回0:上方进入， 返回1:右方进入，返回2：下方进入，返回3：左方进入
                var funArray = [enterObj.top, enterObj.right, enterObj.bottom, enterObj.left];
                funArray[directionNumber](self);
            });
            this.id.addEventListener('mouseleave', function (e) {
                var directionNumber = self.main(e); //返回数字  返回0:上方离开， 返回1:右方离开，返回2：下方离开，返回3：左方离开
                var funArray = [leaveObj.top, leaveObj.right, leaveObj.bottom, leaveObj.left];
                funArray[directionNumber](self);
            });
        };
        /*主函数 返回数字来判断从哪个方向进入*/
        Direction.prototype.main = function (e) {
            var w = this.id.scrollWidth;
            var h = this.id.scrollHeight;
            var x = (e.clientX - this.id.offsetLeft - (w / 2)) * (w > h ? (h / w) : 1);
            var y = (e.clientY - this.id.offsetTop - (h / 2)) * (h > w ? (w / h) : 1);
            var number = Math.round((((Math.atan2(y, x) * (180 / Math.PI)) + 180) / 90) + 3) % 4;
            return number;
        };
        return {
            run: function (id, enterObj, leaveObj) {
                var directionChild = new Direction(id);
                directionChild.init(enterObj, leaveObj);
            }
        }
    })();
    //测试用例
    var enterObject = {
        left: function(self) {
            console.log("从左边进入");
        },
        right: function(self) {
            console.log("从右边进入");
        },
        top: function(self) {
            console.log("从上边进入");
        },
        bottom: function(self) {
            console.log("从下边进入");
        }
    };
    var leaveObject = {
        left: function(self) {
            console.log("从左边离开");
        },
        right: function(self) {
            console.log("从右边离开");
        },
        top: function(self) {
            console.log("从上边离开");
        },
        bottom: function(self) {
            console.log("从下边离开");
        }
    };
    wen_direction.run('box',enterObject, leaveObject);
```
>打开浏览器控制台既可以看到鼠标从哪个方向进入和离开元素后对应的相关操作

以上采用模块模式封装的wen_direction 我已经发布到我的spm上了 地址为：<http://spmjs.io/package/wen_direction>。

怎么发布spm包可以移步这里[构建spm瀑布流模块包](http://wenyang12.github.io/blog/2015/12/03/pubuliu-module-spm/) 有兴趣的可以模仿这个发布一下wen_direction这个我用模块模式封装好的代码

既然发布了，肯定就要涉及到使用这个包，如何使用呢？感兴趣的可以移步这里[Spm瀑布流模块包的使用](http://wenyang12.github.io/blog/2015/12/03/pubuliu-module-spm-use/)

我自己发布wen_direction模块包的源码已经放到我的github上了，地址为：<https://github.com/wenyang12/wen_direction>



