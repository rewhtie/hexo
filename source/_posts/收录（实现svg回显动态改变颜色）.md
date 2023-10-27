---
layout: post
title: 收录（实现svg回显动态改变颜色）
date: 2023-10-26 15:39:34
tags: 
  - 收录
  - CSS
---

利用img标签回显svg实现改变颜色


<!-- more -->

img标签回显svg，在线svg链接同样适用
```html
<div style="overflow:hidden">
  <img src="./demo.svg" />
</div>
```

img添加动态style样式
```ts
const style = {
  filter: `drop-shadow(-1000px 0px 0px ${elementInfo.fill || 'black'})`,
  transform: 'translateX(1000px)',
}
```

>重点：
  filter属性 给图片生成一个一模一样的阴影
  transform 给原图移出可视区域（父元素overflow属性会看不见）
  overflow 父元素hidden超出隐藏
  drop-shadow 原图移出1000px，阴影位置-1000px，通过改变阴影颜色达到改变 图标svg颜色的 假象
缺点：不是所有svg适用，只适合透明背景的svg资源

<!-- more -->