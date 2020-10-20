---
title: 使用CSS+JS实现一个简单的立方体(可旋转)
tags:
- "CSS"
- "JavaScript"
- "HTML"
- "教程"
categories: 
 - JavaScript
 
keywords: "JavaScript, CSS, HTML "
cover: https://gitee.com/EricReeves/proxy/raw/master/img/image-20200902163752855.png
---
使用CSS3的景深与三维变换+JavaScript实现一个可旋转的立方体（x,y两轴旋转）

HTML部分：

首先先构建立方体的DOM结构：

* 将6个面使用cube包裹，目的很简单，为了给子元素开启3d景深，
* 使用wrap再次包裹，wrap的目的是为了包装整个立方体，可控制缩放

```html
<div class="wrap">
    <div class="cube">
        <div class="cube-face"></div>
        <div class="cube-face"></div>
        <div class="cube-face"></div>
        <div class="cube-face"></div>
        <div class="cube-face"></div>
        <div class="cube-face"></div>
    </div>
</div>
```

其对应样式如下：(使用less书写)

html与body的样式，使用 overflow: hidden; 隐藏垂直滚动条，防止移动端的滑动

```less
html,
body {
    margin: 0;
    padding: 0;
    height: 100%;
    overflow: hidden;
    background-color: #dfe6e9;
}

```

.wrap:将其设置为100 x 100 px的初始大小，并水平垂直居中，默认将其放大两倍

```less
.wrap {
    width: 100px;
    height: 100px;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%) scale(2);
}
```

.cube : 将其设置为100 x 100 px 的初始大小，并使用另一种水平垂直居中的解决办法，且开启内部元素的3D变换

```less
.cube {
    width: 100px;
    height: 100px;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
    transform-style: preserve-3d;
    transform-origin: center center; /*设置旋转中心为立方体的重心*/ 
    transform: rotateX(-30deg) rotateY(30deg); /*默认将立方体选择一定角度*/ 
}
```

在编写6个面之前，我们需要了解浏览器页面的3D坐标系

![css3d](https://gitee.com/EricReeves/proxy/raw/master/img/coordinate_axis.png)

从+Z 到 -Z 方向，为用户看向浏览器屏幕的方向，意味着如果不开启3D变换，那么一切元素都是按照Z轴进行选择

从+Y 到 -Y 方向，为屏幕从下到上的方向，意味着元素会按照Y轴左右旋转

从+X 到 -X 方向，为屏幕从左到右的方向，意味着元素可以按照X轴上下翻转

了解了坐标系，那么可以编写css代码了

首先我们需要将6个面的公共样式提取出，然后分别为每个面定制选择的角度，以及选择的原点，在这里所有的`animation: first 1.5s .8s forwards;`可以暂时不编写，因为这是附加的入场动画效果

```less
.cube-face {
    width: 100px;
    height: 100px;
    box-sizing: border-box;
    position: absolute;
    text-align: center;
    font-size: 40px;
    line-height: 100px;
    opacity: 0;
    user-select: none;

    &:nth-child(1) {
        top: -100%;
        transform: rotateX(90deg) translateY(50px) translateZ(150px);
        transform-origin: bottom;
        animation: first 1.5s .8s forwards;
    }

    &:nth-child(2) {
        bottom: -100%;
        transform-origin: top;
        transform: rotateX(-90deg) translateY(-50px) translateZ(150px);
        animation: second 1.5s 1s forwards;
    }

    &:nth-child(3) {
        left: -100%;
        transform-origin: right;
        transform: rotateY(-90deg) translateX(50px) translateZ(150px);
        animation: third 1.5s .4s forwards;
    }

    &:nth-child(4) {
        right: -100%;
        transform-origin: left;
        transform: rotateY(90deg) translateX(-50px) translateZ(150px);
        animation: fourth 1.5s .6s forwards;
    }

    &:nth-child(5) {
        transform: rotateY(180deg) translateZ(150px);
        animation: fifth 1.5s .2s forwards;
    }

    &:nth-child(6) {
        transform: translateZ(150px);
        animation: sixth 1.5s forwards;
    }
}
```

如果你按照我上方的代码编写，你就会发现结果是这样的一个效果：

![image-20200902163752855](https://gitee.com/EricReeves/proxy/raw/master/img/image-20200902163752855.png)

这是因为我们最终的目的是要使用@keyframe来添加进场动画，动画设置forward为播放一次，且在结束时停止，接下来编写6个css动画：

```less
@keyframes first {
    100%{
        transform: rotateX(90deg) translateY(50px) ;
        opacity: 1;
    }
}
@keyframes second {
    100%{
        transform: rotateX(-90deg) translateY(-50px);
        opacity: 1;

    }
}
@keyframes third {
    100%{
        transform: rotateY(-90deg) translateX(50px) ;
        opacity: 1;
    }
}
@keyframes fourth {
    100%{
        transform: rotateY(90deg) translateX(-50px);
        opacity: 1;
    }
}
@keyframes fifth {
    100%{
        transform: rotateY(180deg) translateZ(50px);
        opacity: 1;
    }
}
@keyframes sixth {
    100%{
        transform: translateZ(50px);
        opacity: 1;
    }
}
```

每个动画的最终位置就是每个面的正确位置，最后为每个面加上延迟动画即可，这时你可以看到每一个面交错入场，并搭建成一个立方体的动画效果

此时需要另外使用一个透明的覆盖层，改变z-index，覆盖整个wrap，用于旋转时的防止立方体元素获得鼠标焦点

![image-20200902155629492](https://gitee.com/EricReeves/proxy/raw/master/img/image-20200902155629492.png)

现在布局以及样式都以及准备完毕了，接下来就进入到JavaScript的编写

首先获取需要DOM元素

```js
let wrap = document.querySelector(".wrap");
let cube = document.querySelector(".cube");
let cover = document.querySelector(".cover");
let tip = document.querySelector(".tip > span")
let cubeFace = document.querySelectorAll(".cube-face")
```

创建需要的变量

```js
let keydownPosX;  //鼠标按下时在在浏览器窗口上的X坐标
let keydownPosY;  //鼠标按下时在浏览器窗口上的Y坐标
let currentDegX = 0; //鼠标当前在浏览器窗口上的X坐标
let currentDegY = 0; //鼠标当前在浏览器窗口上的Y坐标
let stopDegX = 30;  //上一次X轴旋转的角度，此处我们默认旋转30度，以便展现立方体
let stopDegY = -30; //上一次Y轴旋转的角度
let timer;	// 自动旋转定时器
```

设置一个定时器，延时旋转立方体

```js
setTimeout(function() {
    timer = setInterval(function() {
        stopDegX += .2;
        cube.style.transform = `rotateX(${stopDegY}deg) rotateY(${stopDegX}deg)`;
    }, 16)
}, 800)
```

监听鼠标在cover覆盖层上的事件：分别是按下时，移动时，抬起时

```js
cover.addEventListener("mousedown", mousedown)
cover.addEventListener("mouseup", mouseup)
cover.addEventListener("mouseout", mouseup)
```

编写按下时的回调函数：立即清除定时器，记录坐标

```js
function mousedown(ev) {
    clearInterval(timer);
    keydownPosX = ev.offsetX;
    keydownPosY = ev.offsetY;
    cover.addEventListener("mousemove", drag);
}
```

编写拖拽与鼠标抬起事件的回调函数

```js
function drag(ev) { 
    //计算鼠标坐标的变化量，并减小旋转速度，即将鼠标移动的坐标除以3，可调节
    currentDegX = (ev.offsetX - keydownPosX) / 3; 
    currentDegY = -(ev.offsetY - keydownPosY) / 3;
    // 将坐标变化量加上之前的旋转角度，设置到transform中
    cube.style.transform = `rotateX(${stopDegY + currentDegY}deg) rotateY(${stopDegX + currentDegX}deg)`;
}
function mouseup(ev) {
    stopDegX += currentDegX // 更新增加的角度
    stopDegY += currentDegY
    currentDegX = 0; // 将当前鼠标位置数据清空
    currentDegY = 0;
    // 松开后移除鼠标移动事件
    cover.removeEventListener("mousemove", drag);
}
```

到此，可旋转立方体基本功能已经完成，代码相对简单，只涉及到事件对象的信息获取以及对元素的样式操作

最后附上Demo地址： [cube](https://EricReevess.github.io/proxy/web/Cube3Dv2/) (使用PC端浏览器打开)

