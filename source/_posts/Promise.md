---
title: 实现ES6的新特性——Promise
date: 2020-08-23
tags:
- "JavaScript"
- "教程"
- "ES6"
- "异步编程"
categories: 
 - JavaScript
sticky: 3
description: "解析ES6中对异步操作的解决方案——Promise，并使用代码实现原生Promise的功能"
keywords: "前端, JavaScript, ES6, Promise, 微任务"
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1598980976412&di=67e9158ce3a92f30edc899040cf2dafb&imgtype=0&src=http%3A%2F%2Fpic.baike.soso.com%2Fp%2F20140710%2F20140710213836-955952945.jpg
---
> ECMAscript 6 原生提供了 Promise 对象。Promise 对象代表了未来将要发生的事件，用来传递异步操作的消息。


## 了解JavaScript事件循环

在解析promise的机制之前，我们需要掌握浏览器的事件循环，此处引用一张事件循环流程图

![image](https://gitee.com/EricReeves/proxy/raw/master/img/event_loop.png)

在上图中的最后2两行队列均为异步队列，可以观察到，每个宏任务队列后都跟随一个微任务队列，

> 注意，需要把第一个同步任务，也就是首次同步执行的代码视为一个宏任务

在执行栈中的函数遇到异步任务时，会将异步任务交给 右侧相应模块进行处理

第一个队列为宏任务队列，也是主要的异步任务队列，其中的任务由计数器处理模块，ajax请求线程与文件处理模块(NodeJs)进行推入，

第二个队列为微任务队列，在promise对象的`then()`方法，与Node中的`process.nextTick()`都会在执行时，将传入的回调函数推入**当前宏任务后**的微任务队列，

当宏任务与微任务相互嵌套时，此时就可能会产生一些异步任务队列与执行栈会相互添加内容，即在栈中异步队列的函数 与 栈中函数产生异步任务，因此形成循环，而JS的异步任务常常通过事件进行执行，也就是为什么这种循环叫做事件循环

## Promise 是什么？

抽象的说，Promise是JavaScript中进行异步编程的一个新的解决方案

具体的说，Promise就是一个构造函数，通过函数构造出的实例可以封装一系列异步操作，并可以从中得到异步操作产生的结果数据

## 为什么要使用Promise？

> 使用链式调用的编写方式，解决异步回调地狱（代码缩进）的问题 

所谓回调地狱，就是当我们要进行多个异步操作的时候，此时一个回调函数包含另一个异步操作，当异步操作数量达到一定程度时，代码会向右持续缩进，这样会使得程序可读性和可维护性下降

```js
// 回调地狱
setTimeout(()=>{
    setTimeout(()=>{
    	setTimeout(()=>{
    		//更多嵌套..
		},3000)
	},2000)
},1000);
```
在上面的计时器回调代码中，我们可以看到代码缩进一直在增加，在实际的开发环境中，如果有几十个回调，那么代码就变得非常的不美观，且不易维护

但在promise的链式调用中，只需要将上一个处理的对象使用方法继续进行处理，且捕获异常和错误也比较方便

![image-20200826225041424](https://gitee.com/EricReeves/proxy/raw/master/img/image-20200826225041424.png)


解决异步任务的最佳办法，是将promise对象配合 ES8 中新增的 async函数与await来操作结果，其中await会等待promise的值，并将之后的语句放入异步任务中执行

![image-20200826225805992](https://gitee.com/EricReeves/proxy/raw/master/img/image-20200826225805992.png)

## Promise/a+ 规范

规范出现的原因：

1. 我们不知道异步请求什么时候返回数据，所以我们就需要些回调函数。但是在某些情况下我们需要知道数据是在什么时候返回的，然后进行一些处理。
2. 当我们在异步回调里面继续执行异步操作的时候，这样就形成了异步回调函数的嵌套，不好维护以及查找问题

规范的内容是什么？

1. 不管进行什么操作都返回一个Promise实例对象，这个对象里面会有一些属性和方法
2. 这个Promise实例对象有三种状态
3.  一个promise对象只能改变一次状态，而且都会返回一个数据
    * pending
      * 默认状态
    * fulfilled
      * 调用`resolve()`返回的状态
      * 英文含义为 已完成 ，实际上表示成功的状态
      * 返回value
    * rejected
      * 调用`reject()`返回的状态
      * 英文含义为 拒绝了  ， 实际上表示为失败的状态
      * 返回reason


## Promise执行机制

### Promise的任务执行流程

1. 创建一个Promise对象，处于`pending`状态
2. 执行异步操作，成功，调用`resolve()`；失败，调用`reject()` ，都会返回一个新的promise对象
3. 执行实例promise.then()方法，按照当前状态，执行内部的回调，可重复步骤2

> Promise实例利用闭包，通过调用函数来改变不同的状态，由此来执行对应的回调函数

下列代码中，创建了一个promise对象同时立即改变状态，而跟随其后又连续调用2个`then()`，每个`then()`调用结束都会**默认**返回一个新的`promise`对象，而`then()`中成功/失败的回调内部返回的值则为新`promise`的`value`，在这一系列调用中如果没有**抛出任何异常**或者**返回任何失败的promise**，则都会视为**成功**的`promise`

注意：**throw xxx 均为失败状态 ,xxx 为reason的值**

```js
new Promise(((resolve, reject) => {
    reject(11)
  })).then(
    value => {
      console.log('value ',value)
    },
    reason => {
      console.log('reason', reason)
    }
  ).then(
    value => {
      console.log('value ',value)
    },
    reason => {
      console.log('reason', reason)
    }
  )
```


如果想在结果的回调中再执行一个异步任务（也可以说是触发一系列的异步任务），在结果回调函数内需要返回一个新的`promise`对象，并成为.then()的返回结果可以在下一次.then()做出更多的操作

```js
new Promise(((resolve, reject) => {
    reject(11)
  })).then(
    value => {
      console.log(value)
      return new Promise((resolve, reject) =>{
          // 异步任务，请求之类的
          setTimeout(()=>{
          	resolve('22')
      	  }, 1000)
      })
    },
    reason => {
      console.log(reason)
      return 0
    }
  ).then(
    value => {
      console.log('value ',value)
    },
    reason => {
      console.log('reason', reason)
    }
  )
```

### Promise异常传递

当`promise`对象进行一系列任务操作时，在Promise链式调用的回调中，不编写处理失败的回调函数，只在链式调用的末尾使用`catch()`处理异常情况，只要遇到了失败状态的`promise`，就会将失败的promise逐级传递到最后的`catch()`失败处理回调中，如果不抛出异常或者返回失败的`promise`对象，`.catch()`和`.then()` 一样，返回成功的`promise`

```js
new Promise((resolve, reject) => {
    reject(0)
}).then(
    value => {
        console.log('value ',value)
    }).then(
    value => {
        console.log('value ',value)
    }).catch(reason => {
    console.log('fatalError', reason)
})
```

### Promise 中断传递

如果因为某个错误，想要在链式调用的过程中中断整个过程，则需要返回一个`pending`状态的promise，即返回`new Promise(()=> {})`

原理：因为`then()`内部的回调函数最终是由调用执行器中的`resolve()`或者`reject()`从而触发执行的，返回一个pending的`promise`显然并没有改变任何状态，因为初始化`promise`的状态就是`pending`

```js
new Promise((resolve, reject) => {
    reject(0)
}).then(
    value => {
        console.log('value ',value)
    }).then(
       value => {
           console.log('value ',value)
       },
       reason => {
           return => new Promise(()=> {})
    }).catch(reason => {
    console.log('fatalError', reason)
})
```

## 实现 Promise

### 方法以及规则

在了解了官方Promise的使用方法后，为了实现Promise，我们需要更详细的内部原理与运行机制

****
* 构造函数`Promise`的方法
  * `Promise(executor)`
  * `Promise.resolve(value)`
  * `Promise.reject(reason)`
  * `Promise.all(promiseArray)`
  * `Promise.race(promiseArray)`
* 实例对象`promise`的方法
  * `promise.then(onResolved , onRejected)`
  * `promise.catch(onRejected)`
* 规则：
  1. 调用`Promise(executor)`进行初始化`promise`时：
     1. 改变状态与值的内部函数`resolve(value)`或者`reject(reason)`的调用类型为**异步调用**，意味着可能等待`then()`执行后才进行调用
     2. 如果存在多次调用`resolve(value)`或者`reject(reason)`，则以第一个调用为准，舍弃后续调用
     3. `resolve(value)`和`reject(reason)`都不执行，而是使用throw xx来抛出错误，需要将`promise`状态设置成`rejected`
     4. 返回一个`Promise`实例
  2. 调用`promise.then()`时
     1. `promise.then()`应该返回一个新的`promise`对象
     2. 如果`promise`的状态为pending，那么将其推入实例的回调函数数组中
     3. 如果`promise`的状态为fulfilled或者rejected，那么将传入的回调立即作为异步函数执行
     4. 如果在规则2.3满足的情况下，回调函数返回了一个`promise`对象，那么，` promise.then()`返回的新` promise` 对象为回调函数返回的对象
     5. `promise.then()`能够实现传透的功能，即在不传入失败的回调函数`onRejected`或者成功的回调函数`onResloved`时，使用`then()`的链式调用能够将失败的`promise`往后传递
  3. 调用`Promise.resolve(value)`时，`value`可以为`promise`类型，也可以为非`promise`类型，调用后返回一个成功/失败的`promise`
  4. 调用`Promise.reject(reason)`时，`reason` 只能是非`promise`类型的值
  5. 调用`Promise.all(promiseArray)` 应该在所有promise均为成功的情况下，返回一个值为所有`promise`的值的数组的新的成功的`promise`，否则，返回第一个失败的`promise`
  6. 调用`Promise.race(promiseArray)` 应该返回数组中第一个改变状态的`promise`

### Promise构造函数

Promise主构造函数，接收一个函数作为执行器，执行后**立即**产生一个`promise`对象

* 构造函数内部拥有三个属性
  * state：保存状态
  * data：保存数据
  * callbacks：保存一个`then()`方法添加的回调函数的对象
* 在`resolve()`和`reject()`对`this.state`和`this.data`的改变后，需要符合规则1.1和1.2：即将对应回调函数推入异步任务队列中
* 在`resolve()`和`reject()`都没有被调用的情况下，需要符合规则1.3 ： 即使用捕获来处理异常情况
* 在executor中调用`resolve()`或者`reject()` ，其本质是同步/异步地改变状态和值（多数情况下为同步），然后将`promise`对象内部的callbacks 中对应的成功或者回调放置在异步队列中进行调用，可能此时callbacks没有回调函数，但是`promise.then()`是同步的将回调放入自身callbacks中，即**放入回调函数**这一操作永远比在**异步队列中执行回调函数**先执行，这就是为什么Promise的链式调用能够处理异步请求的原因

```js
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

function Promise (executor) {
    // 实例的自身状态属性
    this.state = PENDING // 对象状态默认为pending
    this.data = undefined // 对象的值默认为undefined
    // 存储自身的回调函数，这个回调函数数组由.then()方法进行传入
    this.callbacks = [] // 元素结构为对象，存储2个函数：{onResolved(){}, onRejected(){}}

    /*
    * 此时有的执行器内部调用.resolve()的前后2种状态，
    * 1. 调用resolve()之前自身的状态回调函数才被添加，也就意味着执行器内部立即执行.resolve()
    * 2. 调用resolve()之后自身的状态回调函数才被添加，也就意味着执行器内部延迟执行.resolve()
    * 所以无论何时调用resolve()，它必须在被添加回调callbacks之后才能被调用*/
    const resolve = (value) => {
        // 去除后续的resolve调用，保证状态的唯一性
        if (this.state !== PENDING) {
            return
        }
        // 将状态改成fulfilled，并保存value到实例data中
        this.state = FULFILLED
        this.data = value
        // 如果回调数组中有函数，那么将其推入宏任务队列中，异步调用
        if (this.callbacks.length > 0) {
            setTimeout(() => {
                this.callbacks.forEach(callbacksObj => {
                    callbacksObj.onResolved(value)
                })
            })
        }
    }

    // 失败状态 同理
    const reject = (reason) => {
        if (this.state !== PENDING) {
            return

        }
        this.state = REJECTED
        this.data = reason
        if (this.callbacks.length > 0) {
            setTimeout(() => {
                this.callbacks.forEach(callbacksObj => {
                    callbacksObj.onRejected(reason)
                })
            })
        }
    }

    // 即执行外来的executor，并且将2个函数作为参数交给executor,如果有调用resolve或者reject，那也将一并执行,并使用try catch 捕获throw抛出的值或者错误
    try{
        executor(resolve,reject)
    } catch (error) {
        reject(error)
    }
}
```

值得注意的是，如果使用`function`声明的`resolve()`和`reject()`函数，那么内部的`this`就是指向的`window`，因为这两个函数被外部传入的箭头函数`executor`调用，而外部的executor的`this`指向的是`window`，那么内部的两个函数就是被`windows`调用，将`resolve()`和`reject()`改为箭头函数声明，即在声明时就执行父级作用域的`this`，即`new`语句产生的`promise`对象

### Promise.prototype.then ()

给`Promise`主构造函数原型上添加then方法 ，主要用于实例`promise`

* `then()` 方法主要是用于同步的将传入的回调函数放入`promise`的callbacks属性中、或者根据promise的状态调用响应的回调函数，最后返回一个新的`promise`对象

* `_proimise`的状态为pending ，那么直接将回调推入callbacks属性中，那么此时构造`_proimise`的执行器中的`resolve()` 或者 `reject()` 还没有被执行，也就是说，在状态还没出现之前，就调用了`then()`，那么如何在后面异步任务中产生了状态之后就可以马上执行前面`then()`中的回调以及返回新的`promise`对象呢？

  * 在`resolve()` 或者 `reject()`被调用的时候也会将回调函数数组中的函数加入异步队列中等待调用，而等待到这些函数被调用的时候，即是返回的`promise`对象获得状态的时候，而传入的回调`onResolved`和`onRejected`也有三种情况：返回`promise`，返回非`promise`，抛出异常，使用`try catch `以及递归进行处理

    ```js
    if (this.state === PENDING) {
        this.callbacks.push({
            // 当在resolve调用的时候，这里的onResolved会被加入异步队列并被调用，同时也使得返回的promise拥有了状态和值
            onResolved (value) {
                try {
                  let result = onResolved(value)
                  if (result instanceof Promise) {
                    result.then(resolve, reject)
                  } else {
                    resolve(result)
                  }
                } catch (error) {
                  reject(error)
                }
              },
              onRejected (reason) {
                try {
                  let result = onRejected(reason)
                  if (result instanceof Promise) {
                    result.then(resolve, reject)
                  } else {
                    resolve(result)
                  }
                } catch (error) {
                  reject(error)
                }
              }
        })
    }
    ```

    

* `promise`的状态为resolve或者reject，此时异步队列中已经没有执行的回调，需要在接收到回调的时候将其放入异步队列中，即使用计时器，在计时器异步任务中执行对应状态的回调函数

  * 如果状态回调函数返回的是非_Promise类的值，也就是说手动调用`onResolved` / `onRejected` 后返回的值不是 `Promise` 类型，那么返回的新对象就在执行器中直接调用`resolve(result)`来将返回的promise对象设置为 成功的状态（只要不抛出错误且不返回`promise`的回调均为成功）
  * 状态回调函数内部使用`throw`抛出了异常或者普通值，那么在`catch(error){}`中直接将要返回的promise的状态设置为rejected，且值为error

  * 状态回调函数内部返回了一个`promise`对象，此时需要将这个对象特殊处理

    1. 首先整个`then()`方法的参数接收2个函数参数，返回一个新`promise`对象，其中构造函数执行器为同步执行，所以将判断代码放入执行器中

    2. 判断调用`then()`方法的本体`promise`的状态，其中最简单的如果是pending状态则就仅仅将回调函数放入返回的新`promise`对象的回调数组中，然后将在异步任务中被调用，这次情况就符合第一次创建`promise`对象时，执行器中的`resolve()`或者`rejected()`在**异步任务**中的情况
    3. 如果调用`then()`方法的本体`promise`的状态为fulfilled或者rejected ， 此时需要对应状态使用计时器创建一个新的异步任务，并在这个异步任务中调用.then()传入的回调函数，并拿到回调函数返回的结果，通过结果来进一步生成新的`promise`作为`then()`的返回结果

  > 那么此时就会遇到一个问题：如何去继承回调中返回的 promise的状态和值呢？如何判断这个promise的状态是成功还是失败呢？
  
  `then()` 方法就是用来判断调用的promise的成功与失败的方法，那么我们可以`then()`内的回调中，即返回的`promise`内也对作为`promise`对象的结果，调用一次`then()`，并在其中传入，要返回的新`promise`的状态设置函数`resolve(value)`和`reject(reason)`，这样就可以将后续`promise`的状态与最终值继承到第一次调用`then`返回的`promise`对象中
  
  ```js
  Promise.prototype.then = function (onResolved, onRejected) {
      var _this = this
      // ...
      return new Promise(function (resolve, reject) {
  
          var handleCallback = function (callback) {
              try {
                  var result = callback(_this.data) // 将结果计算出来
                  if (result instanceof Promise) {
                      result.then(resolve, reject) // 将上方new Promise的resolve、reject作为结果的promise.then()的回调
                  } else {
                      resolve(result)
                  }
              } catch (error) {
                  reject(error)
              }
          }
      })
  }
  ```
  
* 在此可以举一个例子：  
  
  * 假如说现在创建了一个`Promise`的实例对象`p`,其状态为fulfilled ，值为`11`
  
  * 然后调用了`p.then()`，其中成功的回调返回了一个新的`Promise`实例对象，且状态为成功，值为`22`
  
  ```js
  p.then(value => new Promise((resolve,reject) => {resolve(22)}))
  ```
  
  * 接着`p.then()` 调用后，会返回一个新的 `Promise`实例对象，我们来看这个新的 `Promise`实例对象是如何产生的
  
      1. 按照`then()`的代码进入 `return`右侧 `new Promise()`的执行器中，在这里，我们将这个 `new Promise()`最后所构造出来的对象称为 `p1`
      2. 此时的`this`为最前面的实例对象`p`，对`p`的状态进行判断，执行转到 `else if (this.state === RESOLVED) {...}`中，其内部为一整个异步任务，被主线程放到异步队列中等待执行，此时`p.then()`的同步任务就执行完毕了
      3. 假设同步任务已经执行完毕，现在跳转到异步任务中执行，也就是开始执行`setTimeout`中回调的内容，异步任务开始
      4. 在 `try catch` 中,`p.then()` 内部的函数将`p.data`作为参数放入传入的 `value=> ...` 箭头函数中执行，此时的`onResolved(this.data)`就等于带着参数执行了`value => new Promise((resolve,reject) => {resolve(22)}`
      5. 此时判断result，发现返回了一个`Promise`的实例对象，这里简称`result`，其状态为`fulfilled`，值为22，那么调用这个实例对象`result.then()`，且成功、失败的的回调函数分别为`p1`的`resolve(value)`和`reject(reason)`
      6. 进入到`result.then()`中，同样又进入一个新promise的创建流程，进入执行器，判断`result`的状态，上方我们设置的为成功的状态，那么在同步队列执行完的异步队列中同样执行了`let result = onResolved(this.data)`这一句，且此时的`onResolved(this.data)`就是p1构造器中的`resolve(value)`，调用之后，此时p1的对返回的`promise`对象的`this.data`继承已经完成了
      7. 这个时候构造p1的异步任务已经完成了，此时通过return返回，成为`p.then()`

    ```js
    Promise.prototype.then = function (onResolved , onRejected) {
    // 始终返回一个新的_promise，需要判断返回的状态，以及回调返回的值，
      // 将判断任务放入执行器中执行，以便产生不同状态的promise
      return new Promise((resolve, reject) => {
        // 如果是无状态的，那么直接将回调放入属性中
        if (this.state === PENDING) {
          this.callbacks.push({
              onResolved (value) {
                try {
                  let result = onResolved(value)
                  if (result instanceof Promise) {
                    result.then(resolve, reject)
                  } else {
                    resolve(result)
                  }
                } catch (error) {
                  reject(error)
                }
              },
              onRejected (reason) {
                try {
                  let result = onRejected(reason)
                  if (result instanceof Promise) {
                    result.then(resolve, reject)
                  } else {
                    resolve(result)
                  }
                } catch (error) {
                  reject(error)
                }
              }
            })
        } else if (this.state === RESOLVED) {
          // 如果这个_promise对象有状态，则将其回调放入异步队列中执行，并判断其执行后的返回值是否为_Promise类型
          // 如果是，则需要使用自身的.then来判断在其手动创建的_promise的执行器到底是调用了那个状态函数
          setTimeout(() => {
            try {
              let result = onResolved(this.data)
              if (result instanceof Promise){
                // result.then(
                  // 如果手动返回的_promise的执行器中调用的是resolve()，那么则会执行
                  // 当成功/失败时，将原来的.then()对象也为成功/失败，并且继承回调内部返回的_promise的值
                  //value => resolve(value),
                  //reason => reject(reason)
                //)
                result.then(resolve, reject)
              } else {
                resolve(result)
              }
            }catch (error) {
              reject(error)
            }
          })
        } else {
          setTimeout(() => {
            try {
              let result = onRejected(this.data)
              if (result instanceof Promise){
                result.then(resolve, reject)
              } else {
                resolve(result)
              }
            }catch (error) {
              reject(error)
            }
          })
        }
      })
    }
    
    ```
    
    此时会发现，上面的代码`try catch`部分重复了4次，所以将重复代码提取为一个新函数，并增加异常传透的功能

```js
Promise.prototype.then = function (onResolved, onRejected) {
  // 实现传透
  // 如果没有传入成功的回调函数，当promise成功时，会默认将onResolved赋值为一个函数，通过返回值来传递成功的promise
  // 如果没有传入失败的回调函数，当遇到失败的promise时，将onRejected默认赋值为一个函数，通过抛出值来传递失败的的promise
  onResolved = typeof onResolved === 'function' ? onResolved : value => value
  onRejected = typeof onRejected === 'function' ? onRejected : reason => {throw reason}
  // 始终返回一个新的_promise，需要判断返回的状态，以及回调返回的值，
  // 将判断任务放入执行器中执行，以便产生不同状态的promise
  return new Promise((resolve, reject) => {
    const handleCall = (callback) => {
      try {
        let result = callback(this.data)
        if (result instanceof Promise) {
          result.then(resolve, reject)
        } else {
          resolve(result)
        }
      } catch (error) {
        reject(error)
      }
    }
    // 如果是无状态的，那么直接将回调放入属性中
    if (this.state === PENDING) {
      this.callbacks.push({
          onResolved (value) {
            handleCall(onResolved)
          },
          onRejected (reason) {
            handleCall(onRejected)
          }
        }
      )
    } else if (this.state === RESOLVED) {
      // 如果这个_promise对象有状态，则将其回调放入异步队列中执行，并判断其执行后的返回值是否为_Promise类型
      // 如果是，则需要使用自身的.then来判断在其手动创建的_promise的执行器到底是调用了那个状态函数
      setTimeout(() => {
        handleCall(onResolved)
      })
    } else {
      setTimeout(() => {
        handleCall(onRejected)
      })
    }
  })
}
```

这时候就有一个问题：为什么调用.then()的有状态的promise时候，内部的`handleCall(onResolved)`要使用异步调用呢？

这是因为，状态主要是依靠构造promise时执行器中的`resolve()`和`reject()`两个函数进行的，而先有回调函数再有状态的原因是在构造器内部使用了计时器调用了`resolve()`和`reject()`，导致同步的`then()`先执行，将内部传入的回调放进了`promise`对象的callbacks中

但先有状态，后有回调的情况就是：先执行了构造器的所有内容，立即改变了状态以及值，将执行自身的回调函数这一任务放进了计时器，从而变为了在同步任务结束之后的异步任务；接着在结束`promise`的构造后，立即调用了`then()`

但进入了.then()后，有状态的promise则不会像无状态的一样，直接放入promise的callbacks，来等待`resolve()`和`reject()`，因为已经在执行器里面同步执行过了，这个时候.then()要根据调用回调函数的结果，返回一个新的`promise`，而这一过程往往是应用于发送异步请求，如果在此处直接调用`handleCall(onResolved)`，那么主线程就会等待这个`then()`中回调函数产生的结果从而去创建新promise（包括改变它的状态与值）这一过程，这就违背了Promise思想的初衷

还有一点是，异步的`handleCall(onResolved)`与同步创建`promise`的执行器形成了闭包，这样，`handleCall(onResolved)`在异步任务中执行时，还可以去改变同步任务时创建的状态为pending的`promise`对象，然后通过微任务队列，先调用`then()`的先执行，先改变`then()`返回的promise的状态，这样就形成了一种前后关系

### Promise.prototype.catch()

`catch()`其实就是`then()`处理失败的情况，传入回调到`onRejected` 调用并返回`then()`的结果即可，其中`onResolved`的位置设置为`undefined`或者`null`都可以 

```js
Promise.prototype.catch = function (onRejected) {
	return this.then(undefined, onRejected)
}
```

### Promise.resolve(value)

`Promise.resolve(value)` 返回一个成功的`promise`，值为`value`，`value`的值可以普通值，也可以是`promise`对象

```js
// .reslove()返回一个新的promise对象，并且需要判断value的类型
Promise.resolve = function (value) {
    return new Promise((resolve, reject) => {
        if (value instanceof Promise){
            // 此处使用传入的promise.then方法来解决新返回的promise的状态
            value.then(resolve, reject)
        }else {
            resolve(value)
        }
    })
}
```

### Promise.reject(reason)

`Promise.reject(reason)`返回一个失败的`promise`，值为`reason`，`reason`的值只能为非`promise`类型的值

```js
// .reject()方法也是返回一个promise对象，并且只接受一个reason
Promise.reject = function (reason) {
    return new Promise((resolve, reject) => {
        reject(reason)
    })
}
```

### Promise.all(promiseArray)

`Promise.all(promiseArray)` 返回一个`promise`，接收一个参数`promiseArray`(_promise数组)，但其中的元素可以是`promise`对象，也可以是非`promise`对象

* 当内部所有promise的状态都为成功的时候，返回成功的promise，且promise的value为一个数组，其值promiseArray所有promise的value

* 否则返回失败的`promise` 且其value为状态为失败的`promise`的`value`

  ```js
  Promise.all = function (promiseArray) {
      if (promiseArray instanceof Array) {
          return new Promise((resolve, reject) => {
              const valueArray = new Array(promiseArray.length)
              let counter = 0
              // 遍历promise数组使用then判断状态，并在回调中执行值的积累和以及改变状态
              promiseArray.forEach((promise, idx) => {
                  // 无论数组中的值是否为promise类型，使用.resolve()将其强制转换为promise对象，再进行.then()的判断
                  Promise.resolve(promise).then(
                      value => {
                          counter++
                          valueArray[idx] = value
                          if (counter === promiseArray.length) {
                              resolve(valueArray)
                          }
                      },
                      reason => {
                          reject(reason)
                      }
                  )
              })
  
          })
      } else {
          return null
      }  
  }
  ```

  > valueArray[idx] = value 这行为什么不能被 valueArray.push(value )代替呢？
  >
  > 此处不使用push进行增加元素的原因是因为传入的promise在构造时，可能resolve()或者reject()延迟调用的情况，如果使用了索引，那么在同步遍历时，就以闭包的形式锁定了value在数组中的位置

### Promise.race(promiseArray)

`Promise.race(promiseArray)` 返回一个`promise`，接收一个参数`promiseArray`(promise数组)

* 返回数组中，最快产生成功或者失败状态的`promise`
* 例如：[p1,p2,3]  ,p1延迟2秒更新状态, p2 为同步获得状态，3为非promise，那么将数组看做为一个队列，p2和3为最先获得状态，但p2排在3的前面，所以最终结果输出p2

```js
Promise.race = function (promiseArr) {
    if (promiseArr instanceof Array) {
        return new Promise((resolve, reject) => {
            promiseArr.forEach(__promise => {
                if (promise instanceof Promise){
                    promise.then(resolve, reject)
                } else {
                    Promise.resolve(__promise).then(resolve, reject)
                }
            })
        })
    } else {
        return null
    }
}
```

### 使用微任务

到此你会发现，当promise调用回调函数的时候，总是使用的`setTimeout()`来将其模拟为异步任务来调用，这个时候就出现了一个问题:

如果在所有创建`promise`对象之前就启动一个0秒的定时器来调用输出，那么这个定时器内的内容一定是最先输出的（宏任务队列）这明显不符合`官方Promise`的一个特点：始终在异步微任务中调用回调函数；

通过文档了解到所有定时器函数、ajax请求、DOM事件回调均为宏任务，而因为使用了定时器，以上实现的promise并没有启动任何微任务，那么如何将一个回调函数变成在微任务中执行呢？

这个问题可以在使用`CommandJs`模块规范的NodeJs中使用`process.nextTick(callback)`解决，`process.nextTick(callback)`在NodeJs中用于将回调函数加入微任务队列，由此就可以真正模拟`官方Promise`的功能

以下是`Promise`构造函数 以及`then()`方法的的最终代码
```js
function Promise (executor) {

  this.state = PENDING 
  this.data = undefined 
  
  this.callbacks = []

  const resolve = (value) => {
    if (this.state !== PENDING) {
      return

    }
    this.state = FULFILLED
    this.data = value
    if (this.callbacks.length > 0) {
      process.nextTick(() => {
        this.callbacks.forEach(callbacksObj => {
          callbacksObj.onResolved(value)
        })
      })
    }
  }
  const reject = (reason) => {
    if (this.state !== PENDING) {
      return

    }
    this.state = REJECTED
    this.data = reason
    if (this.callbacks.length > 0) {
      process.nextTick(() => {
        this.callbacks.forEach(callbacksObj => {
          callbacksObj.onRejected(reason)
        })
      })
    }
  }

  try {
    executor(resolve, reject)
  } catch (error) {
    reject(error)
  }
}


Promise.prototype.then = function (onResolved, onRejected) {
  
  onResolved = typeof onResolved === 'function' ? onResolved : value => value
  onRejected = typeof onRejected === 'function' ? onRejected : reason => {
    throw reason
  }
 
  return new Promise((resolve, reject) => {
    const handleCall = (callback) => {
      try {
        let result = callback(this.data)
        if (result instanceof Promise) {
          result.then(resolve, reject)
        } else {
          resolve(result)
        }
      } catch (error) {
        reject(error)
      }
    }
    
    if (this.state === PENDING) {
      this.callbacks.push({
        onResolved (value) {
          handleCall(onResolved)
        }, onRejected (reason) {
          handleCall(onRejected)
        }
      })
    } else if (this.state === FULFILLED) {
      process.nextTick(() => {
        handleCall(onResolved)
      })

    } else {
      process.nextTick(() => {
        handleCall(onRejected)
      })
    }
  })
}
```

### ES6 class（类）版本的 Promise

新增 `Promise.any(array)` 方法，其目的与`Promise.all(array)`方法相反：只要数组中出现成功的状态的`promise`，立即返回成功状态的`promise`

```js
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'
const PENDING = 'pending'

class Promise {
  constructor (executor) {
    this.state = PENDING
    this.data = undefined
    this.callbacks = []

    const resolve = (value) => {
      if (this.state === PENDING) {
        this.state = FULFILLED
        this.data = value
        if (this.callbacks.length > 0) {
          process.nextTick(() => {
            this.callbacks.forEach(callbackObj => {
              callbackObj.onResolved(value)
            })
          })
        }
      }
    }

    const reject = (reason) => {
      if (this.state === PENDING) {
        this.state = REJECTED
        this.data = reason
        if (this.callbacks.length > 0) {
          process.nextTick(() => {
            this.callbacks.forEach(callbackObj => {
              callbackObj.onRejected(reason)
            })
          })
        }
      }
    }

    try {
      executor(resolve, reject)
    } catch (e) {
      reject(e)
    }
  }
  // 实例方法直接使用ES6函数声明方式
  then (onResolved, onRejected) {
    onResolved = typeof onResolved === 'function' ? onResolved : value => value
    onRejected = typeof onRejected === 'function' ? onRejected : reason => {
      throw reason
    }

    return new Promise((resolve, reject) => {
      const callbackHandler = (callback) => {
        try {
          const result = callback(this.data)
          if (result instanceof Promise) {
            result.then(resolve, reject)
          } else {
            resolve(result)
          }
        } catch (e) {
          reject(e)
        }
      }
      if (this.state === PENDING) {
        this.callbacks.push({
          onResolved () {
            callbackHandler(onResolved)
          }, onRejected () {
            callbackHandler(onRejected)
          }
        })
      } else if (this.state === FULFILLED) {
        process.nextTick(() => {
          callbackHandler(onResolved)
        })
      } else {
        process.nextTick(() => {
          callbackHandler(onRejected)
        })
      }
    })
  }

  catch (onRejected) {
    return this.then(undefined, onRejected)
  }

  // 在类中使用关键字 static声明 类本身方法，即此类型方法只属于类本身，而不属于每个类的实例，
  // 以下均为类方法
  static resolve (value) {
    return new Promise((resolve, reject) => {
      if (value instanceof Promise) {
        value.then(resolve, reject)
      } else {
        resolve(value)
      }
    })
  }

  static reject (reason) {
    return new Promise((resolve, reject) => {
      reject(reason)
    })
  }

  static all (promiseArr) {
    if (promiseArr instanceof Array) {
      return new Promise((resolve, reject) => {
        const data = []
        let counter = 0
        promiseArr.forEach((promise, idx) => {
          Promise.resolve(promise).then(value => {
            counter++
            data[idx] = value
            if (counter === promiseArr.length) {
              resolve(data)
            }
          }, reason => {
            reject(reason)
          })
        })
      })
    } else {
      return null
    }
  }

  static race (promiseArr) {
    if (promiseArr instanceof Array) {
      return new Promise((resolve, reject) => {
        promiseArr.forEach(promise => {
          Promise.resolve(promise).then(value => {
            resolve(value)
          }, reason => {
            reject(reason)
          })
        })
      })
    } else {
      return null
    }
  }

  static any (promiseArr) {
    if (promiseArr instanceof Array) {
      return new Promise((resolve, reject) => {
        const data = []
        let counter = 0
        promiseArr.forEach((promise, idx) => {
          Promise.resolve(promise).then(value => {
            resolve(value)
          }, reason => {
            counter++
            data[idx] = reason
            if (counter === promiseArr.length) {
              reject(data)
            }
          })
        })
      })
    } else {
      return null
    }
  }
}

```

## Promise面试题

```js
  setTimeout(()=>{
    console.log(0)
  })
  new Promise((resolve, reject) => {
    console.log(1)
    resolve()
  }).then(()=>{
    console.log(2)
    new Promise(((resolve, reject) => {
      console.log(3)
      resolve()
    })).then(() => {
      console.log(4)
    }).then(() =>{
      console.log(5)
    })
  }).then(()=>{
    console.log(6)
  })

  new Promise((resolve, reject) => {
    console.log(7)
    resolve()
  }).then(() => {
    console.log(8)
  })

```

让我们从头开始解析：

1. 首先第一句执行`console.log(0)`的计时器进入宏队列
2. 主线程同步任务到达第一个new，创建第一个promise ，执行内部的`console.log(1)`，然后执行`then()`，将 `console.log(2)` 以下直到 `console.log(6)`以上的放入微任务队列
3. 主线程同步任务到达`console.log(6)`下方的new，创建第二个promise，执行`console.log(7)`，然后执行`then()` 将`console.log(8)`放入微任务队列
4. 主线程同步任务执行完毕，开始执行异步任务
5. 首先执行异步微任务队列
6. 微任务队列中的**第一个**微任务，包含`console.log(2)` 的`.then()`代码块被执行，接着进入`console.log(2)` 下方的new中执行`console.log(3)`，以及`resolve()`，再执行`.then()`，此时将调用`console.log(4)`的回调放入微任务队列末尾
7. 此时第一个`promise.then()`中的代码已经执行完毕了，即产生了结果，所以他的下一个`then()`被执行，`console.log(6)`被加入微任务队列
8. 接下来执行**微任务队列**中的**第二个**异步微任务，即第二个`promise.then()`中的`console.log(8)`
9. 接着处理微任务队列中的**第三个**异步微任务，即第**6**步放入的任务，执行完毕后，执行下一个`then()`，`console.log(5)`的回调被放入微任务队列
10. 接着处理微任务队列中的**第四个**异步微任务，即第**7**步被放入的调用`console.log(6)`的回调
11. 最后执行微任务队列中最后一个任务，即第**9**步中放入的调用`console.log(5)`的回调
12. 此时**微任务队列**中的任务已经全部执行完毕，开始转到**宏任务队列**中，执行下一个宏任务
13. 第**1**步中的计时器 `console.log(0)`被执行，整个过程执行完毕
14. 整体输出 1 7 2 3 8 4 6 5 0




## Promise总结

最后，简单的总结一下Promise主要的的工作过程

* 每个`promise`对象都拥有一个回调容器，包含成功与失败的回调函数，他们在`promise`本身被创建的时候为空
* `promise`处理异步任务关键是在于在创建`promise`时，内部的状态改变函数一般为同步执行，改变立即其状态以及值，但同时将执行自身的回调函数放入异步微任务队列中
* then方法的过程分为两种情况
  * 第一种情况是当`promise`无状态时，将收到的函数**立即**推入`promise`的回调容器中()，等待主线程处理微任务队列
  * 第二种情况是当`promise`有状态时，直接将回调函数调用推入异步微队列，并立即返回一个新的`promise`（此时状态暂时为`pending`），利用JS的闭包，将改变这个返回的`promise`对象**状态**的函数放入微任务中的回调内，这样做就可以在异步微任务中更新立即返回的`promise`的状态

