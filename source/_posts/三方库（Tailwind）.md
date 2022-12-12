---
title: Tailwind 原子类（样式库）
data: 2022-08-19 16:39:45
tags: 
	- 三方库
---

每个样式单独拆解类名，组合使用


<!-- more -->

## 优雅写法

#### 1.标签内class优雅

```js
:class="['lottie', `lottie-${Index}`]"
```

定义类名加个标识后缀（数字最方便，循环的时候可以依据下标来实现对应效果），通过改变变量来控制样式变化

#### 2.style定义类优雅

Tailwind第三方css库[官网](https://www.tailwindcss.cn/docs)

通过 npm 安装 Tailwind
```js
npm install -D tailwindcss@npm:@tailwindcss/postcss7-compat @tailwindcss/postcss7-compat postcss@^7 autoprefixer@^9
```

生成tailwind.config.js 和 postcss.config.js 文件：
```js
npx tailwindcss init -p
```

项目使用
```js
//建一个css文件写入
@tailwind base;
@tailwind components;
@tailwind utilities;
```
然后在项目入口处引入新建的css文件即可


接下来配置 Tailwind 来移除生产环境下没有使用到的样式声明
tailwind.config.js 文件中，配置 purge 选项指定所有的 pages 和 components 文件，使得 Tailwind 可以在生产构建中对未使用的样式进行摇树优化。
```js
  module.exports = {
-   purge: [], 
+   purge: [ './src/**/*.{vue,js,ts,jsx,tsx}'],
    darkMode: false, // or 'media' or 'class'
    theme: {
      extend: {},
    },
    variants: {
      extend: {},
    },
    plugins: [],
  }
```

<!-- more -->
