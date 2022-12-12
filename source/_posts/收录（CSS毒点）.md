---
title: 你不知道的css几个小细节
data: 2019-12-19 16:39:45
tags: 
	- 收录
---



帮你踩css的一些毒坑，为你的烦恼解忧

<!-- more -->
## 痛点

#### 1.多个img标签拼接处留有白缝

```
display: block;
line-height: 0
```

完美解决你的痛点

#### 2.组件样式更改

```
/deep/ .class
：deep(.class)
```

有时候deep写法不太一样

#### 3.网页无法复制字体

```
user-select: text;
```

对组件库表格组件无法复制真的一大痛点，一条属性直接简单粗暴

#### 4.滚动条问题

```js
//滚动条置顶
window.scrollTo(0, 0); 
```

```js
//阻止默认事件的触发
const mo = function (e) {
    e.preventDefault();
};
```

```js
//禁止页面滑动
document.body.style.overflow = "hidden";
document.addEventListener("touchmove", mo, false); 
```

```js
//出现滚动条，解除限制
document.body.style.overflow = ""; 
document.removeEventListener("touchmove", mo, false);
```

vue项目可以通过watch监听实现页面是否可以滚动

示例：这里可以设置路由跳转时滚动条置顶
```js
router.beforeEach((to, from, next) => {
  document.body.scrollTop = 0
  // firefox
  document.documentElement.scrollTop = 0
  next()
})
```

#### 5.style标签的scoped属性
```js
body,html{
  // 设置样式的时候不能加scoped
}
```

<!-- more -->
