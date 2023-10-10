---
layout: post
title: git提交撤销本地注释
date: 2023-10-10 17:26:46
tags: 收录
---

git过滤本地注释，慎用，有时候拉取他人代码会把自己的代码也给干掉

<!-- more -->

找到文件.git的config用记事本打开，加入以下
```ts
[filter "filter-js"]
	clean = sed '/\\/\\*\\*\\s*@filter.*\\*/d'
[filter "filter-css"]
	clean = sed '/\\/\\/\\s*@filter.*/d'
[filter "filter-html"]
	clean = sed '/<\\!--\\s*@filter.*-->/d'
[filter "filter-vue"]
	clean = sed -e '/\\/\\*\\*\\s*@filter.*\\*/d' -e '/\\/\\/\\s*@filter.*/d' -e '/<\\!--\\s*@filter.*-->/d'
```

找到文件.gitattributes
```ts
*.ts filter=filter-js
*.js filter=filter-js
*.css filter=filter-css
*.html filter=filter-html
*.vue filter=filter-vue
```

效果
```ts
本地vscode
/** @filter 我是注释 */
const a = 1

远程
const a = 1
```
<!-- more -->