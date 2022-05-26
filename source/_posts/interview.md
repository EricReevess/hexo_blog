---
title: 前端常见面试题汇总
tags:
 - "面试题"
categories: 
 - 前端面试题
keywords: "面试题"
cover: https://img-blog.csdnimg.cn/20200618180003244.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoYW95b3VpcW5n,size_16,color_FFFFFF,t_70
---
> 简单总结前端热门面试题要点

# 概述

HTML5 技术栈

* 语义化标签类
* 音视频处理
* Canvas 、 webGL
* history API
* requestAnimationFrame
* 地理位置
* webSocket(直播)

CSS3 

* 常规
* 动画
* 盒子模型
* 响应式布局

JavaScript

* ES 6-11
* DOM
* BOM
* 设计模式
* 底层原理
  * 堆栈内存
  * 闭包作用域 AO/VO/GO/EC/ECSTACK
  * 面向对象
  * this
  * 事件循环
  * 浏览器渲染原理
  * 回流重绘

网络通信

* AJAX / Fetch / axios
* HTTP协议 1.0 、 2.0
* TCP
* 跨域处理方案
* 性能优化

Hybrid 或者 APP 和 小程序

* Hybrid
* uni-app (Vue)
* React Native
* Weex
* PWA

前端工程化

* webpack
* git
* linux 
* nginx

React

* 基础知识
* 核心原理
* react-router-dom
* redux
* react-redux
* dva
* umi
* antd
* SSR
* 优化
* .....

全栈

* NodeJs

# H5 /CSS面试题

## 什么是标签语义化？

   简单的来说，H5标签语义化就是将标签名与其功能联系在一起，例如header负责显示网页头部  footer负责显示网页尾部 等，让合适的标签干合适的事情

## 总结H5标签

   H5标签分为3类，

   可以使用 display: block/inline-block/inline/none/table/flex 进行转换

   * 块级标签
     * `div p h1-h6 ol ul dl li form table hr pre`(格式预处理标签)
     * H5 新增 `header footer main`(网页主体) `article`(文章内容) `section` `figure`(图片相关信息的容器) figcaption(图片描述) nav
     * 独占一行，
     * 元素的宽度和高度，行高可设置
     * 支持所有css样式
   * 行内标签
     * `span a br i em strong` `sub`(上标) `sup`(下标) `lable` `code`(代码块)
     * 不会独占一行
     * 宽高与内外边距不可设置，内容决定宽高
     * 设置为行内块元素则可以设置宽高
   * 行内块元素
     * `img` `input` 
     * 不独占一行，且宽高边距均可设置

   标签之间的嵌套规则

   * 行内标签不能嵌套行内标签
   * 行内标签不能嵌套块级标签
   * ol ul 的直接子级一定是li

## 总结有哪些隐藏元素的方法

   * `display : none`
   * `visibility : hidden`
   * `height : 0 ` 

## css如何实现水平垂直居中(5种)

   ```css
   position:absolute;
   top:50%;
   left:50;
   margin-left:-50%;
   margin-top:-50%
   ```

   ```css
   position:absolute;
   top:50%;
   left:50;
   transform:translate(-50%,-50%);
   ```

   ```css
   /*元素必须拥有宽高*/ 
   position:absolute;
   top:0;
   right:0;
   bottom:0;
   left:0;
   margin:auto;
   ```

   ```css
   /*父级设置*/
   display:flex;
   justify-content:center;
   align-item:center;
   ```

   ```css
   /*父级设置*/
   display: table-cell;
   text-align:center;
   vertical-align:middle
   
   ```

## position 对应的效果和用法

   * `static`  默认定位属性，按照常规文档流布局，此时 `top`, `right`, `bottom`, `left` 和 `z-index `属性无效。

   * `relative`  定位在当前所在位置，且以当前位置为原点进行移动

   * `absolute`  元素被移出正常文档流，相对于最近的非static定位的祖先进行定位
   * `fixed` 元素被移出正常文档流，且元素相对于视口进行定位，会创建新的**层叠上下文**

   * `sticky/-webkit-sticky` 将元素固定在一个具有滚动属性的祖先上创建一个新的**层叠上下文**

   如果`top`和`bottom`都被指定，那么`top`优先，如果`left`和`right`都被指定，如果文字方向从左到右，`left`优先，反之亦然

## css 选择器优先级
   优先级就是分配给指定的 CSS 声明的一个权重，它由 匹配的选择器中的 **每一种选择器类型的** 数值 决定。

   而当优先级与多个 CSS 声明中任意一个声明的优先级相等的时候，CSS 中最后的那个声明将会被应用到元素上。

   所以当同一个元素有多个声明的时候，优先级才会有意义。

   * 通配选择器、关系选择器、否定选择器 0000，即对优先级无影响
   * 元素(标签)选择器 0001
   * 类选择器 0010
   * ID选择器 0100
   * 内联样式 1000 ，最高优先级

   尽少使用`!important` ，因为它与优先级不相关，与最终结果有关，使用`!important` 破坏了样式表级联规则，当2条 不同优先级的样式均带有`!important` 时，优先级大的任然被采用

   > **一些经验法则：**
   >
   > - **一定**要优化考虑使用样式规则的优先级来解决问题而不是 `!important`
   > - **只有**在需要覆盖全站或外部 CSS 的特定页面中使用 `!important`
   > - **永远不要**在你的插件中使用 `!important`
   > - **永远不要**在全站范围的 CSS 代码中使用 `!important`

## 盒子模型有哪些

   * 标准盒模型 content-box : 

     盒子的总体宽高是由 border+padding+设置的content 宽和高决定的，这样的盒模型在布局时，例如居中或者对齐，可能需要精确计算内容、边框、padding的大小，使得开发效率并不是很高

   * 怪异盒模型 border-box ：

     ​	这种设置这种盒模型的块级元素的宽和高一般不受内边距影响，border+padding+content宽高始终等于设置的宽高值，大多数布局框架中基本以怪异盒模型为标准

   * 弹性盒模型 flex：

     ![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071004.png)

     容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做`main start`，结束位置叫做`main end`；交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`。

     * 项目默认沿主轴排列。单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`。

     * `flex-flow`属性是`flex-direction`属性和`flex-wrap`属性的简写形式，默认值为`row nowrap`

     * `align-content`属性定义了多根 **主轴线** 在**交叉轴** 上的对齐方式，需要开启换行`flex-wrap:wrap`如果项目只有一根轴线，该属性不起作用。

     * `flex-basis`属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的内容大小。

     * `flex`声明在项目上 ， `flex`属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选，通常使用`flex: 1`，它等同于：

       ```css
       flex-grow: 1;  元素增长比例为1
       flex-shrink: 1; 元素缩小比例为1
       flex-basis: 0%; 元素没有定宽，的宽度将填充并均分剩余空间，当宽度不够时最小可以被压缩到0
       ```

       - 第一个参数表示: **flex-grow 定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大**

       - 第二个参数表示: **flex-shrink 定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小**

       - 第三个参数表示: **flex-basis**

         - `auto`：首先检索该子元素的主尺寸，如果主尺寸不为 `auto`，则使用值采取主尺寸之值；如果也是 `auto`，则使用值为 `content`。
         - 百分比：根据其包含块（即伸缩父容器）的主尺寸计算。如果包含块的主尺寸未定义（即父容器的主尺寸取决于子元素），则计算结果和设为 `auto` 一样。**百分比定义了当前的元素占用主轴空间的百分比，此时元素的width/height不起作用，所有值均由 flex-basis计算**

         

     * align-self 属性 设置在项目上，覆盖父级的align-items，独立管理交叉轴对齐方式

## 经典布局类型有哪些

  1. 圣杯布局：从上到下，分为header main footer 三部分，其中main分为left center right 从左向右，三部分
  2. 双飞翼布局：从左向右，分为left center right 三部分，其中left和right可有可无，不会影响center部分

## 移动端响应式布局方案

   1. media 媒介查询
   2. rem单位
   3. flex
   4. vh / vw 视窗高度、宽度

## z-index的工作原理
   默认值auto，值越大，层级越高，谁后渲染，谁谁层级越高

   1. 建立新的文档流

   2. 只对具有定位(r a f sticky)的元素有作用 

   3. 内联元素会覆盖浮动元素

      ![z-index](https://raw.githubusercontent.com/EricReevess/proxy/master/img/image-20200711113458770.png)

## 脱离文档流的方式：

   1. 浮动
   2. 定位
   3. transform

## CSS读取机制: 
选择器从右向左读取
```css
/* 先查找所有的a标签 ，再查找所有在.box下的a标签 */
.box a{
    /* ... */
}
```
## BFC

* BFC(Block formatting context), 块级别格式化上下文，一个独立的渲染区域，只有块级的元素参与，内部规定其如何布局，而且这个区域不影响外部区域,

* BFC是CSS中元素的一个隐含的属性，可以为一个元素开启BFC

* BFC布局规则

  * 开启BFC的元素不会被float元素所覆盖,且可以包裹float元素
  * 开启BFC的父元素的外边距不会与子元素重叠(不会因为子元素的外边距改变父元素位置)
  * BFC就是页面上一个隔离的独立容器，容器子元素不会影响容器外部的其他元素
  * 内部的Box会在垂直方向，一个接一个排布
  * BFC的区域不会与float的元素的margin重叠
  * 内部box的垂直方向间距由margin 确定，且相邻的margin 会发生重叠
  * 计算BFC高度时，浮动box参与计算

* BFC特性

  * 一个开启BFC的块级元素的**下边距**会与另一个开启BFC的块级元素的**上边距**重叠，最终间距为最大的上/下边距的值
  * 解决子元素浮动后，父级元素高度塌陷问题 ，开启BFC可让 父元素内部子元素进行浮动排列，并且撑开父级元素的高度

* 如何BFC的开启

  * 根元素是默认开启BFC的

  * 开启浮动float(失去指定宽度)

  * clear:left / right / both  (开启后浏览器默认为元素添加外边距) 

  * position 为绝对定位：absolute 或 fixed

  * overflow 不为 visible时 ：auto 、hidden (常用方式)

  * display 为 inline-block , table-cell , table-caption , flex , inline-flex

  * 使用::after伪类(完美方案)

    * ```css
      .box::after {
          content: "";      使其最后元素内容为空
          display: block;   默认为行内元素，不独占一行，所以使其独占一行
          clear: both;        清除浮动，完成效果
      }  
      ```

  * 或者将其::after 和 ::before 生成一个类.clearfix ,同时解决：高度塌陷和外边距重叠

  ```css
  .clearfix::after,.clearfix::before{
      content: "";
      display: table;
      clear: both;
  }
  ```

# JavaScript面试题

## 三大模块

* 堆栈内存以及闭包作用域
* 面向对象和this处理
* DOM / BOM / 事件处理机制

## 堆栈内存以及闭包作用域

1. JS的有那些数据类型，分别是？ 有基本数据类型和引用数据类型
   * 基本数据类型
     1. Number
     2. String
     3. Boolean
     4. undefined
     5. null
     6. Symbol(ES6)
   * 引用数据类型
     1. Object
     2. Function
2. 栈中的变量是存储的是对象在堆内存中的地址

### JS全局对象与活动对象

* JavaScript 预解析AO 、GO
  * AO(Activation Object)，活动对象、执行期上下文、也叫函数的局部作用域
    * 在JS函数预解析时，先创建对象AO{} ,
    * 先将形参与内部声明的变量加入到AO对象属性中，并赋值，再寻找函数声明，例如函数中的`let const var` 声明的变量
  * GO(Global Object)，全局对象，GO对象就等于window对象
    * 在window下预解析的时候，创建GO{}，
    * 先解析变量，再解析函数  

### js引擎解析代码流程

* 进入执行环境（栈），这里我们将js的整个文件当做一个函数
* 创建变量对象：
  * 变量
  * 函数以及参数
  * 全局对象：浏览器中则是window
  * 局部变量：被当做字符串，以抽象形式存在
* 确认全局this指向
  * 全局的this 指向 `window`
  * 局部 this 指向 被调用的对象
* 创建作用域链
  * 父级作用域链 + 当前的活动对象

1. 基本数据类型存放在栈中，存放实际数值，引用数据类型的数据结构存放在堆中，栈中存放其在堆中的地址

2. 对于对象来说，所有对象的键`key`均为String类型（undefined除外）

     ```js
     let obj ={};
     let a = {name:"123"};
     let b = {haha:"456"};
     obj[a] = 100;
     obj[b] = 200;
     console.log(obj[b]) // 输出 ？ 200
     ```

     * 当均为引用类型的a和b 被当做键`key`时，JS引擎默认将a和b进行一次字符串转换：`a.toString() b.toString()`均得到`[object  Object] ` 这一相同的字符串
     * alert()弹出的内容均会被转换成字符串

3. Execution Context Stack 执行上下文栈：当函数执行时，创建一个执EC Stack

    ```js
     var a = 0;      
     b = 0;
     function A(a){
         A = function(b){
             alert(a + b++);
         }
         alert(a++);
     }
     A(1);
     A(2);
    ```

     * 首先声明2个全局变量 a b 均为 0 
     * 函数A 的内容被放入堆中，内容形式为字符串，栈中A变量为堆中地址
     * 执行`A(1)`，此时函数在堆中的代码被放入执行上下文栈`ECS`中执行，将其中的a形参与A变量进行赋值
       * 在ECS中，a获得`A(1)`的参数值a =1 
       * 在ECS中执行到A的赋值语句时候，发现当前作用域无A变量，向上域寻找，最终到达window的作用域，将函数A的引用修改为内部的函数，此时A引用了一个新的地址，而这个地址是在`ECS`中被创建的，此时的`ECS`不会被销毁因为其中更新了全局变量A的引用，`a=1` 也得以保留
       * 再向下执行到`alert( a++ )`，输出 '1' ，a的值更新为2
     * 执行A(2)的时候，此时A的内的引用地址已经被上次执行修改成为内部的函数
     * 此时执行新指向的函数，开启新的ESC，其中b = 2 ，alert(a+b++)`此此时找到上级ECS作用域的a输出 `'4'` , b在ECS更新为3，然后这个ECS被销毁

## 深克隆、浅克隆

1. 使用`JSON.parse(JSON.stringify(obj))`，但只支持克隆`number` `string` `boolean` `null`数据类型，其余类型均会被转换成{}空对象

2. 使用被克隆对象的构造器：

   ```js
   function deepClone(obj){
       if(!obj) return null;
       if(typeof obj !== "object") return obj;
       if(obj instanceof RegExp) return new RegExp(obj);
       if(obj instanceof Date) return new Date(obj);
       
       let newObj = new obj.constructor; //保持与被克隆的obj类型一致，例如保持相同的原型方法
       for(let key in obj){
           if(obj.hasOwnProperty(key)){
               newObj[key] = deepClone(obj[key])
           }
       }
       return newObj
   }
   
   ```

## 变量提升与构造函数

* `var`具有变量提升：意思就是说，在`var` 声明的变量名与`function`声明的函数的函数名 相同时，`var`的变量会替代function

* 在js引擎对代码进行预解析时，`var`声明的变量会提前被解析，且将其赋值为`undefined`

* 当有多个`var`的变量名称相同且在声明时赋值时，最后声明并赋值的作为最终值,例如：

  ```js
  var a = 10;
  function a (){
  	console.log('fun a')
  };
  var a = 20;
   console.log(a); //输出 20 
  ```

* 如果使用`let`声明的变量与其他`let/var` 声明的任何类型的变量名重复，则浏览器抛出异常

  ```js
  var a = 10;
  let a = function(){
  	console.log('fun a')
   };
  ```

  ```js
  function Foo(){
      getName = function(){
          console.log(1)
      }
      return this;
  }
  Foo.getName = function(){
      console.log(2);
  }
  Foo.prototype.getName = function(){
      console.log(3);
  }
  var getName = function(){
      console.log(4)
  }
  functuion getName(){
      console.log(5)
  }
  Foo.getName(); // 2
  getName();	// 4
  Foo().getName(); // 1 执行了Foo，改变了全局的getName
  getName();	// 1 
  new Foo.getName(); // 2 括号比new优先级高，先调用再new
  new Foo().getName(); // 3 先执行 new Foo()创建一个实例，再访问实例的getName()，通过原型链查找
  new new Foo().getName(); // 3 与上调语句相同
  ```

## 箭头函数

* 无构造函数
* 无原型链
* 不能使用`new`，无法构造对象
* 继承父域this且不能被改变

## 事件队列、异步的微任务与宏任务

![事件队列](https://img-blog.csdnimg.cn/20200618180003244.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoYW95b3VpcW5n,size_16,color_FFFFFF,t_70)

* 事件循环主线程的执行队列的读取是自动的，在这里异步任务会在Event Table中注册相应的回调函数。当指定的步骤完成了，Event Table会将注册了的回调函数推送到Event Queue，在Event Queue这里进行等待上场。当主线程内的任务执行完毕为空之后，就会到Event Queue读取相应的函数，进入主线程执行。这个过程不断重复构成Event Loop。

* `new Promise(()=>{})`为同步函数

* 宏任务：

  * 宏任务是主流，当开始被执行js文件或者`sctipt`标签内的代码时，就是开启一个宏任务，在宏任务中执行一条一条的指令
  * 宏任务可以同时有多个，但会按顺序一个一个执行
  * 调用`setTimeout()`、`setInterval()` 与 Node中的`I/O`函数 、发送异步请求时，都会创建一个新的宏任务

* 微任务：`Promise.then()`传入的回调函数、 `process.nextTick`

* 案例1：

  ```js
  console.log('同步-0.1')
  Promise.resolve().then(() => {
    console.log('P-1.1')
    Promise.resolve().then(() => { // 新加行
      console.log('P-2.1') // 新加行
      Promise.resolve().then(() => { // 新加行
        console.log('P-3.1') // 新加行
      }) // 新加行
    }) // 新加行
  })
  setTimeout(() => {
    console.log('S-1.1')
  });
  Promise.resolve().then(() => {
    console.log('P-1.2')
  })
  setTimeout(() => {
    console.log('S-1.2')
  });
  console.log('同步-0.2')
  //输出
  /*
  同步-0.1
  同步-0.2
  P-1.1
  P-1.2
  P-2.1
  P-3.1
  S-1.1
  S-1.2
  */
  ```

* 案例2：

  ```js
  console.log('同步-0.1')
  setTimeout(() => {
    console.log('S-1.1')
  });
  new Promise(function(reslove, reject) {
      console.log('new Primose');
      reslove()
  }).then(() => {
      setTimeout(() => {
          console.log('P-1.1')
      })  
  })
    .then(()=> {
      setTimeout(() => {
          console.log('P-1.2')
      })
  })
  setTimeout(() => {
    console.log('S-1.2')
  });
  console.log('同步-0.2')
  /*
  new Primose
  S-1.1
  S-1.2
  P-1.1
  P-1.2
  */
  ```

  1. 先将代码中所有同步任务和异步任务进行扫描
  2. 将同步任务放入主线程执行
  3. 将异步任务分为宏任务和微任务进行扫描：微任务进入微任务队列，宏任务进入宏任务队列，扫描完毕后分为以下两个队列：
     * 宏任务：`console.log('S-1.1')` `console.log('S-1.2')`
     * 微任务：`setTimeout(() => {console.log('P-1.1')})`   `setTimeout(() => {console.log('P-1.1')})`

  4. 执行当前宏任务中的2个微任务中发现宏任务，将2个宏任务添加到宏任务队列中，

  5. 此时当前的宏任务中的微任务执行完毕，目前的宏任务队列状态为：
     * `console.log('S-1.1')` `console.log('S-1.2')` `console.log('P-1.1')` `console.log('P-1.2')`

  6. 执行宏任务队列，得到结果

* DOM操作的同步和异步

* js里面的Dom操作代码，是同步执行，浏览器进行的Dom渲染，是异步操作。

  * 浏览器渲染Dom和执行js，同时只能二选一，渲染一次Dom的时机是，当前宏任务和产生的微任务队列

* 案例3：计时器延迟

  ```js
  setTimeout(()=>{	//在被执行后的20ms，回调进入宏任务队列中
      console.log(1)
  },20)
  console.log(2)
  setTimeout(()=>{     //在被执行后的10ms，回调进入宏任务队列中
      console.log(3)
  },10)
  console.log(4)
  console.time('AA')
  for (let i = 0; i <90000000 ; i++) { //在执行完毕后，上面的2个计时器已经结束已经以延迟时间先后顺序进入宏任务队列中
  }
  console.timeEnd('AA')
  console.log(5)
  setTimeout(()=>{
      console.log(6)
  },8)
  console.log(7)
  setTimeout(()=>{
      console.log(8)
  },15)
  console.log(9)
  // 2 4 AA 5 7 9 3 1 6 8
  ```

  

* 案例4：async 函数 与 微任务：

  ```js
  async function async1(){
  	console.log('async1 start');
      await async2();
      console.log('async1 end');
  }
  async function async2(){
  	console.log('async2');
  }
  console.log('script start');
  setTimeout(()=>{
      console.log('setTimeout');
  },0)
  async1();
  new Promise((resolve)=>{
       console.log('promise1');
      resolve();
  }).then(()=>{
      console.log('promise2');
  })
  console.log('script end');
  /*
  输出：
  script start
  async1 start
  async2
  promise1
  script end
  async1 end
  promise2
  setTimeout
  */
  ```

  

* 案例5：立即执行函数的this指向：

  ```js
  var x=2;
  var y={
      x:3,
      z:(function (x) {
          this.x*=x
          x+=2
          return function (n) {
              console.log(this)
              this.x*=n
              x+=3
              console.log(x)
          }
      })(x)
  };
  var m = y.z; //在访问z时，返回一个匿名函数，其中立即执行函数的this指向的是window，同时m保存函数地址，产生闭包
  m(4) //此时返回的函数被window调用，内部this指向的仍然是window，其中的x为闭包函数中的被形参赋值的变量x
  y.z(5) //属性z与m指向的同一地址，但是调用者不同，
  console.log(x,y.x)
  ```

  * 执行`var m = y.z;` 在访问z时，返回一个匿名函数，其中立即执行函数的this指向的是window，同时m保存函数地址，因为使用到了参数x变量而产生闭包
  * 执行`m(4)` ，此时m指向的函数被window调用，内部this指向的仍然是window，其中的x为闭包函数中的被形参赋值的变量x
  * 执行`y.z(5)` 属性y的z与m指向的同一地址，但是调用者不同，前者为对象y调用z指向的函数，后者为window调用m指向的函数，所以this指向也不同

## JS类型转换

> 在 JS 中，只有 0，-0，NaN，""，null，undefined 这六个值转布尔值时，结果为 false

* 弱等于

  * `Object === 字符串` 时对象类型会调用`Object.toString()`后与字符串比较

    * 为当前`Object`重写`toString()`可得到定制结果

    ```js
      var a = ? //如何编写a使得下面判断语句成立
      if(a === 1 && a === 2 && a === 3){
      	console.log(1)
      }
    ```

      1. 改写对象a 的 toString()
      2. 使用数据劫持`Object.defineProperty(window,'a',{get(){return ...}})`

    3. `var a = [1,2,3]; a.toString = a.shift;`

* `null == undefined` 结果为`true`

* `NaN == NaN` 结果始终为 `false`

* 其他类型比较都转换为数字（数字，数组，布尔值等）

## 防抖(debounce)与节流(throttle)，闭包经典应用场景

* **防抖**主要用于防止回调函数在事件连续触发过程中不断调用的问题，浏览器性能有限，让对应高频触发的事件延迟到事件触发结束后的一段时间后执行，即有效得到了最新的结果，又防止了回调函数连续的调用

* 例如：HTML页面滚轮事件，当滚轮滚动页面时，每滚动一个像素就会触发一次页面滚动事件scroll，当有大量计算内容时，可能产生卡顿问题，使用监听防抖模式的回调可以有效解决这个问题：在滚动结束200ms后触发事件回调函数

  ```js
  const debounce = (fn, delay) => { // 传入原始回调以及定义的延迟时间，
      // 初始化timer变量  
      let timer = null  
      // 返回一个函数，制造闭包
      return function() {
      // 如果timer变量在下一次触发的时候不为null，那就清除上一次的定时器，并重新进行定时调用
      clearTimeout(timer)
      timer = setTimeout(() => {
          fn.apply(this, arguments) // 将this指向为外部的this
      },delay)
    }
  }
  window.onscroll = debounce(function(){
    console.log(document.documentElement.scrollTop) // 输出滚动的距离
  },200) //在滚动结束的0.2秒后调用原回调函数
  ```

  

* **节流**的主要功能与防抖类似，但是功能有所区别，节流的目的在于降低回调函数的执行评率，防止事件回调函数执行过快而造成的性能问题

* 例如：当我们在拖动一个HTML元素的时候，想让控制台每隔500ms输出一次元素的偏移量

  ```js
  const throttle = (fn, interval) => {
      // 设置一个可用标志位valid，标识可用状态
      let valid = true
      // 返回一个函数，产生闭包
      return function () {
          // 当函数被调用时，如果valid为false说明函数不可用，直接返回null，不执行fn
          if(!valid){
              return 
          }
          // 立即将下次的valid设置为false，即下次调用无效
          valid = false
          // 一旦函数变为可用状态，设置定时器定时调用原回调函数，并同时在一段时间后使valid为true，即函数有效
          setTimeout(() => {
              fn.apply(this, arguments)
              valid = true 
          }, interval)
          
  }
  }
  ```

## 函数柯里化
用于将接收多个参数的函数，转变为接收一个参数返回个函数的形式，便于保存上次状态以继续调用
```js
function add() {
    const arg = [...arguments]
    function fn() {
      arg.push(...arguments)
      return fn
    
    }
      fn.toString = function() {
        return arg.reduce((prev, cur) => prev+cur )
      }
    return fn
}
// 创建柯里化函数
function curry (fn) {
    const args = [...arguments].slice(1)
    return function () {
        const innerArgs = [...arguments]
        const finalArgs = [...args,...innerArgs]
        return fn.apply(null, finalArgs)
    }
}
```

## 模拟new操作

分为3步：
1. 使用构造函数的原型创建一个新对象newObj
2. 执行构造函数，并将this指向这个新对象newObj，传入参数
3. 如果构造函数没有返回新对象result，则返回第一次创建的对象newObj
```js
function _new(_constructor, ...args ) {
  // 如果第一个参数不是函数
  if (typeof _constructor !== 'function'){
    return 
  }
  // 以构造函数的原型创建一个空对象
  const newObj = Object.create(_constructor.prototype)
  const result = _constructor.apply(newObj, args)
  return result instanceof Object ? result : newObj
}
```

## 实现函数的call、apply、bind方法

```js
Function.prototype.call = function (context = window, ...args) {
    if(typeof this !== 'function'){
        throw new Error('Type Error')
    }
   	const fn = Symbol('fn')
   	context[fn] = this
  	const result = context[fn](...args)
    delete context[fn]
    return result
}
```



```js
Function.prototype.apply = function (context = window, args) {
    if(typeof this !== 'function'){
        throw new Error('Type Error')
    }
   	const fn = Symbol('fn')
   	context[fn] = this
  	const result = context[fn](...args)
    delete context[fn]
    return result
}
```



```js
Function.prototype.bind = function (context = window, ...args) {
    if(typeof this !== 'function'){
        throw new Error('Type Error')
    }
   	const _this = this
    return function Fn(){
        // 如果绑定后的函数用来做构造函数
        if(this instanceof Fn){
            return new _this(...args,...arguments)
        }else {
            return _this.apply(context, [...args, ...arguments])
        }
    }
}
```



## 跨域问题的解决方案和实现原理

* 什么是跨域？

  * 跨域，指的是浏览器不能执行其他网站的脚本。它是由浏览器的同源策略造成的，是**浏览器施加的**安全限制。

  * 当一个**AJAX**请求url的**协议、域名、端口**三者之间任意一个与当前页面url不同即为跨域

* 为什么会出现跨域问题?

  * 浏览器本身特点：
    * 当浏览器基于**AJAX**发送请求的时候， 请求url为其他域的域名时，浏览器会报错
    * 在13年以前，前后端没有分离的时代，数据处理与视图页面是捆绑在前一起的，不存在跨域问题。在13年开始的的前后端分离之后，前后端分为**数据接口服务器**与**Web资源服务器** ，这两个服务器还是在同一域名之下
    * 随着前后端分离的发展，存储各种资源的服务器分别在不同的子域名之下，或者分布在完全不同的域名之下，随即产生跨域问题，目前大型web项目几乎都使用非同源策略，来提高被访问性能
  * 出于浏览器的同源策略限制。同源策略（Sameoriginpolicy）是一种约定，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能可能都会受到影响。可以说Web是构建在同源策略基础之上的，浏览器只是针对同源策略的一种实现。同源策略会阻止一个域的javascript脚本和另外一个域的内容进行交互。所谓同源（即指在同一个域）就是两个页面具有相同的协议（protocol），主机（host）和端口号（port）

* **如何解决跨域？**

  1. 在发送**AJAX**请求时使用`JSONP`数据类型

     * 在HTML中，link标签，script标签 在使用链接请求文件时，不存在域的限制

     * 在`AJAX`中使用`JSONP`数据类型，原理就是动态创建了一个script标签，其请求类型只能是`Get`，其中`src`为请求url，并且给请求url传递一个带参数的函数，函数的细节在客户端`js`中：`?callback=fun(data)`

     * 当服务器接收到请求后，将数据转换成字符串拼接到这个作为参数的函数的参数中：`'fun({....数据})'`，并且返回这个字符串

     * 浏览器接收到字符串后，将执行这个字符串的语句，相当于传入参数并调用了函数`fun(data)`

       ```js
       var script = document.createElement('script')
       function getData(){
           // 使用数据
       }
       script.src = 'http://xxx.xxx.xxx.xxx:xxxx?callback=getData'
       document.body.appendChild(script)
       ```

     * `JSONP`存在的问题：

       * 信息传递不安全，信息可能被伪造和修改
       * 有缓存：浏览器会对script标签接收到的数据进行缓存
       * url存在长度限制
       * 需要服务器支持拼接数据

       * 基于**iframe**的跨域解决方案 

  2. iframe可以忽略域的影响，不存在域的限制

  3. CORS跨域资源共享

     * 跨域产生的错误一般来自于接口服务器的拒绝，需要在服务器端开启CORS跨域资源共享
     * 在前端框架中，使用`axios`进行封装一个模块，设置请求拦截器，携带接口服务器送回的`token`进行跨域请求，接口服务器通过校验`token`来判断请求是否合法，设置响应拦截器进行进一步操作
     * 浏览器ajax在跨域请求时候，浏览器会默认先以`OPTION`请求类型发送一个试探请求，试探目标服务器是否支持跨域

  4. 基于http proxy实现跨域请求（开发时使用，因为请求地址会被打包固定）

  5. Nginx反向代理，部署的时候使用

  

# 框架核心内容概述

## VUE核心内容

1. vue2.0/3.0 双向数据绑定实现原理：ES5
2. vue-cli 3.0 脚手架
   * 配置
   * 优化
3. vue基础
   * 双向数据绑定
   * template模板渲染语法和原理：vue-loader 虚拟DOM
   * 指令和自定义指令
   * method computed watch filters
   * class / style
   * 条件和列表渲染
   * 事件处理
   * 表单处理
   * 组件的prop
   * ref
   * 生命周期
   * slot
   * transition
   * 渲染函数和jsx
   * 插件编写
   * mixin
   * devtools
4. vue-router
   * 基础
   * 动态路由
   * 编程式导航
   * 命名路由和命名容器
   * 导航守卫
   * hash和浏览器路由
   * 路由原理
5. vuex 状态管理
   * state
   * getter
   * mutation
   * action
   * module
   * mapXxx
   * 实现原理
6. 单元测试
7. SSR服务器渲染
8. UI组件库

## React核心

1. create-react-app脚手架
   * 配置
   * 优化
2. react基础
   * JSX语法，虚拟DOM
   * 状态State
   * 属性Props
   * ref
   * 组件
   * 生命周期
   * PureComponent/Component
   * Hooks
3. react-rooter-dom
4. redux
   * redux
   * react-redux
   * 中间件 thurnk
5. TypeScript
6. UI组件
7. SSR服务器渲染

# Vue

1. Vue的双向数据绑定原理：

   * 使用**深克隆**将obj拷贝到newObj中，分别存储数据的当前值和更新的值

   * 使用ES5的`Object.defineProperty`对obj的属性的设置和获取进行拦截，将更新的值优先使用`set()`函数优先设置到newObj当中，再将newObj中的值渲染到DOM中

   * ```js
     let obj = {
         name:''
     }
     let newObj = JSON.parse(JSON.stringify(obj)) //使用...obj只能拷贝一层数据，更深层次仍然是引用数据
     Object.defineProperty(obj,'name',{
         get(){
             return newObj.name
         },
         set(newVal){ //在设置obj的时候同时改变newObj的name，触发修改dom视图更新
             if(newVal === newObj.name) return
             newObj.name = newVal
             observer()
         }
     })
     function observer(){
         span.innerHtml = newObj.name
         inputName.value = obj.name
     }
     inputName.oninput = function(){
         obj.name = this.value
     }
     ```

     对原始数据克隆

     * 需要分别给对象中每个属性设置监听，中途加入的属性无法被监听到，解决方案：ES6 Proxy对象代理

       ```js
       let obj={}
       obj = new Proxy(obj,{     //不需要担心中途加入的prop
           get(target,prop){
           	return target[prop]
           },
           set(target,prop,value){
               if(target[prop] === value) return;
           	target[prop] = value；
               observer();
           }
       })
       function observer(){
           span.innerHtml = newObj.name
           inputName.value = obj.name
       }
       inputName.oninput = function(){
           obj.name = this.value
       }
       ```

2. MVC(React)和MVVM(Vue)的区别

   1. MVVM模式的特点主要是数据的双向绑定，数据更改，视图变化，视图更改(监听)，数据变化
   2. MVC模式的特点是**模型数据**单向通知**视图**更改，而视图原则上不能更新模型数据
   3. React底层不支持双向绑定，但是可以以MVVM模式来进行手动设置监听器来实现数据双向绑定。
   4. Vue底层自带数据双向绑定`v-modle`

3. Vue基础面试讨论点：

   * 组件通信
     * 属性传递
     * 发布订阅，事件总线 ，$on / $ emit
     * Provide / inject
     * slot
     * $parent $children
     * 本地存储（解决所有组件之间的通信）
       * vuex
       * LocalStorage 持久化存储 4MB
       * Cookies 4KB
       * Session Storage：
         * 服务器设置`session` 在客户端请求后返回给客户端信息
         * 在响应头中，携带`set-cookie = 'connect sid'`，客户会把信息种植到本地`Cookie`中，且为只读
         * 客户端再次向服务器发送请求的时候，将默认在请求头中放置`Cookie`，将`connect sid`发送给数据


* * 借助使用C语言编写的`libuv`库实现
* 浏览器访问网页流程：
  * 输入url按下回车后
    1. DNS解析
       - 浏览器DNS缓存
       - 系统DNS缓存
       - 向DNS服务器发送DNS解析报文
    2. 获取到了IP，浏览器网络模块开始发送TCP三次握手的第一次报文
    3. 第三次报文携带HTTP请求报文
    4. 浏览器接收到响应报文
    5. 渲染页面
       1. HTML解析器通过解析HTML标签构建DOM树
       2. 

# React

1. React面试讨论点：

   * 组件通信
     * 属性Props 携带 回调函数
     * 发布订阅
     * redux
     * React.createContext 解决祖先和后代组件

* redux / react-redux 

# 基础算法

## 数组去重

* 使用ES6 Set：

  ```js
  function RemoveDuplicate(arr) {
      return [...new Set(arr)] // Array.from(new Set(arr))
  }
  ```

* 使用原生算法：

  ```js
  function RemoveDuplicate2(arr) {
      let arrCopy = DeepClone(arr)//深拷贝函数
      for (let i = arrCopy.length - 1; i > 0 ; i--) {
          for (let j = 0; j < i ; j++) {
              if (arrCopy[i] === arr[j]){
                  arrCopy.splice(i,1)
                  break;
              }
          }
      }
      return arrCopy;
  }
  ```

* 先排序，再进行相邻比较

  ```js
  function RemoveDuplicate3(arr) {
      let arrCopy = DeepClone(arr);
      arrCopy.sort((a,b)=> a-b)
      for (let i = arrCopy.length - 1; i > 0 ; i--) {
          if (arrCopy[i] === arrCopy[i-1]){
              arrCopy.splice(i-1,1)
          }
      }
      return arrCopy;
  }
  //使用正则
  function RemoveDuplicateReg(arr) {
      let arrCopy = DeepClone(arr);
      arrCopy.sort((a,b)=> a-b)
      arrCopy = arrCopy.join('@') + '@'
      let reg = /(\d+@)\1*/g;
      arrCopy = arrCopy.replace(reg,(group,subgroup) =>{
          console.log("group"+group,"subgroup" + subgroup)
          return subgroup
      })
      return arrCopy.slice(0,-1).split('@').map(elem => Number(elem));
  }
  ```

  

## 数组排序

* 冒泡排序

  ```js
  function bubbleSort(arr) {
      let temp = null;
      let completeFlag;
      for (let i = 0; i <arr.length ; i++) {
          completeFlag = true
          for (let j = 0; j <arr.length -1 -i ; j++) {
              if (arr[j] > arr[j+1]){
                  completeFlag = false;
                  temp = arr[j];
                  arr[j] = arr[j+1];
                  arr[j+1] = temp;
                  //使用ES6结构赋值进行值交换
                  //[arr[j],arr[j+1]] = [arr[j+1],arr[j]]
              }
          }
          if(completeFlag) break;
      }
      return arr
  }
  ```

* 插入排序

  ```js
  function insertSort(arr) {
      let i,j,temp
      for (i = 1; i <arr.length ; i++) {
          if (arr[i-1] > arr[i]){
              temp = arr[i];	//被插入的元素
              for (j = i - 1; j >=0 && arr[j] > temp; j--) {  
                      arr[j + 1] = arr[j]
              }
              // 已经跳出的循环的j，需要回到上一个位置进行插入
              arr[j + 1] = temp
          }
      }
      return arr;
  }
  ```

1. 实现数组扁平化

   * 使用ES6`Array.prototype.flat(Infinity)`扁平化任何维度的数组

     ```js
     let arr = [1, 2, [3, 4, [5, 6, [7, 8, [9, 10]]]],11];
     let result = arr.flat(infinity)
     ```

   * 使用`Array.prototype.toString()`方法，然后使用`split(',')`转换为字符串，此时每一项都为字符串类型，再使用map对每一项进行转换为数字类型

     ```js
     let arr = [1, 2, [3, 4, [5, 6, [7, 8, [9, 10]]]],11];
     let result = arr.toString().split(',').map(elem => Number(elem));
     ```

   * 使用正则表达式：

     ```js
     let arr = [1, 2, [3, 4, [5, 6, [7, 8, [9, 10]]]],11];
     let result =JSON.parse("["+JSON.stringify(arr2).replace(/(\[|\])/g,'')+"]");
     ```

   * 使用数组的`some()`方法对数组内元素进行反复测试，判断是否为数组：

     ```js
     function flatArray(arr) {
         while (arr.some(elem => Array.isArray(elem))){
              
         }
         return arr;
     }
     ```

   * 使用递归类型判断

     ```js
     function streamrollArray(array){
         const newArray = []
         array.forEach(item => {
             if(item instanceof Array){
                 newArray.push(...streamrollArray(item))
             } else {
                 newArray.push(item)
             }
         })
         return newArray
     }
     ```

   

   4. 实现斐波拉契数列

      ```js
      //递归，n不能超过75025
      function FibonacciArray(n,a1=1,a2=1) {
          if (n <=1) return a2;
          return FibonacciArray(n-1,a2,a1+a2 );
      }
      // 非递归
      function fibonacci (n,f1=1,f2=1) {
       let f = 0
        if (n<=2) {
          return 1
        }
        for (let i = 2; i <=n ; i++) {
          f = f1 + f2
          f1 = f2
          f2 = f
        }
        return f
      }
      ```

   5. 输入一个数，求一组连续的数字的和等于这个数字

      ```js
      function GetContinuousSumArray(n) {
          let middle = Math.ceil(n);
          let result = [];
          let tempArr = [];
          let tempSum = 0;
          for (let i = 1; i <middle ; i++) { //主循环，从1开始试探
              tempSum = 0;
              tempArr=[];
              for (let j = i; j <middle ; j++) { 
                  //子循环，从变化的开始位置向后累加
               if (tempSum < n){
                      tempSum += j;
                      tempArr.push(j)
                  }
                  //如果累加到等于n 立即跳出当前子循环
                  if (tempSum === n){
                      result.push(tempArr)
                      break;
                  }
              }
          }
          return result;
      }
      ```

   ## 求斐波那契数组中的奇数之和

   在这道题目中，我们需要写一个函数，参数为一个正整数`num`。它的作用是计算斐波那契数列中，小于或等于`num`的奇数之和。

   斐波那契数列中，第一和第二个数字都是 1，后面的每个数字由之前两数相加得出。斐波那契数列的前六个数字分别为：1、1、2、3、5、8。

   比如，`sumFibs(10)`应该返回`10`。因为斐波那契数列中，比`10`小的数字只有 1、1、3、5。

   * `sumFibs(1)`应该返回一个数字。

   * `sumFibs(1000)`应该返回 1785。

   * `sumFibs(4000000)`应该返回 4613732。

   * `sumFibs(4)`应该返回 5。

   * `sumFibs(75024)`应该返回 60696。

   * `sumFibs(75025)`应该返回 135721。

   ```js
   function sumFibs(num) {
     var prevNumber = 0; // 前一号位
     var currNumber = 1; // 当前位数字
     var result = 0;
     while (currNumber <= num) { //当前位的数字小于num的时候，才进行循环
       if (currNumber % 2 !== 0) { // 如果是奇数，累加
         result += currNumber;
       }
       currNumber += prevNumber; // 后一个位的值为 当前位的值加上前一位的值
       prevNumber = currNumber - prevNumber; // 前一位的值为 新的当前位减去自身，得到新的前一位
     }
     return result;
   }
   ```


   * 对所有素数求和

     在这道题目中，我们需要写一个函数，它接收一个数字参数`num`，返回值为不大于这个数字的所有质数之和。

     质数是大于 1 且仅可以被 1 和自己整除的数。比如，2 就是一个质数，因为它只可以被 1 和 2（它本身）整除。

     注意，传入函数的`num`不一定是质数。

     ```js
     function sumPrimes(num) {
       let sum = 0
       let flag = true;
       for(let currentNum = 2; currentNum <= num ; currentNum++){
         for(let i = 2 ; i<currentNum;i++ ){
           if(currentNum % i === 0){
             flag = false
           }
         }
         if (flag){
           sum+=currentNum
           console.log(currentNum)
         }
         flag = true
       }
       return sum
     }
     console.log(sumPrimes(10)) ;
     ```

     

## 判断是否符合 USD 格式

题目描述

给定字符串 string，检查其是否符合美元书写格式
1、以 $ 开始
2、整数部分，从个位起，满 3 个数字用 , 分隔
3、如果为小数，则小数部分长度为 2
4、正确的格式如：$1,023,032.03 或者 $2.03，错误的格式如：$3,432,12.12 或者 $34,344.3

输入

```js
'$20,933,209.93'
```

输出

```js
true
```

解决方案：

先编写正则表达式：

```js
/^\$\d{1,3}(\,\d{3})*(\.\d{2})?$/
// ^\$\d{1,3} 匹配以$开始，后跟1到3位的数字开头 \$为$的转义
// ()内的内容为匹配一个规定的模板字符串，
// (\,\d{3})* 匹配  ,xxx 类型的 一组字符串0次或者多次 x必须为3个数字  \,为,的转意
// (\.\d{2})? 匹配  .xx 类型的一组字符串 0次或者1次   \.为.的转意
```



```js
function isUSD(str) {
	let reg = /^\$\d{1,3}(\,\d{3})*(\.\d{2})?/;
    return reg.test(str);
}
```



## 横杠命名转换为驼峰命名

首先将横杠命名的字符串使用split("-")进行分割成字符串数组

```js
function ConvertToCamelCase(string) {
    function HyphenLowerToUpper(match) {
        return match.toUpperCase().replace('-','');
        //或者 return match.toUpperCase()..substring(1);
        //或者 return match.toUpperCase()..substr(1);
    }
    return string.replace(/-[a-z]/g, HyphenLowerToUpper);
}

```

* substring（str0，str1）表示从字符串的第几个截取到第几个，如果第一个参数写了，第二个参数没有写的话，那么代表着从第一个参数的索引开始截取到字符串的末尾。 参数值可以是正值，也可以是负值。但是和slice不同的是，slice中参数是负值，那么倒着开始数，而substring参数是负值的话，代表着往前数几位，它是不能倒着开始数的。最后返回的是一个截取的新数组。
* substr （开始的索引，长度），表示从第几个开始，一直往后截取第二个参数的大小的长度。如果参数二是arr.length（数组的长度）的话，那么代表着从第一个参数的索引开始，一直截取到数组的最后一位。

使用replace()的回调函数处理匹配项

```js
function convert1 (string) {
  return string.replace(/-[a-z]/ig,item => (item[1].toUpperCase()))

}

console.log(convert1('up-to-date'))

function convert2 (string) {
  return string.replace(/[A-Z]/g,(item,idx) => {
    if (idx === 0){
      return item.toLowerCase()
    } else {
      return  '-'+ item.toLowerCase()
    }
  }).replace(/-?/,'')

}
console.log(convert2('UpToDate'))

```

## 函数作为参数的this

```js
var number = 10;
function fn() {
  console.log(this.number);
}
var obj = {
  number: 2,
  show: function(fn) {
    this.number = 3;
    fn();
    arguments[0]();
  }
};
obj.show(fn);
```

第一个函数`fn`被调用的时候，这里的`fn`中的this指向的window

第二个`arguments[0]`被调用的时候，相当于`arguments.0`，相当于调用数组的第0个元素，而且`arguments`为一个伪数组，且为对象数组

## 输出打印结果

```js
var name = 'global'
var obj = {
    name: 'local', 
    foo: function () {
        this.name = 'foo'
    }.bind(window)
}
var bar = new obj.foo()  //实际上执行的是 new function(){}.bind(window),而new比bind优先级高，所以，bind是在构造函数执行之后，即使用new和foo内的函数产生新对象之后，才对foo内的function执行bind方法
setTimeout(function () {
    console.log(window.name)
}, 0)
console.log(bar.name)
var bar3 = bar2 = bar
bar2.name = 'foo2'
console.log(bar3.name)
```

## ES6转换为ES5

```js
class Person {
    constructor (name) {
        this.name = name;
    }
    greet () {
        console.log(`Hi, my name is ${this.name}`);
    }
    greetDelay (time) {
        setTimeout(() => {
            console.log(`Hi, my name is ${this.name}`);
        }, time);
    }
}
```

```js
function Person(name){
    this.name = name
}
Person.prototype.greet = function (){
    console.log('Hi, my name is' + this.name)
}
Person.prototype.greetDelay = function (time){
    var self = this
    setTimeout( function () {
        console.log('Hi, my name is '+ this.name);
    },time)
}
```

