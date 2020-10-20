---
title: 使用jQuery简单实现网页图片懒加载
tags:
 - "JavaScript"
 - "jQuery"
 - "HTML5"
categories: 
 - JavaScript
keywords: "JavaScript,图片懒加载,前端性能优化"
cover: https://www.techgenyz.com/wp-content/uploads/2018/02/google-chrome-lazy-loading.jpg
---

## 前言

前段时间看到过一个面试题：

> 一个页面上有大量的图片（大型电商网站），加载很慢,你有哪些方法优化这些图片的加载，给用户更好的体验

第一个想到的就是用图片懒加载，但学了这么久JS，在之前做项目时，许多库与插件都自带图片懒加载，就比如说Hexo框架搭建的博客

但具体实现，经过一番思考，其实很简单：使用JS监听滚动条事件，在其中判断每一个图片与浏览器窗口以及滚动条卷去的距离，控制好时机放入url到图片的src中

所以这里我们使用H5的自定义属性：data-xxx 配合JS中DOM元素的 `dataset['xxx']` ， 

