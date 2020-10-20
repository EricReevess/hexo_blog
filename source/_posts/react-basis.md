---
title: React基础与学习笔记
tags:
- "React"
- "JavaScript"
- "前端框架"
categories: 
 - React
keywords: "前端, JavaScript, React"
cover: https://frontendmasters.com/static-assets/learn/og-learning-path-react.jpg
---
> React 是一个声明式，高效且灵活的用于构建用户界面的 JavaScript 库。使用 React 可以将一些简短、独立的代码片段组合成复杂的 UI 界面，这些代码片段被称作“组件”。
>
> 本文部分内容来源于React官方文档

# JSX

JSX是JavaScript的扩展语法，即 JavaScript XML。在React中使用JSX，JSX 可以很好地描述 UI 应该呈现出它应有交互的本质形式，且具有 JavaScript 的全部功能，JSX 可以生成 React “元素”。

## 为什么要使用JSX？

* React认为 元素的逻辑和以及UI存在一定的耦合，一些HTML元素与JS的事件操作相连，但是React并没有采用将视图和逻辑处理分离在不同的文件中，而是将视图和逻辑处理一同存放在称之为“组件”的松散耦合的单元内，实现 关注点分离 ，JSX本身也是一种表达式，在最终打包编译时会转换为普通的JS文件
* JSX 能有效防止跨站脚本攻击( XSS )，因为在React DOM 渲染所有内容之前都被转换成了字符串

## JSX属性

使用大括号`{}`可插入JavaScript表达式，或者使用引号`" "`直接插入字符串值

## JSX 变量命名

JSX的元素属性跟随React DOM使用小驼峰命名 className，不使用HTML的属性 命名约定，即`-`间隔的命名，JSX对于元素的声明，类似xml，当标签内部无内容时，可直接使用 /> 来进行闭合

## JSX 的表示对象

`Bable`规范会将JSX转译成一个名为 `React.createElement()` 函数调用，`React.createElement()` 会预先执行一些检查，并在内部创建一个React元素对象

```js
const element = (<h1 className="welcome">hello</h1>)
//等价于
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
//相当于创建了这个对象
const element = {
    type:'h1',
    props:{
        className:'welcome',
        children:'hello'
    }
}
```

# React元素渲染

* React 元素元素描述了你在屏幕上想看到的内容，此处的React元素非 React组件
* React 元素 是构成 React 应用的最小砖块，且React DOM 会负责更新 浏览器原生DOM 来与 React 元素保持一致。

## 渲染React 元素为 浏览器 DOM

id为root的根节点的所有内容均由React DOM管理，仅使用 React 构建的应用通常只有单一的根 DOM 节点。 
将 React 集成进一个已有应用，那么可以在应用中包含任意多的独立根 DOM 节点。

* 想要将React 元素 渲染到DOM中，只需要将它们传入到 `ReactDOM.render()` 中

```jsx
const element = <h1>Hello there</h1>
ReactDOM.render(element,document.getElementById('root'))
```

## 更新已渲染的React 元素

React元素使用const 进行声明，所以为**不可再赋值对象**，一旦被创建，就无法更改它的子元素或者属性，此时这个React元素表了某个特定时刻的 UI  
更新UI的唯一方式就是创建一个全新的元素，使用`ReactDOM.render()`重新进行渲染。  
想要定时更新UI ，使用`window.setInterval()`重新声明React元素，调用`render()`即可 而且 React 只更新它需要更新的部分

```jsx
//动态渲染当前时间
function currentTime() {
  const element = (
    <div>
      <h1>你好世界!</h1>
      <h2>当前时间： {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(element, document.getElementById('root'));
}

setInterval(currentTime, 1000);
```

## React组件和Prop

组件: 将UI拆分为独立可复用的代码片段，每个片段拥有独立的构思，组件名称必须以大写字母开头

一种常见的应用模式是尽可能减少状态组件并创建无状态的函数组件。这有助于将状态管理包含到应用程序的特定区域。反过来，通过更容易地跟踪状态变化如何影响其行为，可以改进应用程序的开发和维护。

* 组件的定义方式

  * 函数组件：使用JavaScript函数进行返回一个 React元素 

    ```jsx
    function Hello(props){
        return <h1>Hello World,I'm {props.name}</h1>
    }
    ```

    * 组件名字首字母一定是大写的

    * 返回一个jsx、或者一个字符串，jsx依赖React，所以组件内部需要**引入React**。
    * 使用
      * 以标签的方式使用`<Component/>` ，
        * 组件标内的内容不会展示出来
        * 组件的attributes内className不起作用
      * 直接在双括号内调用`{ Component() }`

    * 组件传参
      * 传递： `<Component list={ arrData }><Component>`
      * 接收： function Component( props ){...}
      * 使用： `const { list } = props`，`list`就是参数数据 

    * 缺点：
      * 无状态组件：只能实现很简单的视图展示功能，没有自己的内容数据、没有状态，没有逻辑处理
      * 没有`this`
      * 没有生命周期，但16.7以后版本的react提供了React Hook
      * 内部不用render函数，会自动把return返回结果当做render返回结果

  

  * 类组件：使用ES6的类(class)继承`React.Component`来定义的组件

    ```jsx
    class Hello extends React.Component{
        render(){
            return (
                <h1>Hello World,I'm {this.props.name}</h1>
            )
        }
    }
    ```

    

### 渲染组件

react元素可以是普通HTML标签，也可以是自定义组件标签

```jsx
const element = <Hello name="jack" />
ReactDom.render(element, document.getElementById('root'))
```

### 组合组件

可以将多个组件组合起来组成另一个React元素，但是每个React应用的最顶层组件都是App组件

### 拆分组件

将组件拆分为更小的组件，在通过行内`attribute`方式传递值

### props的只读性质

在JS中 ，不改变传入参数的函数称之为 纯函数，相反，在函数执行过程中改变了参数的函数称之为 非纯函数，
所有React的组件必须像纯函数一样保护props，props无法被组件自身修改， 但是state可以

### state 以及 生命周期

`state` 与 `props` 类似，但是 `state` 是私有的，并且完全受控于当前组件，`state`声明在以class创建的React组件内部的`constructor()`中，通过传入`props`，使得`state`可以接收`props`传递的数据  
当组件第一次被渲染到页面时，在React生命周期中称为 挂载(mount)， 当组件被删除时，称之为卸载(unmount)  

`componentDidMount()` 方法会在组件已经被渲染到 DOM 中后运行 
`componentWillUnmount() `方法会在组件在DOM删除之前 运行

```jsx
class ClockComponent extends React.Component{
  constructor(props){
    super(props);
    this.state = {
      data:new Date()
    };

    componentDidMount(){
      this.clockInterval = setInterval(
        ()=>this.clock(),
        1000
      )
    }
    componentWillUnmount(){
      clearInterval(this.clockInterval);
    }

    clock(){
      this.setState({
        data:new Date()
      });
    }

    render(){
      return (
        <div>
        <span>{this.state.data.toLocaleTimeString()}</span>
        </div>
      );
    }
  }
}
ReactDOM.render(<ClockComponent />, document.getElementById('root'));
```

### State 注意事项

不要直接修改`state`： this.state.xxx = 'xxx'; 为错误方法
除了使用`this.setState()`传入新对象进行更新，构造函数是唯一可以给 `this.state` 赋值的地方

`this.setState()`，该方法接收两个参数：对象或函数:

* 对象：要修改的state的对象形式；
* 函数：接收2个函数：  
  第一个函数：参数为state和props，返回类型为一个对象，为state对象的形式  
  第二个函数：在state改变后触发的回调函数

### State 的更新可能是异步的

* 出于性能考虑，React 可能会把多个 `setState()` 调用合并成一个调用

每当调用this.setState()时，不一定同步更新内部`state`，换句话说，`this.state()`可能不会立即执行 ,因此不会造成阻塞

> setState何时同步何时异步？
>
> 由React控制的事件处理函数:React封装的事件，比如onChange、onClick、onTouchMove等，以及生命周期函数调用setState()不会同步(立刻)更新state 。

例如在点击按钮直接调用this.setState()的时候，React不会立即更新state中的内容，而是先渲染到页面上，延迟更新state

```jsx
constructor(props) {
    super(props)
    this.state = {
        data:'a'
    };
    this.click = this.click.bind(this)
}
click(){
  this.setState({data:'b'});
  console.log(this.state.data); // log -> a
}
```

要解决显示改变的结果，则将函数传入setState()的第二个参数即可在改变`state`后执行函数

```jsx
click(){
  this.setState((state,props)=>({
    data:'b'
    }),()=>{
      console.log(this.state.data); // log -> b 
    });
  
}
```

### State 的更新会被合并

> React的state的更新为浅合并，可以单独的更新(替换)指定的一个或者多个变量，而不需要提供整个state。

 React 会把以传入对象的形式调用 `setState()` 提供的对象合并到一个对象中，并更新一次状态，并且值调用一次`render()`更新界面 
 
 React 调用多次传入函数形式的 `setState()`，来多次更新状态，不会合并返回的对象，但只调用一次`render()`更新界面 

当传入函数的形式调用 `setState()` 的时候
### 单向数据流

* React中不管是任何等级的组件，都无法知道其他组件的`state`，依靠`state`的局部封装特征  

* 父组件可以使用它的`state`作为一个子组件的行内`attribute` 向下 传递到子组件的`props`中，但是子组件无法得知传入的props数据是来自子组件的默认props值，还是来自于子组件的`state`、或者手动设置的`props`（从实验来看，`props`是只读对象）  

* 任何的 `state` 总是所属于特定的组件，而且从该 `state` 派生的任何数据或 UI 只能影响树中“低于”它们的组件。

## 事件处理

React的时间均采用小驼峰式命名 camelCase 在JSX语法中 需要传入一个{}包裹的函数，而不是""包裹的字符串,且{}中可以为真值表达式、或者三目运算表达式  

```jsx
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

同样在回调函数中，使用参数e或者ev、event来访问事件对象    

在 JavaScript 中，class 的方法默认不会绑定 this。所有在class内部自定义的函数，都需要在构造器内部使用bind将this进行绑定  
或者不使用这种绑定，在声明自定义函数的时候可将函数变为一个箭头函数，或者在绑定事件处，将未绑定的函数作为函数体

```js
onClickHandler = (e) =>{
  //函数处理
}
<button onClick = {this.onClickHandler}></button>


onClickHandler (e) {
  //函数处理
}
<button onClick = { ()=>this.onClickHandler() }></button>
```

### 向事件处理函数中传递参数

通常在一些列表中需要为每一项的删除、修改等按钮  

在绑定事件时使用箭头函数，事件对象event必须显式传递  

```jsx
<button onClick = { (e)=>this.onClickHandler(e, id) }></button>
```

在事件回调为箭头处理函数的时候，需要通过bind方式，事件对象会被隐式传递  

```jsx
<button onClick = {this.onClickHandler.bind(this, id) }></button>
```

## 条件渲染

使用另一个函数组件通过使用if判断prop来进行渲染

* 使用 与运算符 &&  
  在 JavaScript 中，true && expression 总是会返回 expression, 而 false && expression 总是会返回 false。
* 使用 三目运算符

### 阻止组件渲染

直接在函数组件内条件返回 null 或者class组件内的render()返回null则可阻止渲染，但是不影响组件的生命周期，只是渲染内容为空而已

## 列表渲染

列表渲染需要使用到Array.map()方法，可操作数组内部的每一个迭代的元素，并在最后返回一个新数组

```jsx
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((number) => number * 2); //-> 1 4 9 16 25
```

React的渲染列表的规则则是在map中返回一个带有数据的模板数组，并且每个迭代的项目必须分配一个唯一的key属性  
而且key属性是只写属性，不可读，只会传递给React，不会传递给组件，不能通过项目上的props进行访问，如果要标识每个迭代项，则需要另外使用属性进行代替，  
如果你选择不指定显式的 key 值，那么 React 将默认使用索引用作为列表项目的 key 值。 

> 最将key设置为给的迭代的数据中的id，而不是map迭代的index，因为这样可能因为列表的顺序变化而使性能变差  

```jsx
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((number, index) => <li key={index} index={index}>{number*2}</li>); //-> 1 4 9 16 25
ReactDOM.render(
  <ul>{doubled}</ul>, 
   document.getElementById('root'))

// 或者这样，但是最好进行组件的提取
const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <ul>
  {
    numbers.map((number, index) => 
      <li key={index} index={index}>
        {number*2}
      </li>)
   }
  </ul>, 
   document.getElementById('root'))

```

元素的 key 只有放在就近的数组上下文中才有意义，换句话说，必须在数据的map方法中对迭代项目进行key属性的添加。

###  key 只是在兄弟节点之间必须唯一

不同的map方法中的key可以相同，不需要要全局唯一

# 表单

React中的表单一般意味着表单的组件时有状态(state)组件，表单默认拥有默认行为——跳转页面，大多数情况下，使用监听组件标签的值，将其值使用函数设置在state中，这种表单组件称之为 受控组件 —— 表单的值被JS事件函数所控制，而不是被元素本身所控制，state为表单唯一数据源

## input 和 textarea 

把setState作为唯一的数据源

```jsx
InputHandler = (event) =>{
    this.setState({
        inputVal:event.target.value,

    })
}

<input value={this.state.inputVal} onInput={this.InputHandler}/>
```

## select 以及 option

React不会在option标签上使用selected来选择项，而是在跟select标签上使用value来设置默认值，在使用onChange事件监听选中的option的value来设置state

```jsx
SelectHandler = (event) =>{
  this.setState({
      selectFruit:event.target.value,
  })
}

<select value={this.state.selectFruit} onChange={this.SelectHandler}>
    <option disabled value="">--请选择--</option>
    <option value="apple">苹果</option>
    <option value="banana">香蕉</option>
    <option value="pear">梨</option>
    <option value="orange">橘子</option>
</select>
```

## 文件input标签

```jsx
<input type="file" />
```

## 处理多个输入

给每个input标签添加name属性来对获取的vale进行处理

```jsx
const value = target.value;
const name = target.name;
this.setState({
  //这里使用ES6动态属性名称进行更新
  [name]: value
})
//等同 ES5:
var updateObj = {};
updateObj[name] = value;
this.setState(updateObj);

```

将input标签的value设置为null 或者undefined ，组件输入则不受控制

# 状态提升

如果以个父组件内部的一个子组件的状态需要共享给另一个子组件，则可以将它们共同需要的状态提升到父组件的状态中，然后再从父组件中传递到子组件内，父组件作为数据的唯一源，掌握状态控制权，且子组件没有直接控制权，或者说子组件只能通过父组件向子组件传递的props中得到父组件的接口函数，进而调用父组件的某个状态（最好在子组件的自定义函数中调用props中传入的父组件回调）

# 组合与继承

每个子组件中的props.children接收来自父组件插入到子组件标签的内容，在子组件内部使用{props.children}进行放置插入的内容，类似于Vue的插槽slot功能，或者可以在标签的prop中传递一个组件标签，然后再通过props访问

```jsx
//父组件中
<ComponentA 
    <h1>内容</h1>
</ComponentA>
//子组件ComponentA中
<div>
	{props.children}
</div>
```



在React构建的应用中，几乎没有用到继承这一属性的组件，每个组件都可能被高度自定义；

如果要复用非UI的功能而使用继承，建议将其提取为一个单独的 JavaScript 模块，如函数、对象或者类。组件可以直接引入（import）而无需通过 extend 继承它们。

Props 和组合为你提供了清晰而安全地定制组件外观和行为的灵活方式。注意：组件可以接受任意 props，包括基本数据类型，React 元素以及函数。

# React 构建艺术

React最大的优点就是让我们思考如何科学的、规范的构建一个应用

1. 我们先要得到设计师的数据稿件，或者服务器后台工作者提供的JSON数据，依此作为组件和状态的规划
2. 将设计好的UI划分为组件层级，PS图层中的图层名可以被参考为组件名，从而保持美工与程序的一致性
   * 使用单一功能的原则来判断组件包括的范围，一个组件只负责一个功能，而不对其他组件产生依赖
   * 对展示组件内不同类型的数据样式做出决定  
3. 使用React创建一个规划好的静态页面，不需要使用state和prop
4. 确定UI 组件 的state最小的表示(初始化组件需要的最小状态)，以及state放置的位置
   * 遵循**一次且仅一次**的原则
   * 检查JSON判断数据是否属于当前组件的state
     * 该数据是否是从父级通过props传递过来？如果是，则不属于state
     * 该数据是否随着时间的推移产生变化？如果是，则属于state
     * 该数据是否可以通过props和state中的值计算得出？如果不是，则属于state
5. 添加反向数据流，向下层传递函数，向上层传递state数据以调用setState



# React高级

## 组件性能头优化

继承Component编写的的类组件存在一个问题：
1. 父组件在执行`render()`进行重渲染时候，当前组件也会执行`render()`，即使没有任何变化
2. 当前组件设置更新state后若无任何变化，还是会调用`render()`重新渲染

解决方法：
1. 在当前组件生命周期函数 `shouldComponentUpdate()` 中决定返回布尔值，`shouldComponentUpdate()` 默认会返回`true`，导致不必要的更新
2. 使用PureComponent代替Component

## React.lazy函数

使用React.lazy()函数动态引入一个外部组件

React.lazy()接收一个函数，这个函数要动态调用import()，而非这个函数返回的是Promise对象，该 Promise 需要 resolve 一个 `defalut` export 的 React 组件。

```jsx
const OtherComponent = React.lazy(() => import('./OtherComponent'));
```

## Context 与 跨层传递数据

context的目的是共享作用整个组件树的全局数据，能让你将这些数据向组件树下所有的组件进行“广播”，所有的组件都能访问到这些数据，也能访问到后续的数据更新。

在一般情况下，从父组件传递到孙组件需要通过中间组件来逐层传递，使用context我们可以避免传递经过中间组件多次prop

* Context 对象

  创建一个 Context 对象。当 React 渲染一个订阅了这个 Context 对象的组件，这个组件会从在组件树中离自身最近的那个匹配的 `Provider` 中读取到当前的 context 值。

```jsx
const MyContext = React.createContext(defaultValue);
```

> **只有**当组件所处的树中没有匹配到 Provider 时，其 `defaultValue` 参数才会生效

* MyContext.Provider

  使用根级别的Context广播提供者，对下层提供值为value的广播，下层组件需要使用contextType来指定Contex广播对象

```jsx
<MyContext.Provider value={/* 某个值 */}> 放置被广播的组件 <MyContext.Provider/>
```

>  每个 Context 对象都会返回一个 Provider React 组件，它允许消费组件订阅 context 的变化。
>
>  Provider 接收一个 `value` 属性，传递给消费组件。多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据
>
>  当 Provider 的 `value` 值发生变化时，它内部的所有消费组件都会重新渲染。Provider 及其内部 consumer 组件都不受制于 `shouldComponentUpdate` 函数，因此当 consumer 组件在其祖先组件退出更新的情况下也能更新。

* Class.contextType

  在类组件中，订阅一个Context 对象

  ```jsx
  class MyClass extends React.Component{
  	let value = this.context;
  }
  MyClass.contextType = MyContext;
  ```

> 将挂载在 class 上的 `contextType` 属性重新赋值一个Context对象，然后就可以使用this.context来访问最近的Context上的value值，而且可以在所有生命周期钩子和render()中访问它

* Context.Consumer

  在Context.Provider之下，返回使用value进行处理的react元素

  ```jsx	
  <MyContext.Consumer>
    {value => /* 返回基于 context 值进行渲染的React元素*/}
  </MyContext.Consumer>
  // context默认值为一个对象，携带函数
  <ThemeContext.Consumer>
      {({theme, toggleTheme}) => (
          <button 
              onClick={toggleTheme}
              style={{backgroundColor: theme.background}}>
              Toggle Theme
          </button>
      )}
  </ThemeContext.Consumer>
  ```

> 这个函数接收当前的 context 值，返回一个 React 节点。传递给函数的 `value` 值等同于往上组件树离这个 context 最近的 Provider 提供的 `value` 值。如果没有对应的 Provider，`value` 参数等同于传递给 `createContext()` 的 `defaultValue`。





使用方法：

```jsx
//创建一个React的context对象，参数为默认值
const Context = React.createContext(defaultData);
//在App根组件内使用<ThemeContext.Provider /> 标签包裹顶级作用组件，意思就是这个全局数据使用范围的上限
class App extends React.Component {
  render() {
    // 使用一个 Context.Provider 将当前的要传递的值 value 给以下的组件树。
    // 无论多深，任何组件都能读取这个值。
    // 在这个例子中，我们将 “dark” 作为当前的值传递下去。
    // 若将undefined 传入 value中，则不会使用defaultData作为默认值
    return (
      <Context.Provider value="dark">
        <Toolbar />
      </Context.Provider>
    );
  }
}
//在下级组件中，访问Context来使用这个全局数据
static contextType = Context;
let value = this.context

```

注意在使用Context之前需要思考使用是否合理，否则会造成数据域的混乱

### 不使用Context进行跨层传递数据

在根组件处，将最终需要的下级组件本身通过放入一个变量的形式通过prop传递下去，

这种变化下，只有最顶部的 Page 组件需要知道 `Link` 和 `Avatar` 组件是如何使用 `user` 和 `avatarSize` 的。

```jsx
//Page根组件中，声明一个变量装入下级组件
const userLink = (
    <Link>
      <Avatar user={user} size={props.avatarSize} />
    </Link>
  );
//通过prop传递定义好的组件
return <PageLayout userLink={userLink} />;
```

### 通过向prop传递组件来渲染

这种方式可以有效的解除下级组件与上级组件的耦合

```jsx
function Page(props) {
  const user = props.user;
  //将子组件进行封装成react元素，这些元素可以使用当前组件的props和state
  const content = <Feed user={user} />;
  const topBar = (
    <NavigationBar>
      <Link href={user.permalink}>
        <Avatar user={user} size={props.avatarSize} />
      </Link>
    </NavigationBar>
  );
  return (
    // 通过props传递组件
    <PageLayout
      topBar={topBar}
      content={content}
    />
  );
}
//使用
function PageLayout(props){
    return(
    	{props.topBar}
        {props.content}
    )
}
```

## refs 和 DOM

Refs 提供了一种方式，允许我们访问 DOM 节点或在 render 方法中创建的 React 元素。

由于不能直接得到原生DOM的对象 ，给标签加上 ref prop 并声明一个值可以使用""和{}，前者声明值，后者使用函数，在组件域中使用this.refs进行访问该DOM对象

### 旧版本使用ref：

```jsx
...
const input = this.refs.input

render(){
	return (
    	<input ref="input" />
	    <input ref={js表达式/函数} />
    )
}

```

### 何时使用 Refs

- 管理焦点，文本选择或媒体播放。
- 触发强制动画。
- 集成第三方 DOM 库

避免使用 refs 来做任何可以通过声明式实现来完成的事情。

### 新版本使用refs：

Refs 是使用 `React.createRef()` 创建的，并通过 `ref` 属性附加到 React 元素。

创建refs

```jsx
constructor(prop){
	super(prop)
    this.inputRef = React.createRef();
}
render(){
    return(
    	<input ref={this.inputRef} />
    )
}
```

访问refs

* 当 `ref` 属性用于 HTML 元素时，React.createRef()返回的对象中的current接收底层对应DOM元素的对象

* 当 `ref` 属性用于自定义 class 组件时，ref.current作为React组件实例对象
* 不能在函数组件的props中上使用ref，但是可以在函数组件内部使用ref

```jsx
constructor(prop){
	super(prop);
    this.inputRef = React.createRef();
}
render(){
    console.log(this.inputRef.current); //<input />
    return(
    	<input ref={this.inputRef} />
    )
}
```

React 会在组件挂载时给 `current` 属性传入 DOM 元素，并在组件卸载时传入 `null` 值。`ref` 会在 `componentDidMount` 或 `componentDidUpdate` 生命周期钩子触发前更新。

回调Refs

不使用`React.createRefs()`创建，将元素上的`ref`设置成一个回调函数，回调函数的内容是 `以React实例或者DOM元素`作为参数赋值给`constructor`中的变量，访问时，直接访问这个变量即可访问到对应DOM

```jsx
constructor(prop){
	super(prop)
    this.inputRef = null;
    this.setInputRef = elem =>{
        this.inputRef = elem
    }
}
render(){
    console.log(this.inputRef);  // <input  />
    return(
    	<input ref={this.setInputRef} />
    )
}
```

或者

```jsx
<input ref={elem =>this.inputRef = elem} />
```

## render prop

术语 `render prop`是指一种在 React 组件之间使用一个值为函数的 prop 共享代码的简单技术

具有 render prop 的组件接受一个回调函数，该函数返回一个 React 元素并调用它而不是实现自己的渲染逻辑，换句话说，具有render prop的React组件只渲染render内部回调函数的内容

使用场景

* 当某一封装的组件的功能要被其他几个组件复用且不改变封装的时候

使用方法

* 在作为复用的组件封装中，将render内部使用`{this.props.render(this.state)}`将封装组件中的数据传递给组件本身render prop中回调函数的参数中，使用回调函数内容渲染另一个组件

  ```jsx
  class Cat extends React.Component {
    render() {
      const mouse = this.props.mouse;
      return (
        <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
      );
    }
  }
  
  class Mouse extends React.Component {
    constructor(props) {
      super(props);
      this.handleMouseMove = this.handleMouseMove.bind(this);
      this.state = { x: 0, y: 0 };
    }
  
    handleMouseMove(event) {
      this.setState({
        x: event.clientX,
        y: event.clientY
      });
    }
  
    render() {
      return (
        <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>
          {/*
            使用render方法进行动态传递数据，传递给回调函数的参数
          */}
          {this.props.render(this.state)}
        </div>
      );
    }
  }
  
  class MouseTracker extends React.Component {
    render() {
      return (
        <div>
          <h1>移动鼠标!</h1>  
          <Mouse render={mouse => ( // mouse就是封装组件传递过来的state，而这个mouse组件不会渲染自己的render，而是渲染Cat
            <Cat mouse={mouse} />
          )}/>
        </div>
      );
    }
  }
  ```

### 不一定要使用render这个prop 名字

render prop 是因为模式才被称为 *render* prop ，你不一定要用名为 `render` 的 prop 来使用这种模式。

*任何*被用于告知组件需要渲染什么内容的函数 prop 在技术上都可以被称为 “render prop”，即返回内容为react元素的函数，这种函数不一定要反正元素的attributes 中 ，还可以直接放在复用元素的内部

## 高阶组件设计模式

高阶组件（HOC）是 React 中用于复用组件逻辑的一种高级技巧。HOC 自身不是 React API 的一部分，它是一种基于 React 的组合特性而形成的设计模式。

**高阶组件是参数为组件，返回值为新组件的函数。**

```jsx
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```

组件是将 props 转换为 UI，而高阶组件是将组件转换为另一个组件。

当几个组件的更新数据模式都具有同样的行为时，使用 高阶组件 解决横切关注点问题

高阶函数组件接收2个参数，被包装的组件WrappedComponent，被包装的组件所需要的数据/处理方式selectData

```jsx
// 此函数接收一个组件... ，参数数量不限制
function withSubscription(WrappedComponent, selectData,...) {
  // 返回一个新组件
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
          //切面组件拿到数据
        data: selectData(DataSource, props)
      };
    }
	//操作切面的事件，也是就是生成的组件的公共代码部分
    componentDidMount() {
      // ...负责订阅相关的操作...
      DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }
	//回调函数，监听数据变化，不同组件的数据源相同，但是可能传入的selectData进行更新data的操作不同
    handleChange = () => {
      this.setState({
        data: selectData(DataSource, this.props)
      });
    }

    render() {
      // ... 并使用新数据渲染被包装的组件!
      // 请注意，我们可能还会传递其他属性
      //将公共方法处理的数据交给这个组件的data prop中，并传递其他prop
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}
```

高阶组件的最大用处在于，在几个组件需要请求公共数据的时候，把出现相同部分的操作以及对数据处理的不同的行为进行包装，只需要告知，不同操作的函数，类似工厂一样，生成一个新的组件

HOC 通过将组件*包装*在容器组件中来*组成*新组件。HOC 是纯函数，没有副作用。

被包装组件接收来自容器组件的所有 prop，同时也接收一个新的用于 render 的 `data` prop。HOC 不需要关心数据的使用方式或原因，而被包装组件也不需要关心数据是怎么来的。

### 不要试图在 高阶组件模式 中修改组件原型（或以其他方式改变它）

HOC 不应该修改传入组件，而应该使用组合的方式，通过将组件包装在容器组件中实现功能：

## 深入JSX 

prop的展开：

使用`...`形式展开的prop，可以由父级自定义通过props传入到组件内部标签的attributes中，且不受控制，不安全

```jsx
const Button = props => {
  const { kind, ...other } = props;  const className = kind === "primary" ? "PrimaryButton" : "SecondaryButton";
  return <button className={className} {...other} />;
};

const App = () => {
  return (
    <div>
      <Button kind="primary" onClick={() => console.log("clicked!")}>
        Hello World!
      </Button>
    </div>
  );
};
```



## React生命周期(同步)

![img](https://gitee.com/EricReeves/proxy/raw/master/img/react-component-life-cycle.png)

### 从挂载到卸载

* constructor()

  constructor(props)中完成了对`props`和`state`的初始化，props的初始化是使用`prop-type`进行的，而`state`是在构造器内部初始化

  必须在`constructor`中调用`super(props)`，否则会导致内部this指向错误

* componentWillMount()

  在组件进行挂载之前调用的钩子，一般在服务器中使用，`componentWillMount()`方法在`render()`方法之前被调用

* componentDidMount()

  React 的最佳实践是在生命周期方法`componentDidMount()`中对服务器进行 API 调用或任何其他调用。

  在组件第一次渲染之后调用，此时DOM节点以及生成，通常在此处发送ajax请求，然后使用setState()进行更新数据

* componentWillUnmount

  在组件销毁之前调用，用于清除定时器，以及事件监听

### 更新循环

* props被修改

  * componentWillReceiveProps(nextProps)

    在`props`发生修改时后调用，此时新的`props`为参数`nextProps`，而旧的`props`为`this.props`，一般`props`改变会触发`state`的改变。

* state被修改

  * shouldComponentUpdate(nextProps,nextState)

    得到最新的props和最新的state后，调用此钩子，此函数返回一个布尔值，`true`则进行重绘流程，`false`则不触发重绘，回到`running`状态

* componentWillUpdate(nextProps,nextState)

  进入更新流程，此时`props`，`state`已经迭代，在重新渲染之前调用，任然可以获得新的`nextProps`与`nextState`进行操作

* render()  使用新状态与Diff算法对组件部分进行重绘

* componentDidUpdate(prevProps,prevState)

  重绘之后调用的钩子，可访问到 旧的`prevProps`和`prevstate` 

## 使用 && 获得更简洁的条件

if/else 语句在上一次挑战中是有效的，但是有一种更简洁的方法可以达到同样的结果。假设你正在跟踪组件中的几个条件，并且希望根据这些条件中的每一个来渲染不同的元素。如果你写了很多`else if`语句来返回稍微不同的 UI，你可能会写很多重复代码，这就留下了出错的空间。相反，你可以使用`&&`逻辑运算符以更简洁的方式执行条件逻辑。这是完全可行的，因为你希望检查条件是否为真，如果为真，则返回一些标记。这里有一个例子：

```jsx
{condition && <p>markup</p>}
{display && <h1>Displayed!</h1>} 
```

如果`condition`为 true，则返回标记。如果 condition 为 false，操作将在判断`condition`后立即返回`false`，并且不返回任何内容。你可以将这些语句直接包含在 JSX 中，并通过在每个条件后面写`&&`来将多个条件串在一起。这允许你在`render()`方法中处理更复杂的条件逻辑，而无需重复大量代码。

## 用 renderToString 在服务器上渲染 React

到目前为止，你已经能够在客户端上渲染 React 组件，一般来说我们都是这么做的。然而，在一些用例中，在服务器上渲染一个 React 组件是有意义的。由于 React 是一个 JavaScript 视图库，所以使用 Node 让 JavaScript 运行在服务器上是可行的。事实上，React 提供了一个可用于此目的的`renderToString()`方法。

有两个关键原因可以解释为什么服务器上的渲染可能会在真实世界的应用程序中使用。

* 首先，如果不这样做，你的 React 应用程序将包含一个代码量很少的 HTML 文件和一大堆 JavaScript，当它最初加载到浏览器时。这对于搜索引擎来说可能不太理想，因为它们试图为你的网页内容生成索引，以便人们可以找到你。

* 如果在服务器上渲染初始 HTML 标记并将其发送到客户端，则初始页面加载的内容包含搜索引擎可以抓取的所有页面标记。

* 其次，这创造了更快的初始页面加载体验，因为渲染的 HTML 代码量要比整个应用程序的 JavaScript 代码小。React 仍然能够识别你的应用并在初始加载后进行管理。

* ```jsx
  class App extends React.Component {
    constructor(props) {
      super(props);
    }
    render() {
      return <div/>
    }
  };
  
  // change code below this line
  ReactDOMServer.renderToString(<App />)
  ```



## React Hook

在没有`Hook`之前，函数组件与class(类)组件的区别：

* 类组件有`this` ，而函数组件没有`this`
* 类组件拥有`state` ，而函数组件没有 state
* 类组件有完整的生命周期，在创建时需要将其实例化，而函数组件没有声明周期，在执行完毕后返回结果，不用创建实例一个，所以函数组件的性能比类组件的性能好一些，应尽量使用函数组件
* 在类组件每一次重新渲染时，会执行一遍 `render()` 内的代码，在函数式组件每一次重新渲染时，会重新执行一遍函数组件内的所有代码

Hook 可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性

Hook在最新的 React中完全为可选，而且向后兼容

```jsx
import React, { useState } from 'react';
function Example() {
  // 声明一个新的叫做 “count” 的 state 变量,setCount类似于setState
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

### 什么是Hook ？

Hook是一些可以让你在函数组件中 钩入 React state和生命周期等类组件特性的函数，且Hook不能在类组件中使用,Hook 使用了 JavaScript 的闭包机制

### State Hook

引入`{useState}`后，可以在函数组件中使用`const`声明一组`state`变量 `count` 和 `setCount ` 

`useState()`会返回一个数组，使用这个2个变量进行数组解构赋值，`useState()`内部的参数为这个`state`变量的*初始值*

React 会在重复渲染时保留这个 `state` ,但是它不会把新的 `state` 和旧的 `state` 进行合并。

```jsx
// 函数组件中
function Example(){
    const [count,setCount] = useState(0); //useState的内部参数为count的初始值
    //...
}
```

由此可见，在一个组件中多次使用`State Hook`可以声明多个`state`变量

```jsx
// 函数组件中
function Example(){
    const [name,setName] = useState('React'); 
    const [age,setAge] = userState(5);
    //...
}
```

### Effect Hook

在React组件一般在生命周期钩子中执行：

* 数据获取(发送ajax请求)
* 订阅消息(使用第三方库进行状态更新)
* 手动修改DOM对象(使用refs修改获得的DOM)

这些操作被统一成为副作用操作

useEffect 就是一个 Effect Hook ，为函数组件增加了 **进行副作用操作** 的能力，它跟 class 组件中的 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount` 3个生命周期钩子具有相同的用途，但是被合成到了一个useEffect()中，参数为一个函数，React 保证了每次运行 effect 的同时，DOM 都已经更新完毕。

`useEffect(()=>{..})`的回调函数可访问组件的 props state，在函数组件挂载之后，props state更新之后，函数组件卸载之前，都会调用useEffect()，执行传入的函数

#### 无需清除的Effect

我们只想**在 React 更新 DOM 之后运行一些额外的代码。**比如发送网络请求，手动变更 DOM，记录日志，这些都是常见的无需清除的操作。在class组件中，我们需要在挂载后和更新后的钩子中写相同的代码，而Hook只书写一次

`useEffect` 调度的 effect 不会阻塞浏览器更新屏幕

#### 需要清除的 Effect

在`Effect`中订阅数据只需要一次，这可以防止内存泄露，在class组件中，我们能使用3个生命周期钩子很好的进行订阅、更新和取消订阅

在`Effect`中，在回调函数中进行返回一个**清除函数**，对应class组件中的`componentWillUnmount()` 

**React 会等待浏览器完成画面渲染之后才会延迟调用 `useEffect`**

**React 会在组件卸载的时候执行清除函数**

函数组件每次更新时，都会先卸载，然后再挂载，意味着每次更新都会调用一次setEffect中的内容然后进行state变量更新，从而循环监听，如果不将上次的订阅取消，那么每次的监听都会被保留在内存中，造成内存泄露，所以必须在每次卸载之前，将上一次的订阅取消

```jsx
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    function handleStatusChange(status) {
      //这个函数一直在被调用，如果没有变动则没次set的值都相同
      setIsOnline(status.isOnline);
    }
    //这个订阅会在每次更新时都进行一次订阅，相当于监听
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // 清除上一次的订阅
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

**Hook 允许我们按照代码的用途分离他们，** 而不是像生命周期函数那样。React 将按照 effect 声明的顺序依次调用组件中的*每一个* effect

```jsx
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
  // ...
}
```

#### 那么为什么要使用函数组件在每次更新后运行useEffect的回调呢？

如果在class组件中，在组件被挂载之后的`componentDidMount()`中进行**数据订阅**操作，这个时候组件本身的一个prop作为一个订阅的参数，进行订阅。

* 在不使用 `componentDidUpdate()`的情况下，当这个作为参数的prop被更新时，此时数据订阅的参数没有被更新，导致还是以原来的参数在进行订阅，
* 在使用`componentDidUpdate()`情况下，还要在最开始取消上一次的更新，后再次进行一次相同代码的订阅，导致代码重复2次

```jsx
componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
    this.props.friend.id,   //当组件props更新时，这个参数没有被更新
    this.handleStatusChange
    );
}
 componentDidUpdate(prevProps) {
    // 取消订阅之前的 friend.id
    ChatAPI.unsubscribeFromFriendStatus(
      prevProps.friend.id,
      this.handleStatusChange
    );
    //当组件props更新时，只能通过componentDidUpdate()来重复调用订阅，这样造成了代码重复
    ChatAPI.subscribeToFriendStatus(
    this.props.friend.id,   
    this.handleStatusChange
    );
  }

componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
    this.props.friend.id,
    this.handleStatusChange
    );
}
```

而使用Effect Hook，不同，在挂载的时候，就会调用一次订阅，并且随着数据的更新，他会不断的通过卸载时清除effect和挂载时重新调用订阅，保证数据以规定的节奏处于最新状态，但是这会造成一个性能问题：**即使数据没有改变，也会不断的进行监听以及进行挂载和卸载**

```jsx
function FriendStatus(props) {
// ...
    useEffect(() => {
        // ...
        ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
        return () => {
            ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    	};
});
```

### 对Effect 进行性能优化，解决Effect性能问题

在class组件中，`componentDidUpdate()`可以使用`prevProps`和`prevState`与新的状态进行比较，来决定是否要进行**取消之前的订阅**和**调用新的订阅**

在使用Effect Hook中，只要在`useState()`中传递一个数组作为第二个参数（数组中的元素则为进行差异对比的state变量），则可以在两次渲染都没有差别的时候，跳过对effect的回调函数的调用，目前第二个参数**必须**要手动添加

* 以传入[count]为例：

  如果`count`当前的值是`5`，当`count`接收到一个新值的时候，这个函数组件会启动重新渲染(卸载后挂载)，此时的渲染的`count`会与上一次渲染的`count`进行对比，如果这个2个数组 (前面提到的传入的数组) 中的所有元素都相同，即`[5 === 5]`，则React会跳过执行useEffect()的回调函数，实现了性能优化

* 值得注意的是：

  * 使用Effect的优化，必须确保数组中包含了所有外部作用域中的变量，这些变量必须是在`useEffect()`回调中直接、间接影响内部的state变量的变量，否则你的代码会引用到先前渲染中的旧state变量

* 如果指向执行一次effect，可以向第二个参数传递一个空数组`[]`，告诉React不依赖于任何props和state，永远不需要重复执行

```jsx
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {    document.title = `You clicked ${count} times`;
  },[count]);

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
	//这个订阅会在每次更新时都进行一次订阅，相当于监听
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
        // 清除上一次的订阅
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  },[props.friend.id]);//对比新旧props.friend.id，在props.friend.id没有发生改变的时候，跳过执行回调函数
  // ...
}
```

#### Hook 使用规则









#### Effect Hook总结

effect hook 引入 函数组件 ，使得函数组件拥有了类似于class的生命周期钩子，只不过是将挂载后，更新后，卸载前的钩子进行了一个集中处理，简单的来说，effect hook 处理了这么一个流程：

* `useEffect()`的回调函数在被渲染好后，挂载时，触发了一次回调函数
* 当遇到状态更新时，在使用新、旧两个数据渲染后进行差异对比，决定是否跳过`useEffect()`的回调函数，从而决定是否重新渲染
* 在进行重新渲染时，在当前组件卸载之前，对回调内的监听、订阅等影响后续数据的操作进行清除

# Redux

## Redux：创建一个 Redux Store

Redux 是一个状态管理框架，可以与包括 React 在内的许多不同的 Web 技术一起使用。

在 Redux 中，有一个状态对象负责应用程序的整个状态，这意味着如果你有一个包含十个组件且每个组件都有自己的本地状态的 React 项目，那么这个项目的整个状态将通过 Redux`store`被定义为单个状态对象，这是学习 Redux 时要理解的第一个重要原则：Redux store 是应用程序状态的唯一真实来源。

这也意味着，如果你的应用程序想要更新状态，只能通过 Redux store 执行，单向数据流可以更轻松地对应用程序中的状态进行监测管理。

Redux `store`是一个保存和管理应用程序状态的`state`，你可以使用 Redux 对象中的`createStore()`来创建一个 redux`store`，此方法将`reducer`函数作为必需参数，`reducer`函数将在后面的挑战中介绍。该函数已在代码编辑器中为你定义，它只需将`state`作为参数并返回一个`state`即可。

## Redux：从 Redux Store 获取状态

```jsx
const store = Redux.createStore(
  (state = 5) => state
);
let currentState = store.getState()
```

## 定义一个 Redux Action

由于 Redux 是一个状态管理框架，因此更新状态是其核心任务之一。在 Redux 中，所有状态更新都由 dispatch action 触发，action 只是一个 JavaScript 对象，其中包含有关已发生的 action 事件的信息。Redux store 接收这些 action 对象，然后更新相应的状态。有时，Redux action 也会携带一些数据。例如，在用户登录后携带用户名，虽然数据是可选的，但 action 必须带有`type`属性，该属性表示此 action 的类型。

我们可以将 Redux action 视为信使，将有关应用程序中发生的事件信息提供给 Redux store，然后 store 根据发生的 action 进行状态的更新。

```jsx
const action = {
  type:'LOGIN'
}
```

## 定义一个 Action Creator

创建 action 后要将 action 发送到 Redux store，以便它可以更新其状态。在 Redux 中，你可以定义动作创建器来完成此任务，action creator 只是一个返回动作的 JavaScript 函数，换句话说，action creator 创建表示动作事件的对象。

```jsx
const action = {
  type: 'LOGIN'
}

function actionCreator ()  {
    return action
}
```

## 分发 Action Event

`dispatch`方法用于将 action 分派给 Redux store，调用`store.dispatch()`将从 action creator 返回的值发送回 store。

action creator 返回一个具有 type 属性的对象，该属性指定已发生的 action，然后，该方法将 action 对象 dispatch 到 Redux store

```jsx
const store = Redux.createStore(
  (state = {login: false}) => state
);

const loginAction = () => {
  return {
    type: 'LOGIN'
  }
};
store.dispatch(loginAction())
```

## Redux：在 Store 里处理 Action

在一个 action 被创建并 dispatch 之后，Redux store 需要知道如何响应该操作。这就是`reducer`函数存在的意义。

* Redux 中的 Reducers 负责响应 action 然后进行状态的修改。`reducer`将`state`和`action`作为参数，并且它总是返回一个新的`state`。
* 我们要知道这是 reducer 的**唯一**的作用。它不应有任何其他的作用：比如它不应调用 API 接口，也不应存在任何潜在的副作用。
* reducer 只是一个接受状态和动作，然后返回新状态的纯函数。
* Redux 的另一个关键原则是`state`是只读的。换句话说，`reducer`函数必须**始终**返回一个新的`state`，并且永远不会直接修改状态。Redux 不强制改变状态，但是你需要在你的 reducer 函数的代码中强制执行它，

```jsx
const defaultState = {
  login: false
};

const reducer = (state = defaultState, action) => {
  if (action.type === "LOGIN") {
    return {
      login:true
    }
  } else {
    return state
  }
  
};

const store = Redux.createStore(reducer);
const loginAction = () => {
  return {
    type: 'LOGIN'
  }
};
```

## Redux：使用 Switch 语句处理多个 Actions

你可以定义 Redux store 如何处理多种 action 类型。比如你正在 Redux store 中进行用户身份验证，如果你希望用户在登录和注销时具有状态的响应，你可以使用具有`authenticated`属性的单个的 state 对象。你还需要使用 action creators 创建与用户登录和用户注销相对应的 action，以及 action 对象本身。

代码编辑器为你创建了 store、actions、action creators。通过编写`reducer`函数来处理多个身份验证操作。可以在`reducer`通过使用 JavaScript 的`switch`来响应不同的 action 事件。这是编写 Redux reducer 时的标准模式，switch 语句选择`action.type`中的一个值并返回相应的身份验证状态。

**注意：** 此时，不要担心 state 的不变性，因为在这个示例中它很小而且很简单。所以对于每个操作你都可以返回一个新对象，比如`{authenticated:true}`。另外，不要忘记在 switch 语句中写一个`default`case，返回当前的`state`。这是很重要的，因为一旦你的程序有多个 reducer，当一个 action 被 dispatch 时它们都会运行，即使 action 与该 reducer 无关。在这种情况下，你要确保返回当前的`state`

```jsx
const defaultState = {
  authenticated: false
};
const authReducer = (state = defaultState, action) => {
  switch (action.type){
    case 'LOGIN':
      return {
        authenticated: true
      }
    case 'LOGOUT':
      return {
        authenticated: false
      }
    default:
      return {
        authenticated: state.authenticated
      }
  }
};
const store = Redux.createStore(authReducer);
const loginUser = () => {
  return {
    type: 'LOGIN'
  }
};
const logoutUser = () => {
  return {
    type: 'LOGOUT'
  }
};
```

## 使用 const 声明 Action Types

在使用 Redux 时的一个常见做法是将操作类型指定为只读，然后在任何使用它们的地方引用这些常量。你可以通过将 action types 使用`const`声明重构你正在使用的代码。

```js
const LOGIN = 'LOGIN'
const LOGOUT = 'LOGOUT'
const defaultState = {
  authenticated: false
};
const authReducer = (state = defaultState, action) => {
  switch (action.type) {
    case LOGIN:
      return {
        authenticated: true
      }
    case LOGOUT:
      return {
        authenticated: false
      }
    default:
      return state;
  }
};
const store = Redux.createStore(authReducer);
const loginUser = () => {
  return {
    type: LOGIN
  }
};
const logoutUser = () => {
  return {
    type: LOGOUT
  }
};

```

## Redux：注册 Store 监听器

在 Redux `store`对象上访问数据的另一种方法是`store.subscribe()`。这允许你将监听器函数订阅到 store，只要一个 action 被 dispatch 就会调用它们。这个方法的一个简单用途是为你的 store 订阅一个函数，它只是在每次收到一个 action 并且更新 store 时记录一条消息。

```jsx
const ADD = 'ADD';
const reducer = (state = 0, action) => {
  switch(action.type) {
    case ADD:
      return state + 1;
    default:
      return state;
  }
};
const store = Redux.createStore({reducer});
let count = 0;
store.subscribe(()=>{   // dispatch被调用，就会触发一次subscribe内的回调函数
  count ++
})
store.dispatch({type: ADD});
console.log(count);
store.dispatch({type: ADD});
console.log(count);
store.dispatch({type: ADD});
console.log(count);
```

## Redux：组合多个 Reduces

应用程序的状态开始变得越来越复杂时，将状态划分为多个部分可能是个更好的选择。Redux 的第一个原则：所有应用程序状态都保存在 store 中的一个简单的 state 对象中。因此，Redux 提供 reducer 组合作为复杂状态模型的解决方案。定义多个 reducer 来处理应用程序状态的不同部分，然后将这些 reducer 组合成一个 root reducer。然后将 root reducer 传递给 Redux `createStore()`方法。

Redux 提供了`combineReducers()`方法。该方法接受一个对象作为参数，在该参数中定义一个将键与特定 reducer 函数关联的属性。Redux 将使用你给的键值作为关联状态的名称。

通常情况下，当它们在某种程度上是独一无二的，最好为每个 state 创建一个`reducer`例如，在一个带有用户身份验证的reactApp，一个 reducer 可以处理身份验证而另一个处理用户提交的文本和注释。对于这样的应用程序，我们可能会编写`combineReducers()`方法，如下所示：

```jsx
const rootReducer = Redux.combineReducers({
 auth: authenticationReducer,
 notes: notesReducer
});
```

示例：

```jsx
const INCREMENT = 'INCREMENT';
const DECREMENT = 'DECREMENT';
const counterReducer = (state = 0, action) => {
  switch(action.type) {
    case INCREMENT:
      return state + 1;
    case DECREMENT:
      return state - 1;
    default:
      return state;
  }
};
const LOGIN = 'LOGIN';
const LOGOUT = 'LOGOUT';
const authReducer = (state = {authenticated: false}, action) => {
  switch(action.type) {
    case LOGIN:
      return {
        authenticated: true
      }
    case LOGOUT:
      return {
        authenticated: false
      }
    default:
      return state;
  }
};
const rootReducer = Redux.combineReducers({
  count: counterReducer,
  auth: authReducer
})
const store = Redux.createStore(rootReducer);

```

## 发送 Action Data 给 Store

到目前为止，你已经学会了如何将 action dispatch 给 Redux store，但到目前为止，这些 action 并未包含除 `type`之外的任何信息。你还可以发送特定数据和 action 一起。事实上，这是非常常见的，因为 action 通常源于一些用户交互，并且往往会携带一些数据，Redux store 经常需要知道这些数据。

```jsx
const ADD_NOTE = 'ADD_NOTE';

const notesReducer = (state = 'Initial State', action) => {
  switch(action.type) {
    case ADD_NOTE:
      return action.text
    default:
      return state;
  }
};
const addNoteText = (note) => {
  return {
    type: ADD_NOTE,
    text: note
  }
};
const store = Redux.createStore(notesReducer);
console.log(store.getState());
store.dispatch(addNoteText('Hello!'));
console.log(store.getState());
```

## Redux：使用中间件处理异步操作

Redux Thunk 中间件，专门处理异步操作，例如发送ajax请求，这将会编写一个异步action，其返回一个参数为dispatch的函数，在异步action中调用同步action，进行状态更新，目前推荐使用ES8新增的 async函数配合await等待Promise 来作为异步action返回的函数

如果要使用 Redux Thunk 中间件，请将其作为参数传递给`Redux.applyMiddleware()`。然后将此函数作为第二个可选参数提供给`createStore()`函数，看一下编辑器底部的代码，然后，要创建一个异步的 action，你需要在 action creator 中返回一个以`dispatch`为参数的函数。在这个函数中，你可以 dispatch action 并执行异步请求。

```jsx
const REQUESTING_DATA = 'REQUESTING_DATA'
const RECEIVED_DATA = 'RECEIVED_DATA'

const requestingData = () => { return {type: REQUESTING_DATA} }
const receivedData = (data) => { return {type: RECEIVED_DATA, users: data.users} }
const handleAsync = () => {	// 异步操作
  return function(dispatch) {
    // 在这里 dispatch 请求的 action
    dispatch(requestingData())
    setTimeout(function() {
      let data = {
        users: ['Jeff', 'William', 'Alice']
      }
      // 在这里 dispatch 接收到的数据 action
    dispatch(receivedData(data))  
    }, 2500);
  }
};

const defaultState = {
  fetching: false,
  users: []
};
const asyncDataReducer = (state = defaultState, action) => {
  switch(action.type) {
    case REQUESTING_DATA:
      return {
        fetching: true,
        users: []
      }
    case RECEIVED_DATA:
      return {
        fetching: false,
        users: action.users
      }
    default:
      return state;
  }
};
const store = Redux.createStore(
  asyncDataReducer,
  Redux.applyMiddleware(ReduxThunk.default)
);
```

## Redux：用 Redux 写一个计数器

```jsx
const INCREMENT = 'INCREMENT'; // 为增量 action 类型定义一个常量
const DECREMENT = 'DECREMENT'; // 为减量 action 类型定义一个常量

const counterReducer = (state = 0, action) => {
  switch(action.type){
    case INCREMENT:
      return state + 1
    case DECREMENT:
      return state - 1
    default:
      return state
  } 
}; // 定义计数器，它将根据收到的action增加或减少状态

const incAction = () => {
  return {
    type:INCREMENT
  }
}; // 定义一个用于递增的 action creator

const decAction = () => {
  return {
    type:DECREMENT
  }
} // 定义一个用于递减的 action creator

const store = Redux.createStore(counterReducer); // 在这里定义一个 Redux store，传递你的 reducer
```

## Redux：永不改变状态

Redux不可变状态意味着你永远不会直接修改状态，而是返回一个新的状态的副本，其底层原理是React会使用Diff算法比较新旧状态之间的差异，因此更新不能丢失旧的状态

利用数组或者对象的特征，直接返回基于上一次的state的一个副本，而不是重新将state赋予一个新的地址

```jsx
const ADD_TO_DO = 'ADD_TO_DO';

const todos = [
  'Go to the store',
  'Clean the house',
  'Cook dinner',
  'Learn to code',
];

const immutableReducer = (state = todos, action) => {
  switch(action.type) {
    case ADD_TO_DO:
      return state.concat(action.todo) // 返回一个测试副本
    default:
      return state;
  }
};

const addToDo = (todo) => {
  return {
    type: ADD_TO_DO,
    todo
  }
}
const store = Redux.createStore(immutableReducer);

```

## Redux：从数组中删除项目

是时候练习从数组中删除项目了。扩展运算符也可以在这里使用。其他有用的JavaScript方法包括`slice()`和`concat()`。

```jsx
const immutableReducer = (state = [0,1,2,3,4,5], action) => {
  switch(action.type) {
    case 'REMOVE_ITEM':
      // 此处不能使用splice，因为会改变原数组
      return [...state].slice(0,action.index).concat([...state].slice(action.index +1))
    default:
      return state;
  }
};
const removeItem = (index) => {
  return {
    type: 'REMOVE_ITEM',
    index
  }
}

const store = Redux.createStore(immutableReducer);
```

## Redux：使用 Object.assign 合并对象

最后几个挑战适用于数组，但是当状态是`object`时，有一些方法可以帮助强制执行状态不变性。处理对象的一个方法是`Object.assign()`。`Object.assign()`获取目标对象和源对象，并将源对象中的属性映射到目标对象。任何匹配的属性都会被源对象中的属性覆盖。通常用于通过传递一个空对象作为第一个参数，然后是要用复制的对象来制作对象的浅表副本。这是一个例子：

```jsx
const newObject = Object.assign({}, obj1, obj2);
```

这会创建`newObject`作为新的`object`，其中包含`obj1`和`obj2`中当前存在的属性。

```jsx
const defaultState = {
  user: 'CamperBot',
  status: 'offline',
  friends: '732,982',
  community: 'freeCodeCamp'
};
const immutableReducer = (state = defaultState, action) => {
  switch(action.type) {
    case 'ONLINE':
      // 使用Object.assign 从最后一个参数到第二个参数来合并一个对象，并生成一个新的浅拷贝对象
      return Object.assign({},state,{status: 'online'})
    default:
      return state;
  }
};
const wakeUp = () => {
  return {
    type: 'ONLINE'
  }
};

const store = Redux.createStore(immutableReducer);
```

## React 和 Redux：首先在本地管理状态

```jsx
class DisplayMessages extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      input: '',
      messages: []
    }
    this.handleChange = this.handleChange.bind(this)
    this.submitMessage = this.submitMessage.bind(this)
  }
  handleChange(event){
      this.setState({
        input:event.target.value
      })
  }
  submitMessage(){
      this.setState({
        input:'', 
        messages: this.state.messages.concat(this.state.input)
      })
  }
  render() {
    const {input, messages} = this.state
    const msg = messages.map((item,idx) => ( // 注意添加key
      <li key={idx}>{item}</li>
    ))
    return (
      <div>
        <h2>键入新 Message</h2>
        <input onChange={this.handleChange} value={input} />
        <button onClick={this.submitMessage}>Add message</button>
        <ul>
          {msg}
        </ul>

      </div>
    );
  }
};
```

## React 和 Redux：提取状态逻辑给 Redux

完成 React 组件后，我们需要把在本地`状态`执行的逻辑移到 Redux 中，这是为小规模 React 应用添加 Redux 的第一步。该应用的唯一功能是把用户的新消息添加到无序列表中。

```jsx
const ADD = 'ADD'
const messages = []
const messageReducer = (state = messages,action) => {
  switch (action.type){
    case ADD :
      return [
        ...state,
        action.message
      ];
    default :
      return state
  }
}

const addMessage = (message) => {
  return{
    type:ADD,
    message
  }
}

const store = Redux.createStore(messageReducer)

```

## React 和 Redux：使用 Provider 连接 Redux 和 React

在上一挑战中，你创建了 Redux store 和 action，分别用于处理消息数组和添加新消息。下一步要为 React 提供访问 Redux store 及发起更新所需的 actions。`react-redux`包可帮助我们完成这些任务。

React Redux 提供的 API 有两个关键的功能：`Provider`和`connect`，`Provider`是 React Redux 包装 React 应用的 wrapper 组件，它允许你访问整个组件树中的 Redux`store`及`dispatch（分发）`方法。`Provider`需要两个 props：Redux store 和 APP 应用的子组件

```jsx
<Provider store={store}>
 <App/>
</Provider>
```

```jsx
// Redux 代码：
const ADD = 'ADD';

const addMessage = (message) => {
  return {
    type: ADD,
    message
  }
};

const messageReducer = (state = [], action) => {
  switch (action.type) {
    case ADD:
      return [
        ...state,
        action.message
      ];
    default:
      return state;
  }
};


const store = Redux.createStore(messageReducer);
// React 代码：

class DisplayMessages extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      input: '',
      messages: []
    }
    this.handleChange = this.handleChange.bind(this);
    this.submitMessage = this.submitMessage.bind(this);
  }
  handleChange(event) {
    this.setState({
      input: event.target.value
    });
  }
  submitMessage() {
    const currentMessage = this.state.input;
    this.setState({
      input: '',
      messages: this.state.messages.concat(currentMessage)
    });
  }
  render() {
    return (
      <div>
        <h2>Type in a new Message:</h2>
        <input
          value={this.state.input}
          onChange={this.handleChange}/><br/>
        <button onClick={this.submitMessage}>Submit</button>
        <ul>
          {this.state.messages.map( (message, idx) => {
              return (
                 <li key={idx}>{message}</li>
              )
            })
          }
        </ul>
      </div>
    );
  }
};
const Provider = ReactRedux.Provider;

class AppWrapper extends React.Component {
  // 在此渲染 Provider
    constructor(props){
        super(props)
    }
    render(){
        return (
            <Provider store={store}>
                <DisplayMessages />
            </Provider>
        )
    }
  // 请在本行以上添加你的代码
};
```

## React 和 Redux：映射 State 到 Props

`Provider`可向 React 组件提供`state`和`dispatch`，但你必须确切地指定所需要的 state 和 actions，以确保每个组件只能访问所需的 state。

```jsx
const state = [];

// 编写getter函数，将state以对象的值的形式返回给Provider组件
const mapStateToProps = (state) => ()
  return {
    messages:state
  }
}
```

## React 和 Redux：映射 Dispatch 到 Props

`mapDispatchToProps()`函数可为 React 组件提供特定的创建 action 的函数，以便组件可 dispatch actions，从而更改 Redux store 中的数据。它返回一个对象，把 dispatch actions 映射到属性名上，该属性名成为`props`。然而，每个属性都返回一个用 action creator 及与 action 相关的所有数据调用`dispatch`的函数，而不是返回`state`的一部分。你可以访问`dispatch`，因为在定义函数时，我们以参数形式把它传入`mapDispatchToProps()`了，这跟`state`传入`mapDispatchToProps()`是一样的。在幕后，React Redux 用 Redux 的`store.dispatch()`来管理这些含`mapDispatchToProps()`的dispatches，这跟它使用`store.subscribe()`来订阅映射到`state`的组件的方式类似。

```jsx
const addMessage = (message) => {
  return {
    type: 'ADD',
    message: message
  }
};

// 将return中的函数映射到组件的props中，在组件的props中就可以访问这些函数，来调用相应的dispatch
const mapDispatchToProps = (dispatch) => {
    return {
        submitNewMessage: (message) => {
            dispatch(addMessage(message))
        }
    }
}
```

## React 和 Redux：连接 Redux 和 React

既然写了`mapStateToProps()`、`mapDispatchToProps()`两个函数，现在你可以用它们来把`state`和`dispatch`映射到 React 组件的`props`了。React Redux 的`connect`方法可以完成这个任务。此方法有`mapStateToProps()`、`mapDispatchToProps()`两个可选参数，它们是可选的，原因是你的组件可能仅需要访问`状态`但不需要分发任何 actions，反之亦然。

为了使用此方法，需要传入函数参数并在调用时传入组件。这种语法有些不寻常，如下所示：

```jsx
// connect返回一个新常量，这个常量就是已经连接Redux的Component组件副本
const ConnectedComponent = connect(mapStateToProps, mapDispatchToProps)(Component)
// 或者在组件中直接暴露一个连接后的组件
export default connect(
  state => ({
    user: state.user,
    chat: state.chat
  }),
  { sendMessage, hasReadMessage }
)(Chat)
```

## React 和 Redux：将局部状态提取到 Redux 中

```jsx
// Redux:
const ADD = 'ADD';

const addMessage = (message) => {
  return {
    type: ADD,
    message: message
  }
};

const messageReducer = (state = [], action) => {
  switch (action.type) {
    case ADD:
      return [
        ...state,
        action.message
      ];
    default:
      return state;
  }
};

const store = Redux.createStore(messageReducer);

// React:
const Provider = ReactRedux.Provider;
const connect = ReactRedux.connect;

// 请在本行以下添加你的代码
class Presentational extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      input: '',
    }
    this.handleChange = this.handleChange.bind(this);
    this.submitMessage = this.submitMessage.bind(this);
  }
  handleChange(event) {
    this.setState({
      input: event.target.value
    });
  }
  submitMessage() {
    this.props.submitNewMessage(this.state.input)
    this.setState({
      input: '',
    });
  }
  render() {
    const messages = this.props.messages.map( (item, idx) =>(<li key={idx}>{item}</li>)}
    return (
      <div>
        <h2>Type in a new Message:</h2>
        <input
          value={this.state.input}
          onChange={this.handleChange}/><br/>
        <button onClick={this.submitMessage}>Submit</button>
        <ul>
          {messages}
          }
        </ul>
      </div>
    );
  }
};

const mapStateToProps = (state) => {
  return {messages: state}
};

const mapDispatchToProps = (dispatch) => {
  return {
    submitNewMessage: (message) => {
      dispatch(addMessage(message))
    }
  }
};

const Container = connect(mapStateToProps, mapDispatchToProps)(Presentational);

class AppWrapper extends React.Component {
  render() {
    return (
      <Provider store={store}>
        <Container/>
      </Provider>
    );
  }
};
```

# 实现一个Redux

更新中.....
