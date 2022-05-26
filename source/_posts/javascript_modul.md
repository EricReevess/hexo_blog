---
title: JavaScript 模块化规范对比
tags:
- "JavaScript模块"
- "模块化规范"
- "学习笔记"
categories: 
 - JavaScript
 
keywords: "JavaScript, 模块化 "
cover: https://pic4.zhimg.com/v2-5b63fa69f124ccb13b9e248ecd0611b4_1200x500.jpg
---

# 模块化的好处
* 避免变量命名冲突
* 更好的分离
* 更高的维护性
* 高可维护性

# 模块化模式
全局模式：直接在js模块文件中声明变量    外界能够随意修改其值，不安全  
命名空间模式：在js模块文件中均为封装的对象  外界能够随意修改其值，不安全  
IIFE匿名函数自调用模式(闭包)：js模块文件中使用一个立即执行函数进行包装，在内部暴露相应函数，立即执行的函数参数可以为window 
```js
(function(window){
    function foo(){
        //.....
    }
    window.model1 = {foo}; // 将函数以对象形式传入到window对象的属性中，等价于{foo：foo}
})(window)
```
IIFE增强模式：在IIFE基础上 在立即函数的参数中加入代码依赖，例如使用jQuery
```js
(function(window,$){
    function changeBg(){
        $('body').css({
            background : 'red',
            //.....
        })
    }
    window.model1 = {changeBg}; // 将函数以对象形式传入到window对象的属性中，等价于{foo：foo}
})(window,jQuery)
```
## 原生JS进行模块化
使用原生的JS，以匿名函数自调用模式(闭包)的形式进行模块化，实参括号内的参数均为window对象内部属性
```js
//module1.js ，没有任何依赖的模块
(function(window){
    let msg = 'this is a message!';
    function getMessage(){
        return msg;
    }
    //使用window将getMessage进行暴露
    window.message = {getMessage}
})(window)
//module2.js，依赖于获取数据的module1.js
(function(window,message){
    let msg = 'this is a message!';
    function logMessage(){
        console.log(message.getMessage())
    }
    window.showMessage = { logMessage }
})(window, message)
//主模块msg.js，将使用module2.js和module1.js,不需要使用window来暴露模块
(function(showMessage){
    showMessage.logMessage();
})(showMessage)
```
原生JS的模块引用必须严格遵守模块的引用顺序
```html
<script src="./modules/module1.js"></script>
<script src="./modules/module2.js"></script>
<script src="src/js/msg.js"></script>
```


# 模块化规范
## CommonJS
NodeJS使用此语法规范，不存在顶级对象window
### 规范
每一个文件都可以当做一个模块  
服务器(NodeJS)端的模块都是运行时同步加载的  
浏览器端的模块需要提交打包处理  

#### 暴露模块
暴露模块本质是吧所有对象的引用保存在exports对象中
```js
module.exports = value
exports.fun1 = fun1
```
#### 引入并模块
第三方模块直接传入模块名，自定义则传入相对路径，注意，CommandJs中，需要等待require加载完模块后，才能往后执行代码
```js
let module = require(相对路径/模块名);
module.fun1();
```
## AMD 规范
全名为 异步模块定义 
### 规范
专用于浏览器端，模块的加载是异步的
#### 定义暴露模块
定义不存在依赖关系的模块
```js
defind(function(){
    return 模块
})
```
定义有依赖的模块,传入依赖模块名数组、回调函数
```js
// 传入依赖模块名，将依赖的模块注入到回调函数参数中
defind(['module1','module2'], function(module1, module2){
    return 模块
})
```
#### 引入模块
```js
require(['module1', 'module2'], function(module1, module2){
    //通过参数使用模块 
})
```
###  AMD规范引入模块实例(需要在lib文件夹下引入Require.js)
```js
// 定义无依赖的模块 module1.js
define(function(){
    let msg = 'this is a message';
    function getMessage(){
        return msg;
    }
    // 暴露一个对象，内容为一个函数
    return {getMessage} 
})
// 定义依赖于module1.js 的 module2.js
define('module1', function(module1){
    function showMessage(){
        console.log(module1.getMessage())
    }
    return {showMessage};
})
//  定义主模块，引用2个子模块
(function(){
    requirejs(['module1', 'module2'], function(module1, module2 ){
        module2.showMessage();
    })
})()
```

## ES6规范
### 规范
ES6的依赖模块需要编译打包处理，常使用webpack进行打包
#### 暴露模块
均为暴露一个对象
```js
export function fun1(){
    // ...
}
export function fun2(){
    //... 
}
// 或者统一暴露
function fun1(){//...}
function fun2(){//...}
export {fun1,fun2, ...}
//默认暴露
export default () => {
    //默认暴露一个箭头函数
}
export default function()  {
    //默认暴露普通函数
}

```
#### 引入模块
引入一般暴露的模块均使用对象形式解构赋值
引入默认暴露的模块可自定义引用名 

```js
import {fun1} from '模块js相对路径'
import arrowFunc from '默认暴露模块js相对路径'
fun1()
arrowFunc()
```
注意在HTML中引入的时候，需要将脚本标签类型设定为module
```html
<script type="module" src="./src/js/msg.js"></script>
```

## 编辑器相关

写入@ 注释以给编译器加上参数提示

```js
/**
 * 绿茶准备过程
 * @return {string} 一杯绿茶
 **/
const prepareGreenTea = () => 'greenTea';

/**
 * 备红茶准备过程
 * @return {string} 一杯红茶
 **/
const prepareBlackTea = () => 'blackTea';

/**
 * 获得一定数量的茶
 * @param {function():string} prepareTea 茶的类型
 * @param {number} numOfCups 需要茶的数量
 * @return {Array<string>} 给定的茶
 **/
const getTea = (prepareTea, numOfCups) => {
  const teaCups = [];

  for(let cups = 1; cups <= numOfCups; cups += 1) {
    const teaCup = prepareTea();
    teaCups.push(teaCup);
  }

  return teaCups;
};
```

