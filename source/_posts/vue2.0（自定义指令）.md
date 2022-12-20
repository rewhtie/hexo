---
title: vue2.0（自定义指令）
date: 2022-12-20 16:04:28
tags: vue2.0
---

场景：点击到页面的其他标签 隐藏或者收起 某元素标签

<!-- more -->

使用示例
```html
<div v-hide="callback" @click="click"></div>
```

传入回调
```js
Vue.directive("hide", {
  bind: function (el, binding) {
    console.log(binding.value);
    var handler = function (e) {
      binding.value(e);
    };
    el.__vueHide__ = handler;
    el.addEventListener("click", handler, true);
  },
  unbind: function (el) {
    el.removeEventListener("click", el.__vueHide__, true);
    el.__vueHide__ = null;
  }
});
```


<!-- more -->
