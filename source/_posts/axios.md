---
title: 封装AJAX以及Axios的使用
tags:
- "AJAX"
- "JavaScript"
- "HTTP"
- "Axios"
categories: 
 - JavaScript
 
keywords: "JavaScript, AJAX, Axios "
cover: https://i.morioh.com/2019/11/07/e90866e7658d.jpg
---
# HTTP请求与XMLHttpRequest

## API分类

* REST API，restful
  * 使用到请求方式的语义决定服务器进行CRUD的其中一种，例如GET为查询，POST为修改，PUT为增加，DELETE为删除
  * 同一个路径可以进行多个操作
* 非REST API ，restless
  * 请求方式不觉得服务器的操作
  * 一个路径只对应一个操作
  * 一般只有GET/POST 

## XMLHttpRequest对象

* status		http状态码
* statusText  状态码对应的文本
* readyState 标识当前xhr发送请求的状态的只读属性，不是http状态码
* onreadystatechange 当xhr的请求状态发送改变的时候
  * 0 初始化
  * 1 即将发送请求
  * 2 已经发送了秦秋
  * 3  请求中
  * 4 请求完成，收到响应
* responseType 设置主动解析的数据类型
* response 通过数据类型解析好的数据对象
* timeout 设置超时时间
* ontimeout 绑定超时监听回调函数
* error
* open() 初始化一个请求，填写参数以及请求方式
* send() 发送请求
* abort() 终端send()
* getResponseHead(name)
* getAllResponseHeads() 获得所有响应头字段
* setRequestHeader(name,value) 设置请求头字段

## 请求响应报文

  请求报文(客户端[request]->服务端)  
>  GET(请求的方式) /newcoder/hello.html(请求的目标资源) HTTP/1.1(请求采用的协议和版本号)  
   Accept: */*(客户端能接收的资源类型)  
   Accept-Language: en-us(客户端接收的语言类型)  
   Connection: Keep-Alive(维护客户端和服务端的连接关系)  
   Host: localhost:8080(连接的目标主机和端口号)  
   Referer: http://localhost/links.asp (告诉服务器我来自于哪里)  
   User-Agent: Mozilla/4.0(客户端版本号的名字)  
   Accept-Encoding: gzip, deflate(客户端能接收的压缩数据的类型)  
   If-Modified-Since: Tue, 11 Jul 2000 18:23:51 GMT(缓存时间)   
   Cookie(客户端暂存服务端的信息)  
   Date: Tue, 11 Jul 2000 18:23:51 GMT(客户端请求服务端的时间)  


  响应报文(服务端[response]->客户端)  
>  HTTP/1.1(响应采用的协议和版本号) 200(状态码) OK(描述信息)  
   Location: http://www.baidu.com(服务端需要客户端访问的页面路径)   
   Server:apache tomcat(服务端的Web服务端名)  
   Content-Encoding: gzip(服务端能够发送压缩编码类型)   
   Content-Length: 80(服务端发送的压缩数据的长度)  
   Content-Language: zh-cn(服务端发送的语言类型)   
   Content-Type: text/html; charset=GB2312(服务端发送的类型及采用的编码方式)  
   Last-Modified: Tue, 11 Jul 2000 18:23:51 GMT(服务端对该资源最后修改的时间)  
   Refresh: 1;url=http://www.it315.org(服务端要求客户端1秒钟后，刷新，然后访问指定的页面路径)  
   Content-Disposition: attachment; filename=aaa.zip(服务端要求客户端以下载文件的方式打开该文件)  
   Transfer-Encoding: chunked(分块传递数据到客户端）    
   Set-Cookie:SS=Q0=5Lb_nQ; path=/search(服务端发送到客户端的暂存数据)  
   Expires: 服务端禁止客户端缓存页面数据  
   Cache-Control: no-(服务端禁止客户端缓存页面数据)    
   Pragma: no-(服务端禁止客户端缓存页面数据)    
   Connection: close(1.0)/(1.1)Keep-Alive(维护客户端和服务端的连接关系)    
   Date: Tue, 11 Jul 2000 18:23:51 GMT(服务端响应客户端的时间)  

**在服务器响应客户端的时候，带上Access-Control-Allow-Origin头信息，是解决跨域的一种方法。**

## 实现一个简单的 ajax封装

在了解Axios之前，首先我们要知道的是，Axios 是一个基于 promise 的 HTTP 库 ，使用了promise异步执行回调的特性来携带数据，我们可以通过xhr与promise封装一个简单的ajax模块

```js
function axios ({ url, method = 'GET', param = {}, data = {} }) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest()
    if (method === 'GET' || method === 'DELETE') {
      // 处理query参数param
      const queryTempArr = []
      Object.keys(param).forEach(key => {
        queryTempArr.push(`${key}=${param[key]}`)
      })
      // 将GET请求的参数拼接到url后
      const queryString = '?' + queryTempArr.join('&')
      url += queryString
      xhr.open(method, url, true)
      xhr.send(null)
    } else if (method === 'POST' || method === 'PUT') {
      // 请求体格式
      xhr.open(method, url)
      xhr.setRequestHeader('Content-Type', 'application/json;charset=utf-8')
      xhr.send(JSON.stringify(data)) //请求被放入异步队列中
    }
    // DOM 0 级事件监听
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        const { status, statusText } = xhr
        if (status >= 200 && status < 300 || status === 304) {
          const response = {
            data: JSON.parse(xhr.response), status, statusText
          }
          resolve(response)
        } else {
          reject(new Error(`request error ， status is ${status} ${statusText} `))
        }
      }
    }
  })
}
```

# Axios

Axios 是一个基于 promise 的 HTTP请求 库，可以用在浏览器和 node.js 中。

## 特性

- 从浏览器中创建 [XMLHttpRequests](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)
- 可以从 node.js 创建 [http](http://nodejs.org/api/http.html) 请求
- 支持 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 语法
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防御 [XSRF](http://en.wikipedia.org/wiki/Cross-site_request_forgery)

## Axios的拦截器处理流程

先贴上贴上代码：

```js
// 1.通过axios.create() 创建一个实例，其功能与直接使用axios类似，创建一个实例能更好的管理和区分不同域的请求
const axiosInstance = axios.create({
    baseURL:'http://localhost:5000',
    headers:{'Content-Type':'application/json;charset=utf-8'}
  })
// 2.设置请求拦截器
  axiosInstance.interceptors.request.use(
    config => {
      config.params = {
        userType:'jobseeker'
      }
      return config
    },
    error => Promise.reject(error)
  )
// 3.设置响应拦截器
  axiosInstance.interceptors.response.use(
    response => {
      console.log(response)
      return response
    },
    error => Promise.reject(error)
  )
// 4.使用axios实例发送请求
  axiosInstance.get('/get-user-list').then(
    res => {
      console.log(res.data)
    }
  )
```

在整个流程中，拦截器的作用顾名思义，拦截请求的配置，以及响应信息，所以拦截器都应该在发送请求之前进行设置

拦截器的本质其实就是回调函数，观察其回调就会发现，他的回调就是Promise对象的`then()`方法的回调函数。

可以说，相当于在发送请求之前，与接收响应之后，设置拦截器的请求都会被强制进入到拦截器回调函数中做处理，相当于在链式调用中插入了一个`then()`方法，而`then()`方法内部的回调函数就是拦截器的回调函数，所以在拦截器的回调中必须返回一个值，来保证数据能够向后传递

## Axios取消请求以及请求防抖

想要实现取消请求的功能，需要在配置axios以及其实例时，加入cancelToken 属性，其值为一个axios.CancelToken对象，其过程类似于创建Promise对象，需要将其参数保存到外部

```js
let cancel
const axiosInstance = axios.create({
    baseURL:'http://localhost:7000',
    headers:{'Content-Type':'application/json;charset=utf-8'}
    cancelToken: new CancelToken( (c) => {
      cancel = c
    })
  })
 
```

上方代码的cancle=c 其实就是将cancel指向c这个函数，类似于promise的reject，，当前在请求时调用这个函数c时，可立即使其内部的promise立即失败，由此取消请求的发送，因此，取消请求可以用作请求防抖来防止过多的重复请求，代码如下

```js
let cancel
const axiosInstance = axios.create({
    baseURL: 'http://localhost:7000', headers: { 'Content-Type': 'application/json;charset=utf-8' },
})
axiosInstance.interceptors.request.use(config => {
    if (cancel instanceof Function) {
        cancel()
    }
    config.cancelToken = new axios.CancelToken((c) => {
        cancel = c
    })

    return config
}, error => {
    console.log(error)
    return Promise.reject(error)
})

axiosInstance.interceptors.response.use(response => {
    console.log(response)
    return response
}, error => Promise.reject(error))

// 绑定到按钮的click监听上
function handelClick () {
    axiosInstance.get('/test').then(res => {
        cancel = null
        console.log(res.data)
    }, err => {
        console.log(err)
    })
}
```

在以上代码中，假如每按钮点击一次就调用一次`handelClick()`触发一次ajax请求，我们在拦截器中为每个请求设置了一个取消请求的配置，并且将取消请求的函数暴露在了全局作用域，以取消请求来实现请求防抖的流程是在请求到达请求拦截器时，先判断前一个请求的取消函数是否存在，如果存在，则立即调用取消请求，否则不进行处理，在得到响应时，将本次请求的取消函数清除即可

> 这里通过对按钮点击事件的防抖也是一种处理办法

未完待续...
