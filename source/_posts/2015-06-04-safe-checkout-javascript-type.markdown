---
layout: post
title: "安全监测javascript基本数据类型和内置对象"
date: 2015-06-04 17:13:36 +0800
categories: [javascript, 检测数据类型]
tags: [javascript, 检测数据类型]
comments: true
---

## 实现代码

```
//参数：o表示监测的值
/*返回值：返回字符串“undefined”, "number", "boolean", "string" ,"function", "regexp" ,"array", "date", "error", "object" 或 "null" */
function typeOf(o){
    var _toString =  Object.prototype.toString;
    // 获取对象的toString()方法引用
    //列举基本数据类型和内置对象类型，可以进一步补充该数组的监测数据类型范围
    var _type = {
        "undefined" : "undefined",
        "number" : "number",
        "boolean" : "boolean",
        "string" : "string",
        "[object Function]" : "function",
        "[object RegExp]" : "regexp",
        "[object Array]" : "array",
        "[object Date]" : "date",
        "[object Error]" : "error"
    };
    return _type[typeof o] || _type[_toString.call(o)] || (o ? "object" : "null");
}
//测试
console.log(typeOf(undefined));// "undefined"
console.log(typeOf(2));// "number"
console.log(typeOf(true));// "boolean"
console.log(typeOf("hello"));// "string"
console.log(typeOf(function(){}));// "function"
console.log(typeOf(/hello/));// "regexp"
console.log(typeOf([1,2,3]));// "array"
console.log(typeOf(new Date()));// "date"
console.log(typeOf(new Error()));// "error"
console.log(typeOf({}));// "object"
console.log(typeOf(null));// "null"
```
>节选自书--《编写高质量代码》
