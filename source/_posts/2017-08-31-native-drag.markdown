---
layout: post
title: "原生拖放"
date: 2017-08-31 14:05:02 +0800
comments: true
categories: [拖放, html5]
tags: [拖放, html5]
---

## 前言

- 最早引入拖放的是IE4， IE4当时只有两种对象可以拖放： 图像和某些文本； 唯一的放置目标：文本框；
- IE5 扩展了新的事件，几乎网页中任何元素都可以作为放置目标；
- IE5.5 任何元素都可以拖放

> ps： HTML5就是以IE的拖放实例为基础制定了拖放规范的。

## 事件

拖动元素时发生的事件依次触发有3个：（事件目标为拖动的元素）

- dragstart  按下鼠标键并开始移动鼠标时触发
- drag       元素被拖动期间会持续触发
- dragend    拖动停止时触发（无论元素是否放置在了有效目标上都触发）

当某个元素被拖动到一个有效的放置目标上时，依次会触发以下事件：（事件目标为放置目标的元素）

- dragenter  只要有元素拖动到放置目标上，就会触发
- dragover   被拖动的元素在放置目标范围中移动触发
- dragleave/drop 被拖动的元素拖出放置目标范围触发dragleave/元素要是被放置到目标中触发drop

<!--more-->

## 如何把任何元素变成有效的放置目标呢？

直接阻止dragenter 和dragover这两个事件的默认行为即可实现，代码如下

```
// 假如droptargetNode为放置目标的元素
droptargetNode.addEventListener('dragenter', function(evt) {
    evt.preventDefault();
})
droptargetNode.addEventListener('dragover', function(evt) {
    evt.preventDefault();
})

```
存在的兼容性：

- firefox3.5中 放置事件drop的默认行为是打开被放到放置目标上的URL，如把图像拖放到放置目标上，页面就会转向图像文件，但是如果把文本拖放到放置目标上时，会导致无效的URL错误。所以要兼容还得取消drop事件的默认行为，阻止它打开URL，代码如下：

```
droptargetNode.addEventListener('drop', function(evt) {
    evt.preventDefault();
})
```

## 如何把元素设置为可以拖动呢？

默认情况下，图像，链接和文本是可以拖动的。而其他元素怎么实现让它也能拖动呢，html5规范为所有HTML元素添加了一个draggable属性，表示元素是否可以被拖动。当设置为true时，即可被拖动，false为不可被拖动。

这里有个兼容问题：firefox浏览器要是不在dragstart事件中给dataTransfer对象设置保存一些信息的话，元素还是无法被拖动。

## 如何在拖放操作时实现数据交换？dataTransfer对象。

针对这个问题，IE5引入了**dataTransfer**对象，它是事件对象的一个属性

dataTransfer对象的作用： 用于被拖动元素向放置目标传递字符串格式的数据

dataTransfer对象有两个主要方法：

- getData()  只能在drop事件处理程序获取保存的数据
- setDate()  第一个参数表示保存的数据类型，取值为“text” 或 “URL”( IE只定义了这两种有效的数据类型，HTML5扩展了，允许指定各种MIME类型)

代码事例：

```
// 设置和接收文本数据
evt.dataTransfer.setData('text','some text');
var text = evt.dataTransfer.getData('text); // some text

// 设置和接收URL
evt.dataTransfer.setData('URL','https://wenyang12.github.io/');
var text = evt.dataTransfer.getData('URL); // https://wenyang12.github.io/
```

> ps： HTML5规范扩展了各种MIME类型的同时，考虑到向后兼容，也同时保留了对“text” 和“URL” 这两种数据类型的支持，要是你指定了这两种类型，会映射为“text/plain” 和 “text/uri-list”

**浏览器在你拖动文本或图像时，默认行为做了些什么呢？**

当你拖动文本或图像时，浏览器会默认调用setData()方法，将拖动的文本以“text”或“URL”格式保存在dataTransfer对象中，
然后当放置到目标元素时，就可以通过getDage() 方法读取到这些数据，所以我们要是想保存自己的数据，可以在dragstart事件发生时，调用setData设置自己要保存的数据，然后在放置事件drop触发时，就可以通过dataTransfer对象获取到保存的数据了。

**兼容问题**

firfox在第五版本之前不能正确映射“URL”和“text”为 “text/uri-list” 和 “text/plain” ，但是能把“Text”(T大写) 正确映射，
所以为了兼容，应该处理如下：

```
// 读取URL
dataTransfer.getData('URL') || dataTransfer.getdata('text/uri-list')

// 读取文本
dataTransfer.getData('Text');
```

> ps: IE10之前都不支持扩展的MIME类型名称，所以，读取URL时必须把dataTransfer.getData('URL')放前面，不然ie在遇到无法识别的数据类型时，会抛出错误。

**dropEffect 与 effectAllowed**

- dropEffect在dragenter事件中设置，即在放置目标上设置,它包含的值有：
    - 'none': 不能把拖动的元素放在这里。这是除文本框之外说有元素的默认值。
    - 'move': 应该把拖动的元素移动到放置目标。
    - 'copy': 应该把拖动的元素复制到放置目标。
    - 'link': 表示放置目标会打开拖动的元素（但拖动的元素必须是一个连接，有URL）


- effectAllowed在dragstart事件中设置，即在拖动目标上设置， 这个属性表示允许拖动元素的那种dropEffect。它包含的值有：
    - 'uninitialized': 没有给被拖动的元素设置任何行为。
    - 'none': 被拖动的元素不能有任何行为。
    - 'copy': 只允许值为"copy"的dropEffect
    - 'link': 只允许值为"link"的dropEffect
    - 'move': 只允许值为"move"的dropEffect
    - 'copyLink': 只允许值为"copy"和"link"的dropEffect
    - 'copyMove': 只允许值为"copy"和"move"的dropEffect
    - 'linkMove': 只允许值为"link"和"move"的dropEffect
    - 'all': 允许任意dropEffect

> ps: dropEffect 属性只有搭配effectAllowed属性才有用。

**其他**
  
dataTransfer还包括以下属性和方法：

- addElement(elemet)            为拖动元素添加一个元素，只有Firefox3.5+实现
- clearData(format)             清除以特定元素保存的数据。实现浏览器有： IE， Fireforx 3.5+, Chrome, Safari 4+
- setDragImage(element, x, y)    指定一副图像，当拖动发生时显示在光标下方，x,y为光标在图像中的x,y坐标。实现的浏览器有：Firefox 3.5+，Safari 4+， chrome
- types： 当前保存的数据类型。     实现的浏览器有： IE10+，Firefox3.5+， Chrome

## 参考

JavaScript 高级程序设计（第三版） 作者：Nicholas C.Zakas

