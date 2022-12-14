---
layout: post
title: hexo（文章排序不生效）
date: 2022-12-14 18:03:40
tags: hexo
---



<!-- more -->

> 文章设置date时间，但是实际排序不生效（不是版本问题，不是date、data字段错误问题，其他依赖依旧没用）

- 找到node_moudles 模块下 hexo-generator-index 文件夹 lib 下的 generator.js [官网插件](https://hexo.io/plugins/)
- 直接用以下的代码覆盖替换

```js
'use strict';
var pagination = require('hexo-pagination');
module.exports = function(locals){
  var config = this.config;
  var posts = locals.posts;
    posts.data = posts.data.sort(function(a, b) {
        if(a.top && b.top) { // 两篇文章top都有定义
            if(a.top == b.top) return b.date - a.date; // 若top值一样则按照文章日期降序排
            else return b.top - a.top; // 否则按照top值降序排
        }
        else if(a.top && !b.top) { // 以下是只有一篇文章top有定义，那么将有top的排在前面（这里用异或操作居然不行233）
            return -1;
        }
        else if(!a.top && b.top) {
            return 1;
        }
        else return b.date - a.date; // 都没定义按照文章日期降序排
    });
  var paginationDir = config.pagination_dir || 'page';
  return pagination('', posts, {
    perPage: config.index_generator.per_page,
    layout: ['index', 'archive'],
    format: paginationDir + '/%d/',
    data: {
      __index: true
    }
  });
};
```
<!-- more -->
