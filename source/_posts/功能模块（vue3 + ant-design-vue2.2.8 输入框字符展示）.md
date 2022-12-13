---
title: vue2 + ant-design-vue2.2.8 输入框字符长度展示
data: 2022-11-22 12:00
tag:
  - 功能模块
---


ant-design-vue: "^2.2.8" （showCount是否展示字数）这个api无法生效; 可以利用 （suffix 带有后缀图标的input）这个api实现效果

<!-- more -->

```js
<a-input
    :suffix="`${formState.signNo.length}/30`"
    :maxlength="30"
    placeholder="如：F2200070"
    v-model:value="formState.signNo"
/>
```

![](http://linmingqi.top/img/%E5%AD%97%E7%AC%A6%E9%95%BF%E5%BA%A6%E5%B1%95%E7%A4%BA.png)
<!-- more -->