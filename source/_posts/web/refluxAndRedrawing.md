---
title: 浅谈回流与重绘
permalink: /web/reflux/and/redrawing.html
date: 2023-10-21 21:05:16
description: 回流与重绘，都是在浏览器响应过程中展示环节对数据进行对比，而产生的两种不同的方式。
tag: [前端, 浏览器, 性能优化]
comments: true
categories: 
 - 浏览器
 - 性能优化
---

##### 回流
当渲染树中部分或者全部元素的尺寸、结构或者属性发生变化时，浏览器会重新渲染部分或者全部文档的过程就称为回流。
下面这些操作会导致回流：

 - 页面的首次渲染
 - 浏览器的窗口大小发生变化
 - 元素的内容发生变化
 - 元素的尺寸或者位置发生变化
 - 元素的字体大小发生变化
 - 激活 CSS 伪类
 - 查询某些属性或者调用某些方法
 - 添加或者删除可见的 DOM 元素
 
在触发回流（重排）的时候，由于浏览器渲染页面是基于流式布局的，所以当触发回流时，会导致周围的 DOM 元素重新排列，它的影响范围有两种：
全局范围：从根节点开始，对整个渲染树进行重新布局
局部范围：对渲染树的某部分或者一个渲染对象进行重新布局

##### 重绘
当页面中某些元素的样式发生变化，但是不会影响其在文档流中的位置时，浏览器就会对元素进行重新绘制，这个过程就是重绘。

下面这些操作会导致重绘：
color、background 相关属性：background-color、background-image等
outline 相 关 属 性 ： outline-color 、outline-width、text-decoration
border-radius、visibility、box-shadow

注意： 当触发回流时，一定会触发重绘，但是重绘不一定会引发回流。

##### 如何避免回流与重绘？
减少回流与重绘的措施：

操作 DOM 时，尽量在低层级的 DOM 节点进行操作不要使用 table 布局， 一个小的改动可能会使整个table 进行重新布局

使用 CSS 的表达式

不要频繁操作元素的样式，对于静态页面，可以修改类名，而不是样式。

使用 absolute 或者 fixed，使元素脱离文档流，这样他们发生变化就不会影响其他元素

避免频繁操作 DOM，可以创建一个文档片段documentFragment，在它上面应用所有 DOM 操作，最后再把它添加到文档中

将元素先设置 display: none，操作结束后再把它显示出来。因为在display 属性为 none 的元素上进行的 DOM 操作不会引发回流和重绘。将 DOM 的多个读操作（或者写操作）放在一起，而不是读写操作穿插着写。这得益于浏览器的渲染队列机制。

浏览器针对页面的回流与重绘，进行了自身的优化——渲染队列浏览器会将所有的回流、重绘的操作放在一个队列中，当队列中的操作到了一定的数量或者到了一定的时间间隔，浏览器就会对队列进行批处理。这样就会让多次的回流、重绘变成一次回流重绘。

上面，将多个读操作（或者写操作）放在一起，就会等所有的读操作进入队列之后执行，这样，原本应该是触发多次回流，变成了只触发一次回流。