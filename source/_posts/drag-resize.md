---
title: Javascript 实现div的拖拽与变形
date: 2020-08-20
tags:
- "JavaScript"
- "CSS"
- "拖拽"
categories: 
 - JavaScript

description: "解析ES6中对异步操作的解决方案——Promise，并使用代码实现原生Promise的功能"
keywords: "前端, JavaScript, DOM, CSS，事件"
cover: https://raw.githubusercontent.com/EricReevess/proxy/master/img/drag-resize.png
---

# 封装一个简单的拖拽+变形的div的模块

在编写拖拽与变形之前，我们需要知道DOM与元素大小相关的属性

![elem-size](https://raw.githubusercontent.com/EricReevess/proxy/master/img/EED5E2105E90D3063C11A0116A427264.png)

需要的参数：被拖动的元素css选择器elemSelector，容器元素选择器wrapSelector

## 拖拽+边界指针提示部分

```js
function dragElement (elemSelector, wrapSelector) {
  let elemDom = document.querySelector(elemSelector)
  let wrapDom = document.querySelector(wrapSelector)
  /* 分别需要 
   鼠标左键按下时相对于被拖动元素内部的坐标mouseDownOffsetX, mouseDownOffsetY
   鼠标指针相对于body可视区域的坐标mouseMoveClientX, mouseMoveClientY
   被拖动元素当前的left 与 top 偏移量 elemLeft, elemTop
  */
  let { offsetHeight, offsetWidth } = elemDom
  let mouseDownOffsetX, mouseDownOffsetY, mouseMoveClientX, mouseMoveClientY, elemLeft, elemTop
  // 将被拖动元素设置为绝对定位，并且将容器元素设置为相对定位，
  wrapDom.style.position = 'relative'
  elemDom.style.position = 'absolute'
  // 监听鼠标在元素内部的移动事件，为不同区域变换不同的指针类型
  elemDom.addEventListener('mousemove', elemMouseMove)

  // 鼠标按下时的事件，记录按下的坐标，并同时添加在容器内部移动鼠标，松开鼠标，离开区域的监听事件，其中后两个的目的是移除鼠标松开和离开容器时的鼠标移动监听
  function onElemMousedown (e) {
    mouseMoveClientX = e.clientX
    mouseMoveClientY = e.clientY
    wrapDom.addEventListener('mousemove', mouseMoveOnWrap)
    wrapDom.addEventListener('mouseup', removeListener)
    wrapDom.addEventListener('mouseleave', removeListener)
  }

  // 鼠标在容器上移动的监听事件，实时获取鼠标相对于窗口的坐标，实时改变拖动元素的位置
  function mouseMoveOnWrap (e) {
    clientX = e.clientX
    clientY = e.clientY
    elemLeft = mouseMoveClientX - mouseDownOffsetX
    elemTop = mouseMoveClientY - mouseDownOffsetY
    elemDom.style.left = elemLeft + 'px'
    elemDom.style.top = elemTop + 'px'

  }

  function elemMouseMove (e) {
    // 此处内部的offsetX, offsetY记录在移动过程中在被拖动元素内部的位置
    const { offsetX, offsetY } = e
    // 在元素被变形的时候更新检测宽度和高度，放置在拖动过程中鼠标形状发生错误
    offsetHeight = e.target.offsetHeight
    offsetWidth = e.target.offsetWidth
    //如果 鼠标偏移量在一个内部范围内，即内圈范围，则出现move指针形状，并添加鼠标按下监听事件
    if (offsetX > 6 && offsetX < offsetWidth - 6 && offsetY > 6 && offsetY < offsetHeight - 6) {
      e.target.style.cursor = 'move'
      elemDom.addEventListener('mousedown', onElemMousedown)
      return null // 此时返回，不做执行移除按下事件
    // 一下是对不同区域变换不同的鼠标指针
    } else if (offsetY > -5 && offsetY < 5) {
      if (offsetX > -5 && offsetX < 5) {
        e.target.style.cursor = 'nw-resize'
      } else if (offsetX > offsetWidth - 5 && offsetX < offsetWidth + 5) {
        e.target.style.cursor = 'ne-resize'
      } else {
        e.target.style.cursor = 'n-resize'
      }
    } else if (offsetY > offsetHeight - 5 && offsetY < offsetHeight + 5) {
      if (offsetX > -5 && offsetX < 5) {
        e.target.style.cursor = 'sw-resize'
      } else if (offsetX > offsetWidth - 5 && offsetX < offsetWidth + 5) {
        e.target.style.cursor = 'se-resize'
      } else {
        e.target.style.cursor = 'n-resize'
      }
    } else if (offsetX > -5 && offsetX < 5 || offsetX > offsetWidth - 5 && offsetX < offsetWidth + 5) {
      e.target.style.cursor = 'e-resize'
    } else {
      e.target.style.cursor = 'default'
    }
    // 由于这些区域都不是拖动的有效区域，在此最好移除鼠标按下事件
    elemDom.removeEventListener('mousedown', onElemMousedown)
}

// 移除的事件
function removeListener (e) {
    e.target.style.cursor = 'default'
    wrapDom.removeEventListener('mousemove', mouseMoveOnWrap)
    wrapDom.removeEventListener('mouseup', arguments.callee) 
    wrapDom.removeEventListener('mouseleave', arguments.callee)
    // 此处调用arguments.callee可将mouseup，mouseleave的监听移除
  }
}

```

## 边缘拖拽变形部分

所需参数，被拖动的元素选择器elemSelector，拖动元素的最小宽度和高度

```js
function resizeRect (elemSelector, minWidth, minHeight) {
  /*
  需要的变量鼠标在按下时的浏览器窗口坐标clientClickDownX, clientClickDownY
  鼠标在拖动过程中的浏览器窗口坐标currentClientX, currentClientY
  鼠标在拖动元素内部的相对坐标rectMouseOffsetX, rectMouseOffsetY
  首先初始化记录拖动元素在点击之前的宽度elemWidth和高度elemHeight以及top值和left值
  并设置元素的最小宽度和高度 minWidth，minHeight
  */
  let clientClickDownX, clientClickDownY, currentClientX, currentClientY, rectMouseOffsetX, rectMouseOffsetY
  let elemDom = document.querySelector(elemSelector)
  let elemHeight = elemDom.offsetHeight
  let elemWidth = elemDom.offsetWidth
  let elemTop = elemDom.offsetTop
  let elemLeft = elemDom.offsetLeft
  elemDom.style.minHeight = minHeight + 'px'
  elemDom.style.minWidth = minWidth + 'px'
  // 在按下鼠标的时候，向全局变量记录下按下的浏览器窗口坐标clientClickDown以及元素内部坐标rectMouseOffset
  elemDom.addEventListener('mousedown', function (e) {
    clientClickDownX = e.clientX
    clientClickDownY = e.clientY
    rectMouseOffsetX = e.offsetX
    rectMouseOffsetY = e.offsetY
  // 对按下的位置进行判断，分别为上下左右4条边，同时也将4个交也进行了处理，
    if (rectMouseOffsetY > 0 && rectMouseOffsetY < 5) {
      document.addEventListener('mousemove', rectTopResize)
    }
    if (rectMouseOffsetY < elemHeight + 5 && rectMouseOffsetY > elemHeight - 5) {
      document.addEventListener('mousemove', rectBottomResize)
    }
    if (rectMouseOffsetX > -5 && rectMouseOffsetX < 5) {
      document.addEventListener('mousemove', rectLeftResize)
    }
    if (rectMouseOffsetX < elemWidth + 5 && rectMouseOffsetX > elemWidth - 5) {
      document.addEventListener('mousemove', rectRightResize)
    }
  })
  // 在鼠标抬起时，存储当前元素的宽高以及定位，为下一次判断与变形做准备，同时移除4个可能的事件监听
  document.addEventListener('mouseup', function () {
    elemTop = elemDom.offsetTop
    elemLeft = elemDom.offsetLeft
    elemHeight = elemDom.offsetHeight
    elemWidth = elemDom.offsetWidth
    document.removeEventListener('mousemove', rectTopResize)
    document.removeEventListener('mousemove', rectBottomResize)
    document.removeEventListener('mousemove', rectLeftResize)
    document.removeEventListener('mousemove', rectRightResize)

  })
  
  // 顶部变形，顶部的型变量等于 鼠标按下的Y轴坐标clientClickDownY- 鼠标当前的Y轴坐标currentClientY，最终的高度等于 上一次的高度elemHeight + 变化量 ，同时如果要将元素保持在原位（对边或者对角线不动）,则需要改变对应的定位值，top定位值则等于 当前的定位值elemTop - （鼠标按下的Y轴坐标clientClickDownY- 鼠标当前的Y轴坐标currentClientY），左侧变化同理
  function rectTopResize (e) {
    currentClientY = e.clientY
    elemDom.style.height = elemHeight + (clientClickDownY - currentClientY) + 'px'
    if (elemDom.offsetHeight === minHeight) {
      elemDom.style.top = elemDom.offsetTop + 'px'
    } else {
      elemDom.style.top = elemTop - (clientClickDownY - currentClientY) + 'px'
    }

  }
  // 底部变形则不需要改变top值，因为div的高度增长默认是向下生长，右侧同理
  function rectBottomResize (e) {
    currentClientY = e.clientY
    elemDom.style.height = elemHeight + (currentClientY - clientClickDownY) + 'px'
  }

  function rectLeftResize (e) {
    currentClientX = e.clientX
    elemDom.style.width = elemWidth + (clientClickDownX - currentClientX) + 'px'
    if (elemDom.offsetWidth === minWidth) {
      elemDom.style.left = elemDom.offsetLeft + 'px'
    } else {
      elemDom.style.left = elemLeft - (clientClickDownX - currentClientX) + 'px'
    }
  }

  function rectRightResize (e) {
    currentClientX = e.clientX
    elemDom.style.width = elemWidth + (currentClientX - clientClickDownX) + 'px'
  }
}
```

## 暴露模块 

使用匿名函数自调用(IIFE)，将函数挂载到windows中，成为全局方法,参数minWidth和minHeight默认为5px

```js
(function (window) {
  window.dragResize = function (elemSelector, wrapSelector, minWidth = 5, minHeight = 5) {
    dragElement(elemSelector, wrapSelector)
    resizeRect(elemSelector, minWidth, minHeight)
  }
})(window)
```

最终的效果，请访问[drag-resize](https://EricReevess.github.io/webpage-demos/drag-resize/)




