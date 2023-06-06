---
title: Hello World
data: 2020-12-18 12:00
tags: hexo
---

欢迎来到 [Hexo](https://hexo.io/)! 这是您的第一次使用。有关详细信息，请查看文档。如果您在使用Hexo时遇到任何问题，您可以在故障排除中[找到答案](https://hexo.io/docs/)，也可以在[Github上询问我](https://github.com/hexojs/hexo/issues)。
<!-- more -->
## 快速入门

### 安装hexo框架

``` bash
$ npm install hexo-cli -g #全局安装hexo
$ hexo -v #查看hexo版本
```

### 创建新帖子

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### 运行服务器

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### 生成静态文件

需要把.gitignore文件的.deploy*/注释掉

``` bash1
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### 部署到远程站点

``` bash
$ hexo deploy
```

## gitee部署

这里最好部署是master分支，开发是dev分支

``` bash
# blog根目录_connfig.yml
deploy:
  type: git 
  repo: https://gitee.com/lin-mingqi/linmingqi.git #gitee仓库路径
  branch: master
```

无法上传theme主题文件夹到github上，[上传theme主题](https://zhuanlan.zhihu.com/p/349280018)

## hexo插件依赖问题

### 主题修改

```bash
# 根目录_connfig.yml
theme: 主题文件夹名
```
无法上传theme主题文件夹到github上，[上传theme主题](https://zhuanlan.zhihu.com/p/349280018)

### 文章排序不生效（patch-package 插件实现，动态修改node_modules依赖）

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

### 代码块中的{}无法解析问题解决  

node_modules/hexo-prism-plugin/src/index.js文件中map里未支持大括号，补上以下内容后发现有效，即在map中加上对应字符即可:

``` js
const map = {
  '&#39;': '\'',
  '&amp;': '&',
  '&gt;': '>',
  '&lt;': '<',
  '&quot;': '"',
  '&#123;': '{',	//添加的代码
  '&#125;': '}'		//添加的代码
};
```

### hexo-theme-matery主题

[使用指南](https://blinkfox.github.io/2018/09/28/qian-duan/hexo-bo-ke-zhu-ti-zhi-hexo-theme-matery-de-jie-shao/)


More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)

