---
title: react基础语法
data: 2022-06-06 16:00
tags:
  - react
categories: 框架
---

react 的 jsx 语法规则，三大核心属性，babel 严格模式，class 类的 this 指向问题，函数式组件，类式组件，事件绑定，state 值的取改

<!-- more -->

### **react 库引入有顺序，先引入 react.js,再引入 react-dom.js;babel 是 jsx 转 js 的**

script 标签引入，写 jsx 代码得 script 的属性是"text/babel"

shite+浏览器刷新图标（强制刷新）

### **原生 js 写法。调用 react 的 api 创建虚拟 dom**

```js

react.createElement('标签名'，'id，类名'，'内容')

const Vdom = react.createElement('h1',{ id: 'test' }, '<span><span>')

这里视图显示<span><span>

创建虚拟dom，内容里面只能是标签的文本，无法识别标签

```

### **babel 写法。**(严格模式下 this 会指向 undefined，不会指向 window)

```js

const Vdom = <h1 id='test'><span><span></h1>

ReactDom.render(Vdom, document.getElementById('test'))

```

### **dom 对比**

虚拟 dom 比较轻，真实 dom 重，原生的 dom 可以 console，打断点获取发现会有很多属性和 api；而虚拟 dom 是 react 用的，没有那么多繁琐的 api 和属性

### **jsx 语法规则:**

1.定义虚拟 DOMI 时，不要写引号 const Vdom = 111。

2.标签中混入 JS 表达式时要用{}，不包括 js 代码哦

- js 语句（代码）

  下面这些都是语句（代码）

  ```js

  ​	if（）{ }

  ​	for（）{ }

  ​	switch（）{ case： xxxxxx }

  ```

- js 表达式，会产生一个值，可以放在任何一个需要值得地方

  下面都是表达式

  ```js

  ​	console.log(a)

  ​	a

  ​	a.map()

  ​	a+b

  ​	a()  函数也有返回值

  ​	function a（）{ }

  ```

  3.样式的类名指定不要用 c1ass，要用 className.

  4.内联样式，要用 style={ {fikey: value} }的形式去写。

  5.只有一个根标签

  6.标签必须闭合

  7.标签首字母

    - 小写就是转为 html 同名标签，无对应报错
    - 大写就是自定义组件标签，没有定义组件报错，not defined

  8.注释

{ }可以写 js 代码，js 代码的注释就可以用了

{//} 或者 {/\* \*/}

### **函数式组件**

```js
function Demo() {
  console.log(this) //undefine

  return <h1>我我我我</h1>
}
```

此处的 this 是因为 babel 编译后开启了严格模式

ReactDOM.render(<Demo/>,document.getElementById('test'))

执行过程

- react 信息组件标签，找到对应组件
- 发现函数式组件，调用函数，将返回的虚拟的 dom 转为真实 dom，页面展示

### **类式组件**

```js
class MyComonent extends React.Component() {
  render() {
    // this指向 MyComonent实例对象，继承了React.Component的构造器

    return <h2>我是组件</h2>
  }
}

/*

    1.react解析组件标签，找到MyComonent组件

    2.发现组件是使用类定义，随后new出来该类的实例（会自动帮你new 该类的实例）

    3.虚拟dom转为真是dom，呈现页面

*/

ReactDOM.render(<MyComonent />, document.getElementById('test'))
```

### **组件实例的三大核心属性（记得是实例，所以 class 类式组件才有核心属性，function 连 this 都没有）**

**state 组件数据，props 传值，refs 获取组件实例**

#### state 组件数据

```js
class MyComonent extends React.Component() {
  constructor(props) {
    super(props)

    // 原先state是null，通过构造器改变state

    this.state = { isHot: '好热' }
  }

  render() {
    console.log(this) //发现state的值不再是null了，而是对象，里面有isHot属性

    // onclick必须大写onClick,这里是demo事件赋值给了onClick点击事件,假如写demo（）会undefined，因为你把demo的返回值赋值给了onClick了

    return (
      <h2 id="demo" onClick={demo}>
        今天天气{this.state.isHot}
      </h2>
    )
  }
}

/*
	1.react解析组件标签，找到MyComonent组件
	
	2.发现组件是使用类定义，随后new出来该类的实例（会自动帮你new 该类的实例）
	
	3.虚拟dom转为真是dom，呈现页面
*/

ReactDOM.render(<MyComonent />, document.getElementById('test'))
```

原生的事件绑定都可以用，获取节点 （尽量少用）

```js
const demo = document.getElementById('demo')

demo.onclick = ( ) => {  }

demo.addEventListener( ' onclick ' , ( ) = > { })

function demo (){

	console.log('点击了')

}
```

**自定义的函数拿不到类式组件实例对象里面的 state 值，this 指向 undefined 了**

1.**可以直接 window 定义 that，在类式组件里面把 this 挂载到 that 上**

2.**完整的在类式组件里面定义事件（类中方法的 this 指向，babel 的严格模式两点卡住了）**

**（注：这里的 onClick={this.demo}只是顺着原型链找到了 demo 函数并赋值，调用时是在堆里面找到这个函数调用，不是通过实例对象调用）**

**（神来之笔，直接通过 bind 改变 demo 事件的 this 指向即可）**

```js
class MyComonent extends React.Component() {
  // 构造器执行——————1次，在react解析时new了一次（构造器可写可不写）

  constructor(props) {
    // 构造器是否接收props，是否传递给super，取决于：是否希望在构造器中通过this访问props

    super(props)

    // 原先state是null，通过构造器改变state,必须是对象，setState修改时传的就是一个对象

    this.state = { isHot: '好热' }

    //（神来之笔，直接通过bind改变demo事件的this指向即可）把原型上的demo挂载到自己的实例上，实例上有了就不会找原型链下面的demo，本质是同一个

    this.demo = this.demo.bind(this)
  }

  // render执行————————1+n次，首次渲染初始化，n是状态更新的次数

  render() {
    console.log(this) //发现state的值不再是null了，而是对象，里面有isHot属性

    // onClick必须是this.demo,指向当前实例

    return (
      <h2 id="demo" onClick={this.demo}>
        今天天气{this.state.isHot}
      </h2>
    )
  }

  // 通过MyComonent实例调用demo时，demo的this就是MyComonent实例

  // demo执行————————点几次调几次

  demo() {
    console.log(this.state.isHot)
  }
}

/*
	1.react解析组件标签，找到MyComonent组件
	
	2.发现组件是使用类定义，随后new出来该类的实例（会自动帮你new 该类的实例）
	
	3.虚拟dom转为真是dom，呈现页面
*/

ReactDOM.render(<MyComonent />, document.getElementById('test'))
```

react 的状态值不能直接更改，react 不认可这种写法，视图并不会更新（**setState 是一个合并的动作**，多个属性只会改变更新之后的）

```js
this.state.isHot = '寒冷'

this.setState({
  isHot: '寒冷',
})

//必须通过setState方法来改变状态
```

简写模式

```js
class MyComonent extends React.Component() {
  state = { isHot: '好热' }

  render() {
    console.log(this) //发现state的值不再是null了，而是对象，里面有isHot属性

    // onClick必须是this.demo,指向当前实例

    return (
      <h2 id="demo" onClick={this.demo}>
        今天天气{this.state.isHot}
      </h2>
    )
  }

  // 直接赋值demo为一个箭头函数，利用箭头函数没有this可以找到MyComonent实例对象的this。

  demo = () => {
    this.setState({
      isHot: '寒冷',
    })
  }
}

ReactDOM.render(<MyComonent />, document.getElementById('test'))
```

#### props 传值

```js
class MyComonent extends React.Component() {
  render() {
    console.log(this.props) //拿到props：{ name: '爸爸'}
    return <h2>我是你{this.props.name}</h2>
  }
}
/*
1.react解析组件标签，找到MyComonent组件
2.发现组件是使用类定义，随后new出来该类的实例（会自动帮你new 该类的实例）
3.虚拟dom转为真是dom，呈现页面
*/
const p = {
  name: '11',
  sex: '',
  age: 13,
}
ReactDOM.render(<MyComonent name="爸爸" />, document.getElementById('test'))

// p的key值全部传入
// {...p}触发新语法，单纯...无法展开对象
// react的{}只是表达式的语法，为什么可以...p是因为react和babel可以展开对象，仅仅供实例对象使用
ReactDOM.render(<MyComonent {...p} />, document.getElementById('test'))

// 传递number
ReactDOM.render(<MyComonent name="爸爸" age={18} click={click} />, document.getElementById('test'))
// 传参很繁琐，不知道传递的变量类型，必须在类式组件直接限制传递参数的类型，不符合报错

// 16版本已经没有React.PropTypes，15之前还是有的,必须手动引入一个库
MyComonent.propTypes = {
  // 必传的字符串（isRequired）
  name: PropTypes.string.isRequired,
  // 字符串
  sex: PropTypes.string,
  // 数字
  age: PropTypes.number,
  // 函数
  click: PropTypes.func,
}
// 没有传值时，默认值的展示
MyComonent.defautProps = {
  sex: '不男不女',
}
// 简写模式
class MyComonent extends React.Component() {
  render() {
    console.log(this.props) //拿到props：{ name: '爸爸'}
    return <h2>我是你{this.props.name}</h2>
  }
  // 这里直接加给类实例对象，我需要的时加给这个类自身
  propTypes = {
    // 必传的字符串（isRequired）
    name: PropTypes.string.isRequired,
    // 字符串
    sex: PropTypes.string,
    // 数字
    age: PropTypes.number,
    // 函数
    click: PropTypes.func,
  }
  // 这里直接加给类实例对象，我需要的时加给这个类自身
  static propTypes = {
    // 必传的字符串（isRequired）
    name: PropTypes.string.isRequired,
    // 字符串
    sex: PropTypes.string,
    // 数字
    age: PropTypes.number,
    // 函数
    click: PropTypes.func,
  }
  static defautProps = {
    sex: '不男不女',
  }
}
function click() {
  console.log(this)
}
ReactDOM.render(<MyComonent name="爸爸" age={18} click={click} />, document.getElementById('test'))
```

函数式组件可以使用 props 和传参校验

```js
// 函数式组件只能接收props，因为函数有传参的规则，所以能接收到
function demo(props) {
  const { name, age, click } = props
  return (
    <ul>
      <li>{name}</li>
      <li>{age}</li>
      <li>{click}</li>
    </ul>
  )
}
// 函数式组件可以传参限制
demo.propTypes = {
  // 必传的字符串（isRequired）
  name: PropTypes.string.isRequired,
  // 字符串
  sex: PropTypes.string,
  // 数字
  age: PropTypes.number,
  // 函数
  click: PropTypes.func,
}
// 没有传值时，默认值的展示
demo.defautProps = {
  sex: '不男不女',
}
ReactDOM.render(<MyComonent name="爸爸" age={18} click={click} />, document.getElementById('test'))
```


#### ref获取组件实例

ref字符串形式

```js
// 组件ref，获取节点refs
class MyComonent extends React.Component(){
    demo = () => {
        const { h2 } = this.refs
        console.log(h2.value)
        // 这里获取到标签的value值，是真实dom的value值，获取这个节点有很多属性
    }
    render(){
        return <h2 ref="h2" onClick={this.demo}>我是你{ this.props.name }</h2>
        // ref给标签一个标识
    }
}
ReactDOM.render(<MyComonent name="爸爸"/>, document.getElementById('test'))
```

ref回调形式：内联

```js
class MyComonent extends React.Component(){
    demo = () =>{
        const { input1 } = this
        console.log(input1.value) //打印我是你
    }
    render(){
        // 这个形参就是当前节点<h2></h2>,this.input1就是在MyComonent实例对象赋值，this指向render，render的this指向实例对象
        return <h2 ref={ (currentNode) => { this.input1 = currentNode }} onClick={this.demo}>我是你</h2>
    }
}
ReactDOM.render(<MyComonent/>, document.getElementById('test'))

```

ref回调形式：类名绑定

```js
class MyComonent extends React.Component(){
    demo = (c) =>{
        this.input = c
        console.log(this.input.value) //打印我是你爸爸
    }
    render(){
        // 这个形参就是当前节点<h2></h2>,this.input1就是在MyComonent实例对象赋值，this指向render，render的this指向实例对象
        return <h2 ref={ this.demo } onClick={this.demo}>我是你{ this.props.name }</h2>
    }
}
ReactDOM.render(<MyComonent name="版本"/>, document.getElementById('test'))
// 内联和类名绑定没什么区别
// 研究调用次数的问题：demo()打印多少次
// 更新（视图改变）过程会执行两次，第一次打印null清空旧的ref，第二次打印当前节点

```

react.createRef()

```js
class demo extends React.Component{
    // 生成一个容器，可以存储ref所标识的节点,只能存一个
    demoRef = react.createRef()
    demoRef2 =react.createRef()
    click = ()=>{
        console.log(this.demoRef.current.value)
    }
    click2 = ()=>{
        console.log(this.demoRef2.current.value)
    }
    render(){
        return (
            <div>
                <h2 onClick={click} ref={ this.demoRef } >数据</h2>
                <h2 onClick={click} ref={ this.demoRef2 } >数据</h2>
            </div>
        )
    }
}
ReactDOM.render(<Demo/>, document.getElementById('test'))
```

<!-- more -->
