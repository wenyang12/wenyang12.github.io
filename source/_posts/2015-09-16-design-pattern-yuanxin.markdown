---
layout: post
title: "设计模式--原型模式"
date: 2015-09-16 10:20:18 +0800
categories: [设计模式, 原型模式]
tags: [设计模式, 原型模式]
comments: true
---

## 概念解读

文字解读
>原型模式是指原型实例指向创建对象的种类，并且通过拷贝这些原型创建新的对象。
对于原型模式，可以利用javascript特有的原型继承特性去创建对象的方式，真正的原型继承是作为最新版的ECMAScript5标准提出的，使用 Object.creat方法来创建这样的对象，如 Object.creat(prototype,optionalDescriptorObjects).

## 作用和注意事项

作用
>原型对象本身就是有效地利用了每个构造器创建的对象。

 主意事项
>浅拷贝和深拷贝的问题，免得出现引申问题。

>现有的文献里查看原型模式的定义，没有针对JavaScript的，你可能发现讲解的都是关于类的，但是现实情况是基于原型继承的JavaScript完全避免了类（class）的概念。

##代码实战
```html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>原型模式</title>
</head>
<body>
<script>
    var myObj = {
        str : "mystring",
        number:1,
        arr:[1,2,3],
        obj: {
            innerObj:{
                num:5
            },
            str:'nini'
        }
    };
    // 浅克隆(修改拷贝出来的对象的深层次属性时，原对象会跟着改变)
    function clone(obj) {
        var result = {};
        for(var key in obj) {
            result[key] = obj[key];
        }
        return result;
    }
    //深克隆（修改拷贝出来的对象的任何属性，都不会影响到原对象）
    function deepClone(obj) {
        var result;
        var _toString = Object.prototype.toString;
        if((result=(_toString.call(obj) ==='[object Array]')) || _toString.call(obj) === '[object Object]'){
            result = result ? [] : {};
            for(var key in obj) {
                if(_toString.call(obj[key]) === '[object Array]' || _toString.call(obj[key]) === '[object Object]'){
                    result[key] = deepClone(obj[key]);
                }else{
                    result[key] = obj[key];
                }
            }
        }
        return result;
    }
    //es5 原型模式
    // Object.create() 方法创建一个拥有指定原型和若干个指定属性的对象。用于创建原型模式
    var aa = Object.create(myObj,{
        type:{
            value:"人民银行",
            writable:true,// 是否可以改写属性
            enumerable:false,//是否可以被枚举
            configurable:false//是否可以被删除
        }
    });
    aa.type = '平安银行';// 修改
    delete aa.type;//删除
    for( var k in aa){//枚举
        console.log(k);
    }
    //原型模式测试
    aa.obj = {innerObj:'men'};//修改了对象下的obj对象，但是不影响myObj对象下的obj对象
    aa.str = "wen";//修改了对象的str，不影响myObj对象的str
    console.log("原型模式"+aa.obj.innerObj+' '+aa.str);
    console.log("原型模式"+myObj.obj.innerObj+' '+myObj.str);//未改变
    //深克隆测试
    var sObj = deepClone(myObj);
    sObj.obj.innerObj="深克隆";//修改了拷贝出来的qianObj的第二层属性（即指的深层属性）时
    console.log(sObj.obj.innerObj)//“深克隆”
    console.log(myObj.obj.innerObj)//{num：5}（未被改变）
    //浅克隆测试
    var qObj = clone(myObj);
    qObj.obj.innerObj="浅克隆";//修改了拷贝出来的qianObj的第二层属性（即指的深层属性）时
    console.log(qObj.obj.innerObj)//“浅克隆”
    console.log(myObj.obj.innerObj)//“浅克隆”（可以看出，原对象被改变了）
</script>
</body>
</html>
```
>以上信息来自[极客学院](http://www.jikexueyuan.com/)的视频观看，属于个人记录，以及添加了一些个人理解。
