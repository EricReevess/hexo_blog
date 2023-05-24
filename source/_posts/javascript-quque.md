---
title: JavaScript 并行异步队列的实现
tags:
 - "JavaScript"
 - "TypeScript"
 - "数据结构与算法"
categories: 
 - 数据结构
keywords: "JavaScript, TypeScript, 异步队列, 文件上传, Promise"
cover: https://cdn.programiz.com/sites/tutorial2program/files/queue-implementation.png
---

# JavaScript 并行异步队列的实现

## 背景

队列，这玩意几乎计算机相关的大学生都学过，用简单的描述来说，就是一个排队处理调度机制，先来先出去嘛，队列的算法操作与数据结构也相对比较简单，一个装东西的容器，一个从头出队列的操作，一个从尾部进的操作。当然可能还有其他的操作。

最近团队的前端项目中有一个很常见的需求：上传文件。平常我们遇到最多的场景就是网盘，但是文件上传就只有把文件推上去那么简单吗？不，文件大小是个问题。

小文件在上传中几乎是秒级的速度，但大文件不一样，需要切片。

文件切片可以说是很常规的文件操作了，其实就是把文件的二进制数据按照大小一节一节的砍下来，传输完成后再按照相应的位置拼上去，这里我们不讨论如何保证传输中不出错，我们讨论如何优雅的传输这大量的文件“碎片”

## 为什么需要队列

可以想象一下这样的一个问题，我们的文件大小有 100MB，我需要将它切为20份，每一份5MB，那么我们该如何传输这 20 份文件切片呢？将他们都创建一个上传任务吗？这样做可以，你可以同时发送20个请求来发送字节流。

要是文件再大一点呢？1GB 或者更大，那文件的切片的规模就会非常大，同时传输你的浏览器已经把内存给爆了，完全不可行。为了解决这个问题，那么我们可以使用队列，使用并行队列。

那么并行队列是什么样子呢？就像食堂排队打饭，但是有多个打饭窗口，每次可以打 4 个人饭，但当 3 个人都打完了，第 4 个人还在打，那么这3个人就应该出队列，然后后面补上，保证窗口的任何时刻都是满载状态，我们在分块上传中，也是需要这种效果

## 实现队列

我们直接上 ts 代码，来看看这个队列如何实现

```typescript
export function createQueue(
  tasks: (() => Promise<void>)[],
  maxNumOfWorkers = 1,
): Promise<void> {
  let numOfWorkers = 0;
  let taskIndex = 0;

  return new Promise((done, failed) => {
    const getNextTask = (): void => {
      if (numOfWorkers < maxNumOfWorkers && taskIndex < tasks.length) {
        tasks[taskIndex]()
          .then(() => {
            numOfWorkers -= 1;
            getNextTask();
          })
          .catch((error: Error)=> {
            failed(error);
          });
        taskIndex += 1;
        numOfWorkers += 1;
        getNextTask();
      } else if (numOfWorkers === 0 && taskIndex === tasks.length) {
        done();
      }
    };
    getNextTask();
  });
}
```

首先我们需要使用 Promise ，Promise 的具体相关可以查阅 [MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

我们创建了一个函数名为`createQueue`，意为创建队列，这个函数需要两个参数`tasks`和`maxNumOfWorkers`。

`tasks`参数的类型为元素类型为函数的数组，其中的函数的返回值是一个 返回值为空的`Promise`对象

`maxNumOfWorkers`为`number`类型，决定最多有多少个任务同时进行处理

先看整体函数结构：`createQueue`返回一个`Promise`对象，当这个return的`Promise`改变状态时，也就表明队列中所有任务都结束了

细看函数体：我们需要初始化两个变量，`numOfWorkers` 和`taskIndex`

`numOfWorkers`：记录的是当前正在处理的队列任务的数量

`taskIndex`：记录整体任务的处理进度

先说说该队列实现的思想，该队列主要利用递归调用和闭包机制，简单的来说就是套娃，但不是简单的套娃。因为每个结束的任务都可以开启下一个任务，以此类推。我们来看看返回的这个`new Promise()`干了啥

```typescript
return new Promise((done, failed) => {
    const getNextTask = (): void => {
      if (numOfWorkers < maxNumOfWorkers && taskIndex < tasks.length) {
        tasks[taskIndex]()
          .then(() => {
            numOfWorkers -= 1;
            getNextTask();
          })
          .catch((error: Error)=> {
            failed(error);
          });
        taskIndex += 1;
        numOfWorkers += 1;
        getNextTask();
      } 
      if (numOfWorkers === 0 && taskIndex === tasks.length) {
        done();
      }
    };
    getNextTask();
});
```

`Promise`上的`done`、`failed `分别对应队列任务全部完成、失败，失败的结果是：只要有一个任务失败，则后续任务全部终止

任务能按预期处理的实现则是在`getNextTask`这个函数中

在函数`getNextTask`外部代码中，我们先调用`getNextTask` 使得`getNextTask`递归到整个处理池满载的状态，即`numOfWorkers = maxNumOfWorkers`

每个`getNextTask`任务中，符合条件的情况下，将任务队列中的函数进行执行，也就是`tasks[taskIndex]()`，等待任务结束，即调用`then()`或者`catch()`

任务结束，调用`then()`将处理池中的数量-1，然后在开始下一个任务，以此递归，这样，即使有任务提前完成，也会让队列处于满载状态，提高处理效率

任务结束条件`numOfWorkers === 0 && taskIndex === tasks.length`，当正在任务处理的数量为`0` 并且 任务指针已经指向末尾+1的位置，表明最后一个队列任务已经完成，这个时候调用`done()`，即可让`new Promise`产生一个`fulfilled`状态，让外部调用队列的`Promise`对任务的结束进行处理，队列异常终止处理也是如此

# 总结
在做大文件上传之前，一直觉得队列这个数据结构用处不多，其原因是我们大多时候都是用的同步的思维编写代码和思考问题。
上文的队列仅仅是一个简单的解决方案，使用递归的方式来实现队列，其实是不太好的，因为当数据量过于庞大和复杂，会导致内存占用过大，并且不好处理在失败的任务重启这个问题。