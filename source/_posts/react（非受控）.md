
---
title: react非受控组件和受控组件
data: 2022-06-11 16:00
tags:
  - react
categories: 框架
---

非受控组件需要ref获取节点实例获取到value值，受控是把值更新存储到state，更新改变（避免了ref过度使用）

<!-- more -->

```js
// 非受控组件
class demo extends React.Component {
  submit = event => {
    event.preventDefault() //阻止表单提交
    const { name, pass } = this
    console.log(name, passs) //拿不到;${name.value} , ${pass.value}这样才能拿到值
  }
  render() {
    return (
      <form onSubmit={this.submit}>
        <input type="text" name="name" ref={c => (this.name = c)} />
        <input type="text" name="pass" ref={c => (this.pass = c)} />
        <button>提交</button>
      </form>
    )
  }
}
// 受控组件(所有输入类的受到state状态改变)
class demo2 extends React.Component {
  state = {
    name: '',
    pass: '',
  }
  nameClick = event => {
    this.setState({ name: event.target.value })
  }
  passClick = event => {
    this.setState({ pass: event.target.value })
  }
  submit = event => {
    event.preventDefault() //阻止表单提交
    const { name, pass } = this.state
    console.log(name, passs) //拿到值
  }
  render() {
    return (
      <form onSubmit={this.submit}>
        <input type="text" name="name" onChange={this.nameClick} />
        <input type="text" name="pass" onChange={this.passClick} />
        <button>提交</button>
      </form>
    )
  }
}
React.render(<demo />, document.getElementById('test'))
```

<!-- more -->