---
layout: post
title: "JavaScript ES5之Object.create函数详解"
date: 2015-09-11 16:09:03 +0800
categories: [es5, javascript]
tags: [es5, javascript]
comments: true
---
## 介绍

在创建对象的时候，我们有2种常用方法

一个是文本标记法(var obj = {}),一种是运用Object函数进行对象的创建(new Object()).

但是这两种方式并不是创建的一个完完全全“干干净净”的对象,这里的干净只得是没有继承链.

幸运的是，ES5为我们提供了一种创建完全”干净”的对象的方法，Object.create函数,接下我将向大家介绍Object.create的详细使用

## 语法

    Object.create(proto, [ propertiesObject ]);

## 参数介绍

- proto

	>一个对象，作为新创建对象的原型。如果 proto 参数不是 null 或一个对象值，则抛出一个 TypeError 异常。null表示没有原型对象(这样就创建了一个”干净的对象”)

- propertiesObject

	>一个对象值，可以包含若干个属性，属性名为新建对象的属性名，属性值为那个属性的属性描述符对象.(属性将做简单介绍，后面将详细解答)

>value: 设置属性的值

>writable: 布尔值,设置属性是否可以被重写,默认属性值为false(不能被重写)

>enumerable: 布尔值,设置属性是否可以被枚举,默认属性值为false(不能被枚举)

>configurable: 布尔值,设置属性是否可以被删除，默认属性值为false(不能被删除)

>get: 函数,设置属性返回结果(后面解答)

>set: 函数,有一个参数(后面解答)

## 详细使用

###简单对象创建

继续上面的创建”干净”的对象，我们可以这么做:

    javascript   var clearObj = Object.create(null);
       //Object with no prototype

现在我想创建一个银行账户对象，占时关注它是哪个银行的

    var account = Object.create(Object.prototype,{
       type: {
           value: "建设银行"
           //enumerable: false
           //configurable: false
           //writable: false
       }
       });
       account.type; // "建设银行"

### writable属性使用

继续上面的例子，有好奇的同学可能已经发现了，上面的例子中，我们做一下操作:

    account.type = "人民银行"; //"人民银行"
    account.type; // "建设银行"

这是怎么回事？别急，这就是writable属性惹得货，如果我们将属性设置为true

    account.type = "人民银行"; //"人民银行"
    account.type; // "人民银行"

### configurable属性使用

还是上面的例子，突然，我不想要account对象的type属性了，自然的我们会想到

    delete account.type // false

我去！！！删都不让我删！？ 别急，这次是configurable属性的问题，如果我们将它设置为true

    account.type; // "建设银行"
    delete account.type // true
    account.type; // undefined


### enumerable属性值

现在我要遍历account对象

    for(var i in account){
               console.log(account[i]);
           }
           // undefined

没有结果！？type属性去哪了！？我知道大家都很聪明，这次肯定是enumerable属性值惹得祸,如果enumerable属性设置为true

    for(var i in account){
               console.log(account[i]);
           }
           // "建设银行"

### get和set的用法

现在我需要我的account对象有另外一个属性：账户号码,我需要如果修改了给出特定格式，没有账户需要给出警告

```
var account = Object.create(Object.prototype,{
	type: {
	   value: "建设银行",
	   enumerable: true,
	   configurable: false, // 不能被删除
	   writable: false // 不能被修改
   },
   number: {
	   get: function(){
		   if((typeof number) === "undefined"){
			   //说明没有设置number
			   return "您还没有开通账户!请联系前台!";
		   }
		   return "您的账户号码是："+number;
	   },
	   set: function(num){
		   number = num;
		   if(this.cTime === 0){
			   console.log("账户开通成功！");
			   this.cTime++;
		   }else{
			   console.log("账户号码已经被修改！");
		   }
	   }
   },
   cTime: {
	   value: 0,
	   writable: true
   }
});
account.type; // "建设银行"
account.number; // "您还没有开通账户!请联系前台!"
account.number = "610XXXXXXXXXX21";
// "账户开通成功！"
account.number; // "您的账户号码是：610XXXXXXXXXX21"
account.number = "610XXXXXXXXXX88";
// "账户号码已经被修改！"
account.number; // "您的账户号码是：610XXXXXXXXXX88"
```
## 注意
以上代码均在Chrome浏览器console环境下测试
在使用set,get函数的时候，不能和value属性和writable属性一起用，会报错
文章出自：<http://hao.jser.com/archive/8062/?utm_source=tuicool>
