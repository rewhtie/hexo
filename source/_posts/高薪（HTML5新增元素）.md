---
title: CSS常问
tags:
  - 高薪之路
---

经常有的一些 CSS 效果实现，包括问的呀，写的呀，问来问去都是这些基本

<!-- more -->

## HTML5 新增的语义标签

| 新增标签 | 标签语义 |
| :-: | :-- |
| header | 定义一个页面的页眉 |
| nav | 定义导航栏链接的部分 |
| aside | 定义页面主区域内容之外的内容，比如侧边栏 |
| section | 定义文档中的节（section、区段），section 内应包含一组内容(\<span\>、\<p\>等)及其标题(\<h1\>-\<h6\>) |
| article | 定义独立的文章内容，\<article\>内应包含一组内容(\<span\>、\<p\>等)及其标题(\<h1\>-\<h6\>) |
| figure | 定义独立的流内容（图像、图表、照片、代码等等），元素的内容应该与主内容相关，但如果被删除，则不应对文档流产生影响 |
| figcaption | 定义 \<figure\> 元素的标题，\<figcaption\>应该被置于 \<figure\> 元素的第一个或最后一个子元素的位置。 |
| footer | 定义一个页面的页脚 |

## H5/C3 面试题

<font size=4 color='#4DD7EB'> 如何关闭 IOS 键盘首字母自动大写</font>

- < input type = "text" <font color='red'> autocapitalize = "off"</font>/ >
- The autocapitalize 全局属性 是一个枚举属性，它控制用户输入/编辑文本输入时文本输入是否自动大写，以及如何自动大写。属性必须取下列值之一：
  - off or none: 没有应用自动大写（所有字母都默认为小写字母）。
  - on or sentences: 每个句子的第一个字母默认为大写字母；所有其他字母都默认为小写字母。
  - words: 每个单词的第一个字母默认为大写字母；所有其他字母都默认为小写字母。
  - characters: 所有的字母都默认为大写。

<font size=4 color='#4DD7EB'> 怎么让 Chrome 支持小于 12px 的文字？</font>

- 浏览器最小就是 12px 字体，只能通过<font color='#4DD7EB'> webkit-transform:scale(0.8)</font> 来控制
- 会影响元素导致布局错乱

<!-- more -->
