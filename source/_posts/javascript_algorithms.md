---
title: JavaScript数据结构与算法
tags:
 - "JavaScript"
 - "算法"
 - "数据结构"
 - "学习笔记"
categories: 
 - 算法与数据结构

keywords: "算法与数据结构, 二叉树, 队列，栈"
cover: https://pic4.zhimg.com/v2-6d4a0ccd09e126a8af0035327a9a235c_1440w.jpg?source=172ae18b
---
> 数据结构与算法是每一个计算机编程学者的必备知识，在日常生活中，算法无处不在，本文将使用JavaScript实现一些常用的数据结构与算法，例如二叉树的创建以及删除等，以及一些算法的实际应用

## 队列
* 使用队列实现约瑟夫环

  ```js
  function Queue (size) {
    this.items = []
    this.index = 0
    this.maxSize = size
  }
  Queue.prototype = {
    constructor: Queue,
    enter (item) {
      if (this.index < this.maxSize) {
        item !== '' ? this.items[this.index++] = item : console.log('数据不能为空')
      } else {
        console.log('当前队列已满')
      }
    },
    shift () {
      if (!this.isEmpty()) {
        this.index--
        return this.items.shift()
      } else {
        console.log('当前队列为空')
      }
    },
    peek () {
      if (!this.isEmpty()) {
        return this.items[0]
      } else {
        console.log('栈当前为空')
        return null
      }
    },
    isEmpty () {
      return this.index === 0
    },
    size () {
      return this.index
    },
    clear () {
      this.index = 0
      this.items = []
    },
    toString () {
      return `head->| ${this.items.map(item => item)} |<-tail`
    }
  }
  
  function josephRing (arr,num) {
    let res = null
    let queue = new Queue(arr.length)
    for (let i = 0; i < arr.length; i++) {
      queue.enter(arr[i])
    }
    for (let i = 0; i <arr.length-1 ; i++) {
      for (let j = 0; j <num -1 ; j++) {
        queue.enter(queue.shift())
      }
      res = queue.shift()
      //console.log(res)
    }
    return queue.peek()
  }
  console.log(josephRing([1,2,3,4,5,6],2))
  ```

  

* 使用插入排序实现优先级队列

  ```js
  function priorityQueue (size) {
    function QueueElement (value, priority) {
      this.value = value
      this.priority = priority
    }
    this.items = []
    this.index = 0
    this.maxSize = size
  
    this.enter = function (value, priority) {
      if (this.index < this.maxSize) {
        let element = new QueueElement(value, priority)
        if (this.isEmpty()) {
          this.items.push(element)
        } else {
          // 经典插入排序
          let flag = false	// 扫描标志位
          for (let i = 0; i < this.size(); i++) {
            if (this.items[i].priority > element.priority) { //检测到有元素的优先级比新元素优先级大
              this.items.splice(i, 0, element) // 在当前位置插入
              flag = true // 标识已经插入
              break
            }
          }
          if (!flag) { // 如果队列中所有的元素优先级都比新元素小
            this.items.push(element) // 放置在队列最后
          }
        }
        this.index++ // 增加index
      } else {
        console.log('当前队列已满')
      }
    }
  }
  priorityQueue.prototype = Queue.prototype // 继承普通队列的一些方法
  
  let pQueue = new priorityQueue(10)
  pQueue.enter('Apple', 10)
  pQueue.enter('sbb', 30)
  pQueue.enter('xiaomi',20)
  pQueue.enter('lenovo',3)
  
  console.log(pQueue)
  ```


## 集合

集合通常是一组无序，不能重复的元素构成，常见的实现方式为哈希表

```js
function Set () {
  this.items = {}
}
Set.prototype = {
  add: function (value) {
    if (!this.has(value)) {
      this.items[value] = value
      return true
    } else {
      return false
    }
  },
  has: function (value) {
    return this.items.hasOwnProperty(value)
  },
  remove: function (value) {
    if (this.has(value)) {
      delete this.items[value]
      return true
    } else {
      return false
    }
  },
  getValues: function () {
    return Object.keys(this.items)
  },
  isSet:function (set) {
    return set instanceof Set
  },
  size: function () {
    return Object.keys(this.items).length
  },
  clear: function () {
    this.items = {}
  }
}
Set.prototype.constructor = Set
```

集合间操作：

* 并集

  ```js
  union: function (set) {
    if (!set instanceof Set) {
      return false
    }
    let unionSet = new Set()
    let values = this.getValues()
    for (let i = 0; i < values.length; i++) {
      unionSet.add(values[i])
    }
    values = set.getValues()
    for (let i = 0; i < values.length; i++) {
      unionSet.add(values[i])
    }
    return unionSet
  },
  ```

  

* 交集

  ```js
  intersection:function(set) {
      if (!this.isSet(set)) {
        return false
      }
      let intersectionSet = new Set()
      let values = this.getValues()
      for (let i = 0; i < values.length; i++) {
        if (set.has(values[i])){
          intersectionSet.add(values[i])
        }
      }
      return intersectionSet
    },
  ```

* 差集

  ```js
  difference:function(set) {
      if (!this.isSet(set)) {
        return false
      }
      let differenceSet = new Set()
      let values = this.getValues()
      for (let i = 0; i < values.length; i++) {
        differenceSet.add(values[i])
      }
  
      values = set.getValues()
      for (let i = 0; i < values.length; i++) {
        if (differenceSet.has(values[i])){
          console.log(values[i])
          delete differenceSet.remove([values[i]])
        } else{
          differenceSet.add(values[i])
        }
      }
      return differenceSet
    },
  ```

* 子集

  ```js
  isChild:function (set) {
      if (!this.isSet(set)) {
        return false
      }
      let values = set.getValues()
      for (let i = 0; i < values.length; i++) {
        if (!this.has(values[i])){
          return false
        }
      }
      return true
    },
  ```

## 哈希表

* 哈希表通常基于数组实现
* 优点：
  * 可以提供非常快的插入删除查找 操作
  * 无论数据规模大小，插入和删除的时间复杂度接近常量O(1)，实际为通过计算得出
  * 一般情况下性能比树结构快，且编码容易
* 缺点：
  * 数据无序，且不能通过固定方式顺序遍历
  * key不允许重复
* 空间利用率不高
  * 查找最大最小值不方便
  
* 哈希化：将对应数据转换为数字
* 哈希函数：将大数字转换为小数字
* 解决冲突：综合来讲，链地址法的效率比较稳定
  * 链地址法：结合链表存储冲突元素（推荐）
  * 开放地址法：寻找表中空白位置进行添加
    * 线性探测：
      * 以线性的方式向后寻找空白位置，
      * 在查找时候遇到空白位置即停止
      * 在删除某个冲突元素的时候，将删除后的位置的值作特殊处理
      * 可能会产生聚集：多个元素在表中连续吗，导致哈希表性能下降
    * 二次探测
      * 在产生冲突后，使用`x+n^2`为跨度进行空白位置的探测，但探测的步长任然是固定的
    * 再哈希法
      * 当产生冲突时，将关键字用另外一个哈希函数再次哈希化

* 哈希函数的选择：
  * 霍纳法则
* 哈希表的长度：为了实现尽可能最大的均匀分布，在设置哈希表长度的时候尽量使用质数，以及N次幂的底数

### 实现一个Hash函数

```js
/*
* 设计一个哈希函数
* 将字符串转换为比较大的数字HashCode
* 然后将Hashcode压缩到数组的范围之内
* */
function hashFunc (str, size) {
  // 初始化hashCode
  let hashCode = 0
  // 霍纳算法，使用一个质数和Unicode编码迭代计算出该字符串的hashCode
  for (let i = 0; i < str.length; i++) {
    hashCode = 43 * hashCode + str.charCodeAt(i)
  }
  return hashCode % size
}
```

### 判断一个数是否为质数

* 普通的判断：

  ```js
  function (num){
      for(let i=2;i<num;i++){
          if(num %i === 0){
              return false
          }
      }
      return true
  }
  ```

* 更高效的判断:
  如果一个数可以被除了1和本身以外其他数字因式分解，那么他的平方根一定大于等于其因式分解的第一个数，小于等于因式分解的第二个数，如果在平方根到2的范围内不能找到与其整除的数，那么此数就为质数

  ```js
  function (num){
      for(let i=2 ; i<= parseInt(Math.sqrt(num)) ; i++){
          if(num %i === 0){
              return false
          }
      }
  }
  ```

  

## 二叉搜索树（BST）

* 概念：

  * 二叉搜索树可以为空
  * 非空左子树的所有节点键值小于其根节点的键值
  * 非空右子树的所有节点键值大于其根节点的键值

* 基本结构：

  ```js
  class Node {
    constructor (key) {
      this.key = key
      this.left = null
      this.right = null
    }
  }
  
  class BinarySearchTree {
    constructor () {
      this.root = null
    }
  }
  ```

  

* 常见操作：
  * insert(key)

    ```js
    insert (key) {
        const newNode = new Node(key)
    
        if (!this.root) {
          this.root = newNode
        } else {
          this.insertNode(this.root, newNode)
        }
      }
    
      // 递归寻找并插入
      insertNode (treeNode, newNode) {
        // 需判断类型，如果不是Node类的实例，则返回false
        if (!newNode instanceof Node) {
          return false
        }
        if (newNode.key <= treeNode.key) {
          if (!treeNode.left){ // 若孩子节点为空，则直接插入
            treeNode.left = newNode
          } else {  // 否则 递归 插入，知道找到空位置
            this.insertNode(treeNode.left, newNode)
          }
        } else if (newNode.key > treeNode.key){
          if (!treeNode.right){
            treeNode.right = newNode
          } else {
            this.insertNode(treeNode.right, newNode)
          }
        } else {
    
        }
    
      }
    ```

    

  * search(key)

    ```js
    // 使用递归搜索二叉树  
    search(key,node = this.root){
        if(!node){
          return false
        }
        if (key < node.key){
          return this.search(key,node.left) // 此处必须return，不然上级函数无法收到下级的返回值
        } else if (key > node.key){
          return this.search(key,node.right)
        } else {
          return true
        }
      }
    // 非递归搜索二叉树
    searchWithoutRecursive(key){
        let currentNode = this.root
        while (currentNode){
          if (key < currentNode.key){
            currentNode = currentNode.left
          } else if (key > currentNode.key){
            currentNode = currentNode.right
          } else {
            return true
          }
        }
        return false
      }
    }
    ```

  * preOrderTraverse() 前序遍历二叉树

    ```js
    // 前序遍历节点:根左右
    // 递归遍历
      preOrderTraverse(node = this.root) {
        if (!node){
          return
        } else {
          console.log(node.key)
          this.preOrderTraverse(node.left)
          this.preOrderTraverse(node.right)
        }
      }
    
    // 非递归遍历
    preOrderTraverse(root) {
       if(!root){
           return
       }
       let stack = []
       let result = []
       stack.push(root) // 第一次先推入根节点
       while(stack.length){ // 当栈中有值时，继续遍历
           let node = stack.pop()  // 弹出栈顶节点
           result.push(node.value) // 推入节点值
           if(node.right) {     // 先推入右子树，后推入左子树，即左子树先遍历
               stack.push(node.right)
           }
           if(node.left) {
               stack.push(node.left)
           }
       }
    }
    ```

    

  * inOrderTraverse() 中序遍历二叉树

    ```js
    // 中序遍历二叉树:左根右
    // 递归遍历
    inOrderTraverse(node = this.root) {
        if(!node){
            return
        } else {
            this.inOrderTraverse(node.left)
            console.log(node.key)
            this.inOrderTraverse(node.right)
        }
    }
    
    // 非递归遍历，回溯算法
    inOrderTraverse(root) {
        if(!root){
            return
        }
       let stack = []
       let result = []
       let node = root
       stack.push(root) // 第一次先推入根节点
       while(stack.length || node){ 
           if(node){  // 循环找到左子树尽头
               stack.push(node) // 记录过程中的所有双亲节点
               node = node.left //指向左子树
           } else {
               node = stack.pop()
               result.push(node.value)
               node = node.right // 指向右子树，并向左寻找到尽头重复if内的操作
           }
       }
    }
    ```

  * postOrderTraverse() 后序遍历二叉树

    ```js
    // 后序遍历二叉树:左右根
     postOrderTraverse(node = this.root){
         if(!node){
            return
         } else {
            this.postOrderTraverse(node.left)
            this.postOrderTraverse(node.right)
            console.log(node.key)
        }
     }
    ```
    
 * 二叉树广度优先遍历
  
    ```js
    // 递归遍历
    let result = []
    let stack = [root] // 第0个元素作为根开始遍历
    let count = 0  //记录层级，从第0层开始，也就是队列的第0个元素根元素
    function bfs () {
        let node = stack[count] 
        if(node){ // 依次将左右子树推入栈中，并在递归之前，增加层级吗，在下次遍历时，
            result.push(node.value)
            if(node.left) {
                stack.push(node.left)
            }
            if(node.right) {
                stack.push(node.right)
            }
            count ++
            bfs()
        }
    }
    bfs()
   
    //或者
   
    let result = [];
    let stack = [root]; // 先将要遍历的树压入栈
    let bfs = function () {
       let node = stack.shift() // 使用出队列
       if(node) {
           result.push(node.value);
           if(node.left) stack.push(node.left);
           if(node.right) stack.push(node.right);
           bfs();
       }
    }
    bfs();
    console.log(result);
    
    // 非递归
   
    function bfsWithoutRecursive (root) {
       let result = [];
       let queue = [root];    // 使用pointer模拟队列 先将要遍历的树根放入队列中
       let pointer = 0          // 当访问一次队列头的元素，就让指针向后移动一位，即指针之前代表已经出队列的元素
         // pointer < queue.length 模拟队列中的所有元素还未被访问，即队列没有为空
       while(pointer < queue.length){
       let node = queue[pointer++] // 获得指针指向的元素，在让指针增加，相当于出队列
           result.push(node.value) 
           node.left && queue.push(node.left) // 将当前层级的左右子树推入队列中
           node.right && queue.push(node.right)
       }
       return result
    }
    
    bfsWithoutRecursive()
    ```
    

  * min()

    ```js
  min(){
        if (!this.root){ // 空根，退出
          return false
        }
        let currentNode = this.root // 一直向左子树寻找，直到左子树为null
        while(currentNode.left){
            currentNode = currentNode.left
        }
        return currentNode.key
    }
    ```
  
    
  
  * max()
  
    ```js
    max(){
        if (!this.root){
          return false
        }
        let currentNode = this.root
        while(currentNode.right){
          currentNode = currentNode.right
        }
        return currentNode.key
    }
    ```
  
    
  
  * remove(key)
  
    ```js
    removeNode(key){
        let currentNode = this.root
        let prevNode = this.root
        let isLeftChild = true
        if (this.search(key)){
          while (currentNode.key !== key){
            if (key < currentNode.key){
              isLeftChild = true
              prevNode = currentNode
              currentNode = currentNode.left
            } else if (key > currentNode.key){
              isLeftChild = false
              prevNode = currentNode
              currentNode = currentNode.right
            } else {
              console.log(prevNode)
              console.log(currentNode)
              break
            }
          }
    
          // 情况1： 删除的节点是叶子节点，或者是没有孩子的根节点
          if (! (currentNode.left && currentNode.right) ){
            if (currentNode === this.root){
              this.root = null
            } else if ( isLeftChild ){
              prevNode.left = null
            } else {
              prevNode.right = null
            }
            return true
          }
          // 情况2： 删除的节点只有一个子节点
          // 如果删除的节点只有右子节点
          if(!currentNode.left){
            // 如果删除的节点是根节点，且只有右子节点
            if( currentNode === this.root){
              this.root = this.root.right
    
            // 如果删除的节点是上一个节点的左节点
            } else if (isLeftChild){
              prevNode.left = currentNode.right
    
            // 如果删除的节点是上一个节点的右节点
            } else {
              prevNode.right = currentNode.right
            }
    
            return true
          // 如果删除的节点只有左子节点
          } else if(!currentNode.right){
    
            // 如果删除的节点是根节点，且只有左子节点
            if( currentNode === this.root){
              this.root = this.root.left
    
            // 如果删除的节点是上一个节点的左节点
            } else if (isLeftChild){
              prevNode.left = currentNode.left
    
              // 如果删除的节点是上一个节点的右节点，将上一个节点的右节点连接到当前节点的左节点
            } else {
              prevNode.right = currentNode.left
            }
    
            return true
    
          // 情况3：如果删除的节点有2个子节点，情况就复杂起来了
          // 经过多个情况的综合探查，如果要删除有2个子节点的节点，需要找到一个节点将其替换
          // 这个节点必须是删除节点左子树的最大值，称为删除节前驱 或者 右子树的最小值，称为后继
          }  else {
            // 此处的后继要么没有子节点，要么只有右节点，没有左节点
            let successor = this.getNodeSuccessor(currentNode)
            // 如果当前删除的节点是根
            if(currentNode === this.root){
              successor.left = currentNode.left
            }
            // 如果被删除的节点是双亲节点的左节点
            if (isLeftChild){
              prevNode.left = successor
              // 如果删除的节点是上一个节点的右节点
            } else {
              prevNode.right = successor
    
            }
            // 最后将删除的节点的左节点接上后继节点
            successor.left = currentNode.left
            return true
          }
  
        //  没有找到这个节点，返回false
      } else {
          return false
      }
    
    }
      // 获取后继节点
      getNodeSuccessor(node){
        if (!node){
          return false
        }
        let successorParent = node.right
        let successor = node.right
        // 找到后继节点
        while(successor.left){
          successorParent = successor
          successor = successor.left
        }
        // 如果后继节点不是删除的节点的右子树的根
        if (successor !== node.right){
          // 让后继节点的右节点成为 后继节点的双亲节点的左节点（因为后继节点上所有节点都比后继节点的双亲节点小）
          successorParent.left = successor.right
          // 让后继节点的双亲节点成为 后继节点的右节点（比后继大的节点）
          successor.right = node.right
        }
    
        return successor
      }
    ```
  
    在实际操作中，删除二叉树节点操作编码比较复杂，而且开销相对较大，所以一般情况下，尽量避免删除操作，而是给Node节点类添加一个`isDelete`的Boolean值，来标识当前节点是否删除，这样只需要寻找到删除的节点即可，而标识了被删除的节点在寻找时不会被返回，而是向下寻找或者跳出，但是这种处理方法会浪费大量的空间。
  
    ### 寻找二叉树的最小深度
  
    ```js
  function run( root ) {
        if(!root)
        return 0;
        if(!root.left && !root.right)
        return 1;
    
        let depth = 0
        let queue = []
        queue.push(root);
        while(queue.length){
            let len = queue.length // 必须记录上次放入的节点数，否则depth不会增加
            depth++;
            for(let i = 0; i < len; i++){
                let cur = queue.shift()        
                if(!cur.left && !cur.right)
                    return depth;
                if(cur.left)
                    queue.push(cur.left);
                if(cur.right)
                    queue.push(cur.right);
            }              
        }
        return 0;
    }
    ```
    
    
    
    ### 二叉搜索树的缺陷
    
    当按照有序，或者大部分有序的的方式插入数据，二叉搜索树的根节点会出现左子树过小，右子树过大，和与之相反的情况，二叉搜索树将失去平衡
    
    解决方法：
    
    * 使用AVL树，自平衡二叉查找树，每个节点多存储了一个额外的数据
    * 红黑树：目前广泛使用的平衡树，使用一些特性来保持平衡，在插入、删除操作时，性能优于AVL树
      * 节点只有红色和黑色两种
      * 根节点是黑色
      * 每个叶子节点都是黑色的空节点(NIL)
      * 每个红色节点的两个子节点都是黑色
      * 从任意节点到其每个叶子姐弟啊你的所有路径都包含相同数目的黑色节点

## 线索二叉树

普通完全二叉树的问题：n个节点一共有2n个指针，除了根节点之外，n-1个节点都被2n个指针中的n-1个指针引用着，所以剩下2n-(n-1) = n + 1个指针域为null

如果我们将这n + 1 个指针域在某种遍历次序下存放下一个遍历的前驱或者后继，那么这样的二叉树成为线索二叉树

按照遍历次序不同可分为三种线索二叉树：前序、中序、后序

空节点指向规则：

* 如果节点的左指针没有被使用，那么左指针指向遍历次序的前驱节点
* 如果节点的右指针没有被使用，那么右指针指向遍历次序的后继节点

```js
// 线索化二叉树方法,在搜索二叉树的基础上实现
inOrderThreadedNodes = (node = this.root) => {
    if (!node){
        return
    }
    // 线索化左子树
    this.inOrderThreadedNodes(node.left)
    // 处理前驱节点
    if (!node.left){
        node.left = this.pre
        node.leftIstree = false
    }
    // 处理后继节点，使用一个全局变量指向中序遍历的上一个节点，为上一个节点线索后继指针
    // 当pre不为空，且pre的后继指针没有被使用的时候，pre的后继就是当前节点
    if (this.pre && !this.pre.right){
        this.pre.right = node
        this.pre.rightIstree = false
    }
    // 处理节点后，改变pre成为下一个前驱节点
    this.pre = node

    // 线索化右子树
    this.inOrderThreadedNodes(node.right)
}

inOrderTraverseThreadedNodes = () => {
    let p = this.root
    // 当p不为空的时候循环
    while (p){
        // 当p的节点的左指针指向的是子树而不是前驱，则一直循环直到找到最左的元素
        while (p.leftIstree){
            p = p.left
        }
        // 输出这个值
        console.log(p.key)
        // 如果p的右节点指向的不是子树，而是后继节点，则直接向后继节点访问
        while (!p.rightIstree){
            p = p.right
            console.log(p.key)
        }
        // 当遇到了右指针指向子树的节点，则跳转到这个节点，继续循环以上步骤，直到p为null
        p = p.right
    }

}
```



## 红黑树（概念）

红黑树规则
1. 节点只能是红色和黑色
2. 根节点是黑色
3. 每个叶子节点都是黑色且空节点（NIL节点）
4. 黑节点可以连续，红节点不能连续出现
5. 红节点的子节点都必须是黑色节点
6. 从任意节点到其子树的叶子节点的路径上包含相同的黑色节点

红黑树特性：

*   从根到叶子的最长路径，不会超过最短路径的两倍

红黑树变换

*   插入：新节点一般默认为红色节点，如果新节点为黑色会难以调整
*   左旋转：逆时针旋转
*   右旋转：顺时针旋转

红黑树插入

相关节点角色：红色新节点:N  新节点的兄弟节点:B	新节点的双亲节点:P	新节点的祖节点:G	新节点的双亲节点的兄弟节点:U

在搜索二叉树规则下进行插入新节点替换为某个黑色NIL节点之后，在红色新节点上添加2个叶子节点NIL，此时有5种可能的情况

1. N是根节点，直接将N的红色变为黑色（规则2）

2. N的P是黑色，不做任何变换

3. P是红色，U也是红色，G一定为黑色，此时需将P和U变为黑色，G变为红色

   1. 如果变化后G为根节点，则将G以及整个子树都插入到内容为空的红黑树中，即G变为黑色

   ![image-20200822114725471](https://gitee.com/EricReeves/proxy/raw/master/img/image-20200822114725471.png)

4. N是P的左子节点，P为红色，且P的右子节点不为空，U为黑色，G为黑色，此时需要将P变为黑色，G变为红色，再进行右旋转

   ![image-20200822115425240](https://gitee.com/EricReeves/proxy/raw/master/img/image-20200822115425240.png)![image-20200822115551815](https://gitee.com/EricReeves/proxy/raw/master/img/image-20200822115551815.png)

5. N是P的右子节点，P为红色，且P的左子节点B不为空，U为黑色，G为黑色，此时

   1. 以P为根进行左旋转
   2. 将G变为红色，N变为黑色，以G为根进行右旋转   

   ![image-20200822123122453](https://gitee.com/EricReeves/proxy/raw/master/img/image-20200822123122453.png)

## 内部排序

* 插入排序

  基本思路：以第一个元素作为参照，将之后的元素插入到前方有序数组中

  ```js
  function insertSort (arr) {
      let arrCpy = arr.slice(0)
      let i,j
      let temp
      for (i = 1; i < arrCpy.length; i++) { // 一共循环n-1次，因为一第一个元素为参照进行插入
          temp = arrCpy[i]	// 保存要插入值
          // 从前面的有序序列的最后一个位置开始向前与插入值比较，如果遇到比插入值大的元素，则将该元素往后移一位
          for (j = i-1; j >=0 && arrCpy[j] > temp; j--) { 
              // 如果遇到元素的值比插入值小，则直接跳出循环，此时j指向的是比插入值小的元素，j+1此时要么指向i 要么指向已经向后移位的元素
              arrCpy[j+1] = arrCpy[j]
          }
          arrCpy[j+1] = temp // 将值插入进去
      }
      return arrCpy
  }
  console.log(insertSort([4,5,7,5,4,23,6]) )
  ```

* 选择排序

  基本思路：每一次循环从数组中选择一个最小/大的数，并记录下标，在循环结束后交换位置

  ```js
  function selectSort (array) {
      const arrCpy = array.slice(0)
      let i,j,minIndex
      for( i = 0; i<arrCpy.length - 1 ;i++){
      	minIndex = i // 保存初始下标
          for( j = i + 1 ; j < arrCpy.length ; j++){
              // 如果找到比minIndex位置小的元素，则更新minIndex
              if(arrCpy[j] < arrCpy[minIndex]){ 
                  minIndex = j
              }
          }
          if(minIndex !== i){
              [arrCpy[i],arrCpy[minIndex]]=[arrCpy[minIndex],arrCpy[i]]
          }
      }
      return arrCpy
  }
  ```

* 交换排序

  基本思路：每一次循环都对比两个数的大小，满足条件就进行交换

  ```js
  function swapSort(array) {
    const arrCpy = array.slice(0)
    for(let i = 0 ; i < arrCpy.length -1 ; i++){
      for(let j = i + 1 ; j < arrCpy.length ; j ++){
        if(arrCpy[i] > arrCpy[j]){
          [arrCpy[i], arrCpy[j]] = [arrCpy[j], arrCpy[i]]
        }
      }
    }
    return arrCpy;
  }
  ```

* 冒泡排序

  ```js
  function bubbleSort(array) {
    const arrCpy = array.slice(0)
    let flag = true
    for(let i = 0; i< arrCpy.length  ; i++){
      flag = true
      for(let j=0 ; j< arrCpy.length -i ; j++){
        if(arrCpy[j] > arrCpy[j+1]){
          [arrCpy[j],arrCpy[j+1]] = [arrCpy[j+1],arrCpy[j]]
          flag = false
        }
      }
      if(flag){ // 如果没有进行过冒泡过程，说明数组已经有序
        break
      }
    }
    return arrCpy;
  }
  ```

* 希尔排序

  属于插入排序的改进版本，需要选择合适增量，通过从数组中间元素开始向前的位置进行插入排序

  ```js
  function shellSort (array) {
    const arrCpy = array.slice()
    const length = arrCpy.length
    // 初始化间隔
    let gap = Math.floor(length / 2)
    let i, j
    // 当间隔大于1时循环
    while (gap >= 1) {
      // 插入排序的起始位置从数组中间开始，每次都向后方移动一位，与前面对应间隔位置处进行插入排序
      for (i = gap; i < length; i++) {
        // 保存插入值
        const temp = arrCpy[i]
        // 从插入值i元素的前一个间隔位置开始与 插入值i 比较，如果大于则使用j以间隔向后覆盖
        // 如果遇到小值或者已经到达边界，退出，插入值到j的上一个位置
        for (j = i - gap; temp < arrCpy[j] && j >= 0; j -= gap) {
          arrCpy[j + gap] = arrCpy[j]
        }
        arrCpy[j + gap] = temp
      }
      // gap减半继续循环
      gap = Math.floor(gap / 2)
    }
    return arrCpy
  
  }
  console.log(shellSort([15, 4, 2, 6, 12, 4, 3, 5, 123, 2352, 3]))
  ```

* js快速排序

  * 目前最优的排序算法，O(n*log2 n)
  * 改进版的冒泡排序，每经过一个循环，就确定一个元素在数组中所在的最终位置，不会再被位移
  * 选择枢纽，使得枢纽左右相对平衡或者随机平衡
  * 快速排序可以在一次大循环中（包含递归），找出某个元素的正确的位置，并且不需要再次移动
  * 核心思想：分而治之，选择一个基数，将数组分为2部分，将小于基数的放在左边，大于基数的放在右边，由此可以得到基数最终的位置
  * 选择一个枢纽，取头 中 尾元素中的一个中位数，例如头部第一个值为3 中部的值为8 尾部值为 1，则选择中部的8作为枢纽

  ```js
  function getPivot (array, left, right) {
    // 取数组中部index
    let center = Math.floor((left + right) / 2)
    // 将前 中 后 三个元素进行排序并交换位置
    if (array[left] > array[right]) {
      [array[left], array[right]] = [array[right], array[left]]
    }
    if (array[left] > array[center]) {
      [array[left], array[center]] = [array[center], array[left]]
    }
    if (array[center] > array[right]) {
      [array[right], array[center]] = [array[center], array[right]]
    }
    // 此时得到有序的前 中 后 三个元素，中间的元素被选择为第一次递归的排序枢纽
    // 将中部元素与末尾元素的前一个元素进行交换，因为末尾元素在上面三元素排序的时候已经确定比枢纽元素的值大
    [array[center], array[right - 1]] = [array[right - 1], array[center]]
  
    //返回处于倒数第二个位置的枢纽元素
    return array[right - 1]
  }
  
  
  function quickSort (array) {
    let arrCpy = array.slice(0)
    quick(arrCpy, 0, arrCpy.length - 1)
    return arrCpy
  
  }
  
  // 第一种递归
  function quick1 (array, low, high) {
    // 结束条件
    if (low >= high) {
      return
    }
    // 得到当前分组的枢纽
    let pivot = getPivot(array, low, high) //得到枢纽，以及处于倒数第二个元素的子数组
    let i = low
    let j = high - 2  // 从当前子数组倒数第二个元素开始
    while ( true ) {
      // 从低位到高位扫描，当扫描元素值小于等于枢纽值则继续扫描，当扫描值大于枢纽值，跳出
      while (array[i] < pivot ) {
        i++
      }
      // 跳出从低位到高位扫描后，从高向低扫描，当扫描元素值大于等于枢纽值则继续扫描，当扫描值小于枢纽值，跳出
      while (array[j] > pivot ) {
        j--
      }
      // 当以上循环均跳出，且i和j没有穿过彼此(i是要小于j才说明扫描并未结束)，交换双方位置的值，否则，这一轮扫描与交换结束
      if (i < j ) {
        [array[i], array[j]] = [array[j], array[i]]
      } else {
        break
      }
    }
    [array[i], array[high - 1]] = [array[high - 1], array[i]]
  
    arguments.callee(array, low, i - 1)
    arguments.callee(array, i + 1, high)
  }
  // 第二种递归
  function quick (array, low, high) {
    // 结束条件
    if (low >= high) {
      return
    }
    // 得到当前分组的枢纽
    let pivot = getPivot(array, low, high) //得到枢纽，以及处于倒数第二个元素的子数组
    // 在寻找完枢纽后，三个以下的元素不用再继续排序，否则两两排序会出现问题
    if(high - low <= 2 ){
      return
    }
    let i = low
    let j = high - 1  // 从当前子数组倒数第二个元素开始
    while ( true ) {
      // 从低位到高位扫描，从最低位的后一位开始，当扫描元素值小于等于枢纽值则继续扫描，当扫描值大于枢纽值，跳出
      while (array[++i] < pivot ) {}
      // 跳出从低位到高位扫描后，从枢纽位的前一位开始，从高向低扫描，当扫描元素值大于等于枢纽值则继续扫描，当扫描值小于枢纽值，跳出
      while (array[--j] > pivot ) {}
      // 当以上循环均跳出，且i和j没有穿过彼此(i是要小于j才说明扫描并未结束)，交换双方位置的值，否则，这一轮扫描与交换结束
      if (i < j ) {
        [array[i], array[j]] = [array[j], array[i]]
      } else {
        break
      }
    }
    // 如果i与high -1 指向的不是同一个位置，则交换
    if(i !== high -1)
    [array[i], array[high - 1]] = [array[high - 1], array[i]]
  
  
    arguments.callee(array, low, i - 1)
    arguments.callee(array, i + 1, high)
  }
  ```



## 常用算法

### 计算逆波兰式（后缀表达式）的值

* 使用递归方法 加 字符串拼接和eval(）

  ```js
  function evalRPN (tokens) {
    if (tokens.length > 1) {
      for (let i = 2; i < tokens.length; i++) {
        if (/^[+\-*\/]$/.test(tokens[i])) {
          let res = `(${tokens[i - 2]})${tokens[i]}(${tokens[i - 1]})`
          tokens.splice(i - 2, 3, parseInt(eval(res)))
          evalRPN(tokens)
        }
      }
    }
    return tokens[0]
  }
  console.log(evalRPN(["4", "13", "5", "/", "+"]))
  ```

* 栈方法

  ```js
  function evalRPN2 (tokens) {
    const arrCpy = tokens.slice(0)
    const temp = []
    const reg = /^[+\-*\/]$/
    const _operators = {
      '+': (a, b) => (+a) + (+b),
      '-': (a, b) => (+a) - (+b),
      '*': (a, b) => (+a) * (+b),
      '/': (a, b) => (+a) / (+b),
    }
    while (arrCpy.length > 0) {
      if (!reg.test(arrCpy[0])) {
        temp.push(arrCpy.shift())
      } else {
        const num2 = temp.pop()
        const num1 = temp.pop()
        const operator = arrCpy.shift()
        temp.push(_operators[operator](num1, num2))
      }
    }
    return temp.toString()
  }
  ```

### 转换数字为分隔格式

```js
function paddingNum(number){
  let str = number.toString()
  let arr = str.split('.')
  let left = arr[0].split('') //得到整数部分
  for (let i = left.length - 3; i > 0 ; i -= 3) { //从倒数第三位开始，向前扫描，并插入逗号
    left.splice(i, 0, ',')
  }
  // 返回处理后的整数部分+小数部分
  return left.join('').concat(arr[1] ? '.'+arr[1]:'' )
}
```

### 递归实现数组扁平化

```js
function steamrollArray(arr) {
  // 扁平化，扁平化……
  let res= []
  arr.forEach(item => {
    if(item instanceof Array){
      res.push(...steamrollArray(item)) // 调用自身为了深入类型还是Array的元素，使用...为了将返回的数组类型的res进行解开作为上一个push的多个参数，依次递归
    }
    else {
      res.push(item)
    }
  })
  return res;
}

// 使用reduce
function flatten (array) {
  return array.reduce((prev, cur) => 
            prev.concat(Array.isArray(cur) ? flatten(cur) : cur))
}
// 使用some + concat
function flatWithConcat (array) {
  while (array.some(item => Array.isArray(item))){
    array = [].concat(...array)
  }
  return array
}
```
### 数组去重
```js
// ES7解决方案
function removeDuplicate (array) {
  var res = []
    array.forEach(item => {
      res.includes(item) && res.push(item)
    })
    return res
}

// ES6解决方案
function removeDuplicate1 (array) {
  return Array.from(new Set(array))
  // 或者 return [...new Set(array)]
}

// ES5
function removeDuplicate2 (array) {
  var res = []
  array.forEach(item => {
    res.indexOf(item) === -1 && res.push(item)
  })
  return res
}

// 使用filter()
function removeDuplicate3 (array) {
  var res = []
  array.filter(item => {
    res.indexOf(item) === -1 && res.push(item)
  })
  return res
}
```

### 实现斐波拉契数列

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
  return f1
}
```

