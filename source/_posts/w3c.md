---
title: W3C代码标准规范是什么，那有些规定
tags:
- "W3C规范"
- "XHTML"
categories: 
 - HTML

description: "本文简单介绍W3C规范对XHTML的规定，什么才是一个标准的，符合规范的网页文档"
keywords: "前端规范，XHTML"
cover: https://media.geeksforgeeks.org/wp-content/uploads/20200424201843/W3C.png
---

## 什么是 W3C？

- W3C 指万维网联盟（*World Wide Web Consortium*）
- W3C 创建于*1994年10月*
- W3C 由 *Tim Berners-Lee* 创建
- W3C 是一个*会员组织*
- W3C 的工作是*对 web 进行标准化*
- W3C 创建并维护 *WWW 标准*
- W3C 标准被称为 *W3C 推荐（W3C 规范）*

> W3C 最重要的工作是发展 Web 规范

## 为什么要设计规范？

主要解决残障人士方便的获得Web内容相关的技术问题并形成解决方案，包括Web信息无障碍的基本原则、实现技术、评估方法等。

## 为什么要遵循标准？

作为生产者实际上只是位于中游，既不是上游的浏览器制造商，他们是标准的真正制定者，也不算是下游，他们是浏览器的终端使用者。这个角色就意味着我们位于一个接口的位置，我们需要想办法满足下游的用户使用上游不同浏览器时看到的是同样的内容。

我们想方设法让我们的页面、我们的程序能够支持所有的浏览器、能够满足尽可能多的用户。我们要满足所有的客户，即使做不到，我们也要满足我们技术范围之内的所有用户。

## 遵循标准的目的

制作一个标准的网站，让使用者感觉不到跟标准有关

## W3C代码标准规范内容

### 废弃`<font>`标签

抛弃font标签，新的页面中不应该再出现如`<font color="red"></font>`，而是使用`span`进行替代：`<span class="red"></span>`

### 文档声明

一个标准XHTML头信息格式，可以利于搜索引擎优化（SEO）

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" lang="gb2312"> 
<head> 
    <meta charset="utf-8" /> 
    <title>W3Cschool - 学技术查资料，从w3cschool开始!</title>
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
    <meta name="renderer" content="webkit" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="keywords" content="关键字，以逗号分隔" />
    <meta name="description" content="网页描述文字" />
</head>
```

* **DOCTYPE**

  浏览器需要解析文档头部的DOCTYPE来解释页面的标识，并展现出来，所以DOCTYPE是建立符合标准的XHTML网页的必不可少的部分，没有DOCTYPE的XHTML的任何标识都不会生效

  DOCTYPE分为三种类型：

  1. 过渡模式Transitional：允许你继续使用HTML4.01的标识

     ```html
     <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
     ```

  2. 严格模式Strict：不能使用任何表现层的标识和属性，例如`<br>`

     ```html
     <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
     ```

  3.  框架模式Frameset：专门针对框架页面设计

     ```html
     <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
     
     ```

### 定义语言编码

为了被浏览器正确解释和通过W3C代码校验，所有的XHTML文档都必须声明它们所使用的编码语言，我们一般使用UTF-8

```html
<meta charset="utf-8">
```

### JavaScript定义

JavaScript必须要用`<script language="javascript" type="text/javascript">`来定义，并且需要加个注释符`//<![CDATA[ ... //]]>  `，以保证不在不支持js的浏览器上直接显示出代码来。 

```html
<script language="javascript" type="text/javascript"> 
//<![CDATA[ 
var a = 1
//]]> 
</script> 
```

### CSS定义

CSS标签必须要用`<style type="text/css">`来定义，与js相同，最好加入html注释，且所有数值单位最好带上对应单位，以保证兼容性，

```html
<style type="text/css" media="screen"> 
<!-- 
    body {
        margin:0px;
        padding:0px;
        text-align:center
    } 
--> 
</style>
```

>  不要在注释内容中使用 --

### 所有标签的元素和属性的名字都必须使用小写

XHTML对大小写敏感，例如`<title>`和`<TITLE>`是不同的标签，XHTML要求所有的标签和属性的名字都必须使用小写

### 所有的属性必须用引号`" "`括起来

例如：`<div height=80></div>`必须修改为：`<div height="80"></div>`,单引号可以使用`&apos`代替

### 把所有<和&特殊符号用编码表示

任何小于号 `<`，不是标签的一部分，都必须被编码为 `&lt;`
任何大于号`>`，不是标签的一部分，都必须被编码为 `&gt;`
任何与号`&`，不是实体的一部分的，都必须被编码为 `&amp;` 

### 标签所有属性必须赋一个值

XHTML规定所有属性都必须有一个值，没有值的就重复本身

```html
<td nowrap="nowrap"><input type="checkbox" name="shirt" value="medium" checked="checked" />
```

### **所有的标记都必须要有一个相应的结束标记**

在HTML中，有些标签可以不用闭合，例如`<p>`和`<li>`而不一定写对应的`</p>`和`</li>`来关闭，但在XHML中这种操作不合法，所有标签必须关闭

### 所有的标记都必须合理嵌套

例如` <li></li>`只能出现在`<ol>` 或者`<ul>`中，在行内元素标签中不能嵌套块元素标签

### 链接与图片添加有意义的属性

所有的`<a>`标签需要添加`title`来帮助显示不完整的信息

所有`<img/>` 标签需要添加`alt`属性来描述图片信息

### 在form表单中增加`lable`，以增加用户友好度

`<lable>`标签的好处就是，当在点击时，自动聚焦到对应的表单输入框内

> 更多W3C规范信息，请参见 [W3C英文官网](https://www.w3.org/Consortium/)
