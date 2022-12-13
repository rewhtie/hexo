---
title: CSS妙招
data: 2022-12-08 17:00
tags:
  - 收录
---

CSS骚操作，拒绝调教

<!-- more -->

<font color="#08c">元素宽度自适应大小，超出父元素宽度隐藏，不够展示也隐藏</font>

>CSS 单行隐藏溢出元素（正常使用 overflow:hideen 会出现一种尴尬的场面，元素展示了一半，需要利用弹性布局来完美实现）

![](http://linmingqi.top/img/%E6%8D%A2%E8%A1%8C%E6%95%88%E6%9E%9C%E5%89%8D.png)
```css
.tag {
  overflow: hidden;
  white-space: nowrap;
}
```

![](http://linmingqi.top/img/%E6%8D%A2%E8%A1%8C%E6%95%88%E6%9E%9C%E5%90%8E.png)
```css
.tag {
  display: flex;
  flex-wrap: wrap;
  max-height: 20px;
}
```

<font color="#08c">flex布局（局部滚动）</font>
```css
<!-- 除去页面默认属性 -->
body,html{
    margin:0;
    padding:0;
    overflow:hidden
}
<!-- 页面容器 -->
.big_div{
    height:100%;
    display:flex;
    flex-direction: column;
}
<!-- 滚动div -->
.small_div{
    flex:1;
    overflow: auto;
    <!-- 200px是容器里面其他元素的总高度，不需要头置顶尾置底的，不需要加 -->
    min-height: calc(100vh-200px); 
}
```


<font color="#08c">排除第一个标签元素</font>
```css
:not(:first-child)
```

<font color="#08c">排除最后一个标签元素</font>
```css
:not(:last-child)
```

<font color="#08c">高度自动撑开，保持跟父元素高度一致</font>
```css
div{
    height: initial;
}
```

<font color="#08c">镂空效果</font>
```css
.mask-box {
    position: absolute;
    box-shadow: 1px 1px 1px 1500px rgba(0, 0, 0, 0.8);
    z-index: 100;
    margin: 0;
    padding: 0;
    border-radius: 1.2rem;
    border: 2px solid #ff7326;
    box-sizing: border-box;
    top: 10.3rem;
    left: 8.9rem;
    width: 19.8rem;
    height: 11.4rem;
}
```

<font color="#08c">进度条</font>
```css
.schedule {
    margin-top: 1rem;
    width: 29.1rem;
    height: 2.3rem;
    position: relative;
    overflow: hidden;  
    border-radius: 11rem; 
    span {
        width: 29.1rem;
        height: 2.3rem;
    }
    div {
        position: absolute;
        height: 2.3rem;
        background: linear-gradient(90deg, #fff4c6 0%, #ffb424 100%);
    }
}
```

<font color="#08c">文本框图标</font>
```css
input{
    outline: none;//文本框去除效果
    background-image: url();//背景图标
    background-size: ;大小
    background-repeat: no-repeat;不重复
    background-position: ;定位到合适的位置
    padding-left: ;//输入字体开始位置，之前有个图标所以要在图标之后
    font-size: ;//输入字体，和文本框对比，美观一些
　　background-color: ;//背景色和图片颜色同时显示，这里写要显示的背景色
}
```



<!-- more -->