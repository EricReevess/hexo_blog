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

> 一个页面上有大量的图片（大型电商网站），加载很慢  ，你有哪些方法优化这些图片的加载，给用户更好的体验？

第一个想到的就是用图片懒加载，但学了这么久JS，在之前做项目时，许多库与插件都自带图片懒加载，就比如说Hexo框架搭建的博客，

但具体实现，经过一番思考，其实很简单

## 思路

这里我们使用H5的自定义属性：data-xxx 存储图片url

将所有图片的url存储在自定义属性中，使用JS监听滚动条事件，并判断每一个图片与浏览器窗口以及滚动条卷去的距离，当`<img>`标签符合出现在视口的条件时，将存储在自定义属性中的url赋给其`src`属性（为了简单起见，就不做节流处理了）

## 代码

```js
$(function () {
    //  需要将所有图片的url放在其data-img-url自定义属性中，
    //  在页面打开的时候变量所有img，并判断他们是否在视口当中，有，则将url放入src中
    $('img').each(function () {
      if (isShow($(this)) && !isLoaded($(this))) {
        //加载当前img
        loadImg($(this))
      }
    })

    $(window).on('scroll',function () {//滚动的触发事件
      $('img').each(function () {//遍历img标签
        if (isShow($(this)) && !isLoaded($(this)) ){
          loadImg($(this));//加载当前img
        }
      })
    })
    function checkShow($img) { // 传入img对象
      const scrollTop = $(window).scrollTop()  // 获取卷去的距离
      const windowHeight = $(window).height() // 获取浏览器自身的高度
      const offsetTop = $img.offset().top  //目标标签img相对于document顶部的位置
      // 如果图片的头部出现在了视口底部或者图片的完整地出现在了视口顶部
      return offsetTop < (scrollTop + windowHeight) && offsetTop > scrollTop

    }
    //如果data-img-url和src相同那么就是已经加载了图片
    function isLoaded ($img) {
      return $img.attr('data-img-url') === $img.attr('src')
    }
    // 把自定义属性中存放的真实的src地址赋给src属性
    function loadImg ($img) {
      $img.attr('src',$img.attr('data-img-url'))
    }
  })
```

