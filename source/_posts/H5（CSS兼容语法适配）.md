---
title: H5（CSS兼容语法适配）
data: 2022-12-14 14：07：20
tags: 
  - CSS
  - H5
---

H5 的 样式兼容各个浏览器的毒点、实现效果

<!-- more -->

### IOS底部小黑条影响布局

```css
.safe-area-bottom {
  padding-bottom: constant(safe-area-inset-bottom) !important;
  padding-bottom: env(safe-area-inset-bottom) !important;
}
```
<font color="red">注：假如元素本身有padding-bottom边距，会被覆盖</font>

### 多个img标签拼接处留有白缝

```css
.img {
  display: block;
  line-height: 0
}
```

### height单位100vh 会把一部分浏览器的底部算进去，导致布局底部的被遮挡

<font color="red">注：不要使用vh单位，改为100%</font>

### line-height 在华为系统会有偏差

```css
.font-center {
  display: flex;
  justify-content: center;
  align-items: center;
}
```
<font color="red">注：改为flex布局水平垂直居中</font>

<!-- more -->