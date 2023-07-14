---
title: react生命周期
data: 2022-06-13 16:00
tags:
  - react
categories: 框架
---

包括 react 旧生命周期和新生命周期，初始化、视图更新分别对应的生命周期流程

<!-- more -->

## 旧的生命周期（无代码顺序，由 react 决定执行顺序）

**（1）初始化时（ReactDOM.render()触发）**

&emsp;&emsp;1.constructor（运行一次，可读数据，可同步修改 state，异步修改 state 需要 setState,setState 在实例产生后才可以使用，可以访问到 props）

&emsp;&emsp;2.即将挂载 componentWillMount

&emsp;&emsp;3.render (创建虚拟 dom，进行 diff 算法，更新 dom 树)

&emsp;&emsp;4.组件渲染之后调用 componentDidMount (一般在这个钩子中做一些初始化的事，例如：开启定时器、发送网络请求、订阅消息) 常用更新阶段

**（2）更新时（正常顺序）（setState()触发）**

&emsp;&emsp;1.setState 数据更改

&emsp;&emsp;2.shouldComponentUpdate 是否需要更新（return true 就会更新 dom，false 阻止更新）

&emsp;&emsp;3.componentwillUpdate

&emsp;&emsp;4.render

&emsp;&emsp;5.componentDidMount

**（3）强制更新（setState 没有数据更改）（forceUpdate()触发）**

&emsp;&emsp;1.forceUpdate 强制更新

&emsp;&emsp;2.componentwillUpdate（不会被 shouldComponentUpdate 阀门 false 阻止）

&emsp;&emsp;3.render

&emsp;&emsp;4.componentDidMount

**（4）父组件数据更新，子组件更新（componentWillReceiveProps 第一次父组件挂载不会触发，传入新的 props 才会触发）**

&emsp;&emsp;1.父：setState —— shouldComponentUpdate —— componentwillUpdate —— render —— componentDidMount

&emsp;&emsp;2.子：componentWillReceiveProps —— shouldComponentUpdate —— componentwillUpdate —— render —— componentDidMount

**componentWillReceiveProps(props){ console.log('可以接收到',props) }**

&emsp;&emsp;更新阶段 componentDidUpdate 组件数据更新完成后调用

&emsp;&emsp;卸载阶段 compoenentWillUnmount (即将卸载，可以做一些组件相关的清理工作，如青春计时器、网络请求等 )常用

&emsp;&emsp;组件卸载： unmountComponentAtNode(document.getElementById('root'))

&emsp;&emsp;所有子组件挂载完成，才标记着父组件挂载完成，父组件更新，子组件更新，子组件更新，子组件不更新

&emsp;&emsp;新的生命周期有小改动（不是安全性问题，只是未来版本异步渲染可能带来的 bug）前缀加 UNSAFE\_

&emsp;&emsp;&emsp;&emsp;UNSAFE_componentWillUpdate()
&emsp;&emsp;&emsp;&emsp;UNSAFE_componentWillReceiveProps()
&emsp;&emsp;&emsp;&emsp;UNSAFE_componentWillMount()

**旧生命周期 demo**
```js
//创建组件
class Count extends React.Component {
  //构造器
  constructor(props) {
    console.log('Count---constructor')
    super(props)
    //初始化状态
    this.state = { count: 0 }
  }

  //加1按钮的回调
  add = () => {
    //获取原状态
    const { count } = this.state
    //更新状态
    this.setState({ count: count + 1 })
  }

  //卸载组件按钮的回调
  death = () => {
    ReactDOM.unmountComponentAtNode(document.getElementById('test'))
  }

  //强制更新按钮的回调
  force = () => {
    this.forceUpdate()
  }

  //组件将要挂载的钩子
  componentWillMount() {
    console.log('Count---componentWillMount')
  }

  //组件挂载完毕的钩子
  componentDidMount() {
    console.log('Count---componentDidMount')
  }

  //组件将要卸载的钩子
  componentWillUnmount() {
    console.log('Count---componentWillUnmount')
  }

  //控制组件更新的“阀门”
  shouldComponentUpdate() {
    console.log('Count---shouldComponentUpdate')
    return true
  }

  //组件将要更新的钩子
  componentWillUpdate() {
    console.log('Count---componentWillUpdate')
  }

  //组件更新完毕的钩子
  componentDidUpdate() {
    console.log('Count---componentDidUpdate')
  }

  render() {
    console.log('Count---render')
    const { count } = this.state
    return (
      <div>
        <h2>当前求和为：{count}</h2>
        <button onClick={this.add}>点我+1</button>
        <button onClick={this.death}>卸载组件</button>
        <button onClick={this.force}>不更改任何状态中的数据，强制更新一下</button>
      </div>
    )
  }
}

//父组件A
class A extends React.Component {
  //初始化状态
  state = { carName: '奔驰' }

  changeCar = () => {
    this.setState({ carName: '奥拓' })
  }

  render() {
    return (
      <div>
        <div>我是A组件</div>
        <button onClick={this.changeCar}>换车</button>
        <B carName={this.state.carName} />
      </div>
    )
  }
}

//子组件B
class B extends React.Component {
  //组件将要接收新的props的钩子
  componentWillReceiveProps(props) {
    console.log('B---componentWillReceiveProps', props)
  }

  //控制组件更新的“阀门”
  shouldComponentUpdate() {
    console.log('B---shouldComponentUpdate')
    return true
  }
  //组件将要更新的钩子
  componentWillUpdate() {
    console.log('B---componentWillUpdate')
  }

  //组件更新完毕的钩子
  componentDidUpdate() {
    console.log('B---componentDidUpdate')
  }

  render() {
    console.log('B---render')
    return <div>我是B组件，接收到的车是:{this.props.carName}</div>
  }
}

//渲染组件
ReactDOM.render(<Count />, document.getElementById('root'))
```

## 新的生命周期（新增了 getDerivedStateFromProps、getSonapshotBeforeUpdate 来替代弃用的三个钩子函数(compostWillMount、componentWillReceiveProps、componentWillUpdate）

**（1）加载时**

&emsp;&emsp;1.getDerivedStateFromProps(props,state)

&emsp;&emsp;2.render()

&emsp;&emsp;3.componentDidMount()

```js
渲染前 static getDerivedStateFromProps(props,state)
无法访问this
props,state是更新后的
必须返回一个对象，用来 更新state 或者 null不更新
必须要初始化state
场景：state的值在任何时候都取决于props时
```

**（2）更新时**

&emsp;&emsp;1.getDerivedStateFromProps(props,state)

&emsp;&emsp;2.render()

&emsp;&emsp;3.getSnapshortBeforeUpdate(prevProps,prevState)（更新前拿到一些页面的快照）

```js
组件能在发生更改之前从DOM中捕获一些信息(dom渲染前的状态,例如滚动位置)
返回值｜null 会给componentDidUpdate
prevProps、prevState 更新前this.props、this.state更新后
```

**新的生命周期 demo**

```js
class Count extends React.Component {
  constructor(props) {
    console.log('Count---constructor')
    super(props)
    //初始化状态
    this.state = { count: 0 }
  }

  //加1按钮的回调
  add = () => {
    //获取原状态
    const { count } = this.state
    //更新状态
    this.setState({ count: count + 1 })
  }

  //卸载组件按钮的回调
  death = () => {
    ReactDOM.unmountComponentAtNode(document.getElementById('test'))
  }

  //强制更新按钮的回调
  force = () => {
    this.forceUpdate()
  }

  //若state的值在任何时候都取决于props，那么可以使用getDerivedStateFromProps
  static getDerivedStateFromProps(props, state) {
    console.log('getDerivedStateFromProps', props, state)
    return null
  }

  //在更新之前获取快照
  getSnapshotBeforeUpdate() {
    console.log('getSnapshotBeforeUpdate')
    return 'atguigu'
  }

  //组件挂载完毕的钩子
  componentDidMount() {
    console.log('Count---componentDidMount')
  }

  //组件将要卸载的钩子
  componentWillUnmount() {
    console.log('Count---componentWillUnmount')
  }

  //控制组件更新的“阀门”
  shouldComponentUpdate() {
    console.log('Count---shouldComponentUpdate')
    return true
  }

  //组件更新完毕的钩子
  componentDidUpdate(preProps, preState, snapshotValue) {
    console.log('Count---componentDidUpdate', preProps, preState, snapshotValue)
  }

  render() {
    console.log('Count---render')
    const { count } = this.state
    return (
      <div>
        <h2>当前求和为：{count}</h2>
        <button onClick={this.add}>点我+1</button>
        <button onClick={this.death}>卸载组件</button>
        <button onClick={this.force}>不更改任何状态中的数据，强制更新一下</button>
      </div>
    )
  }
}

//渲染组件
ReactDOM.render(<Count count={199} />, document.getElementById('root'))
```



<!-- more -->
