---
title: webpack 4 打包入门
tags:
- "webpack"
- "前端兼容性"
- ""
- "JavaScript"

categories: 
 - webpack

description: "解析ES6中对异步操作的解决方案——Promise，并使用代码实现原生Promise的功能"
keywords: "前端, JavaScript, DOM, CSS，事件"
cover: https://pic3.zhimg.com/v2-fd4298513141b363287e8358a93804fb_1440w.jpg?source=172ae18b
---

> webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

## 安装

使用npm全局安装

> npm install webpack webpack-cli webpack-dev-server -g

进入项目根目录进行局部安装

> npm install webpack webpack-cli webpack-dev-server 

## 入口(entry)

入口起点(entry point)指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

我们在开发环境下的项目根目录中创建`webpack.dev.js`，对webpack进行出口配置

```js
// ./config/webpack.dev.js
module.exports = {
    entry : 'main.js'
}
```

可以使用数组包含多个入口文件

```js
module.exports = {
    entry: {
        main: ['./src/main.js']
    },
}

```

每个依赖项随即被处理，最后输出到output设置的指定的 的文件中

## 打包环境

与webpack3 不同，在`webpack.dev.js`中新增了一个`mode`的选项，即选择打包的模式，development（开发环境）production（生产环境），两者的区别很简单，生产环境会对代码进行压缩，即去除所有代码缩进

```js
module.exports = {
    entry: {
        main: ['./src/main.js']
    },
    mode: 'production' 
}
```



## 出口(output)

出口是指示将零散的模块汇入到哪一个文件中，出口中设置的文件将包含所有需要的依赖，这里我们使用node的path模块来解析目录

`filename`：指定最终打包生成的js文件名，可以使用[name]获取入口js的文件名

`path`：指定输出的目录

`publicPath`：指定静态资源的根路径，意味着有时候静态资源需要从外部加载，那么要将`publicPath`指定为一个外部域

```js
const path = require('path')
module.exports = {
    entry: {
        main: ['./src/main.js']
    },
    mode: 'production' ，
    output: {
        filename: '[name]-bundle.js', // 使用
        path: path.resolve(__dirname, '../dist'), 
        publicPath: '/',
	},
}
```

## 开发环境调试服务器

在开发环境中使用webpack并进行调试，并期望进行即时打包与错误提示，那么我们可以使用一下选项

`contentBase`: 指定服务器读取文件的根目录
`overlay`: 布尔值，开启错误提示
`port`: 自定义端口号

```js
const path = require('path')
module.exports = {
    entry: {
        main: ['./src/main.js']
    },
    mode: 'production' ，
    output: {
        filename: '[name]-bundle.js', // 使用
        path: path.resolve(__dirname, '../dist'), 
        publicPath: '/',
	},
    devServer: {
        contentBase: 'dist',
        overlay: true, // 为错误显示提示信息
        port: 9000
    },
}
```



## 打包静态资源

webpack需要使用几个额外的模块来对css，图片，html文件进行打包处理

需要执行一下命令，执行局部安装

> npm install style-loader css-loader file-loader extract-loader html-loader

style-loader：负责将打包后生成的css模块以`<style>`标签的形式插入到html中

css-loader：负责打包所有css样式

file-loader：负责找到所需文件资源，并返回其public中的URL

extract-loader ：它的主要用例是解决来自各自装载器的 HTML 和 CSS 内的 URL

html-loader：将 HTML 导出为字符串。当编译器要求时，HTML 被最小化（去除缩进）

我们需要在`./config/webpack.dev.js`中继续添加一个module选项

```js
const path = require('path')
module.exports = {
    entry: {
        main: ['./src/main.js']
    },
    mode: 'production' ，
    output: {
        filename: '[name]-bundle.js', // 使用
        path: path.resolve(__dirname, '../dist'), 
        publicPath: '/',
	},
    devServer: {
        contentBase: 'dist',
        overlay: true, // 为错误显示提示信息
        port: 9000
    },
    module: {
        rules: [{
                // css-loaders,找到所有的css文件
                test: /\.css$/,
                // 对上面匹配到的文件使用一下模块进行打包
                use: [
                    {
                        loader: 'style-loader'
                    },
                    {
                        loader: 'css-loader'
                    },
                ]
            },
            {
                test: /\.html$/,
                // 对上面匹配到的文件使用一下模块进行打包
                use: [
                    {
                        loader: 'file-loader',
                        options: {
                            name: '[name].html' // 指定打包后的html文件名称，这里设置[name]就是打包之前html的名称
                        }
                    },
                    { // 将html和js文件分开打包
                        loader: 'extract-loader',
                    },
                    {
                        loader: 'html-loader'
                    }
                ]
            },
            // 配置图片资源加载器image-loader
            {
                test: /\.(jpg|git|png)/,
                
                use: [
                    {
                        loader: 'file-loader',
                        // 对上面匹配到的文件使用一下模块进行打包
                        options: {
                            name: 'images/[name]-[hash:8].[ext]'
                    }
                }]
            }
        ]
    }
}
```

## ES语法兼容

我们知道ES6相对于ES5新增了一些语法方面的内容，这可能导致使用ES6编写的js文件在某些不支持ES6的浏览器上无法被正常执行，所以Babel就出现了，他是一个将ES6语法转换为ES5的js模块

Babel的使用方法具体可参考官方文档，在这里我们要在webpack中将打包的js文件转换为ES5的版本

首先是安装Babel核心模块，安装第七版本的babel-loader与webpack中打包使用

> npm install babel-core babel-loader@7

安装 babel 的插件

> npm install babel-plugin-transform-es2015-arrow-functions

安装完毕后，需要在项目根目录下新建一个文件 `.babelrc`，来配置Babel中使用的插件，内容为JSON格式

这里先配置了一个箭头函数的转换

```json
{
  "plugins": [
    "transform-es2015-arrow-functions"
  ]
}
```

然后在`./config/webpack.dev.js`的module中的rule内添加一个loader即可

```js
// ./config/webpack.dev.js
{
    // 选择所有js文件进行
    test: /\.js$/,
    use: [
        {
        	loader: 'babel-loader'
    	}
    ]
},
```

安装async函数语法转换插件，将async转换为**使用promsie的普通函数**

```json
{
  "plugins": [
    "transform-es2015-arrow-functions",
    "async-to-promises"
  ]
}
```

`Babel`默认只会转译语法，比如箭头函数，但是不会转换一些新增的对象方法，例如`Object.assign()`和`Array.prototype.includes()`此时，使用额外的三个Babel衍生模块就能更好的处理ECMAScript新增内容

`babel-polyfill`：常规的babel打包是将打包后的js出口文件进行语法的转换，而`babel-polyfill`将语法转换提前到打包之前

`babel-preset`：一个将常用的babel插件集合，也就是说，使用preset会自动配置一些常用的babel插件

`babel-transform`：`babel-polyfill`会将Promise等添加成全局变量，污染全局空间，而`babel-transform`可以解决这个问题

安装`babel-polyfill` ：

> npm install babel-polyfill  

使用：

```js
// 修改./config/webpack.dev.js
entry: {
        main: ['babel-polyfill', './src/main.js']
},
```

安装`babel-preset`：

> npm install babel-preset

使用：

```json
{
  "presets": [
    [
      "env",
      {
        "debug":true
      }
    ]
  ],
  "plugins": [
    "transform-runtime"
  ]
}
```
