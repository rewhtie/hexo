---
title: Winicss 原子类（样式库）
data: 2023-06-19 16：39：45
categories: 插件、依赖
tags: 
	- 三方库
  - 项目
---

快捷使用 <font color="red">"<\div _flex="~ items-center justify-cente"></\div>"</font> 实现div弹性布局水平垂直居中，更加方便的搬砖

<!-- more -->

npm install windicss

#### 1.新建flex样式 plugin/flex.js 文件

```js
import plugin from 'windicss/plugin'
/**
 * flex 布局插件
 */
const flexPlugin = plugin(({ addUtilities }) => {
  addUtilities({
    '.flex-self-auto': {
      'align-self': 'auto'
    },
    '.flex-self-start': {
      'align-self': 'flex-start'
    },
    '.flex-self-end': {
      'align-self': 'flex-end'
    },
    '.flex-self-center': {
      'align-self': 'center'
    },
    '.flex-self-baseline': {
      'align-self': 'baseline'
    },
    '.flex-self-stretch': {
      'align-self': 'stretch'
    },
    '.flex-center': {
      'align-items': 'center',
      'justify-content': 'center'
    },
    '.flex-justify-center': {
      'justify-content': 'center'
    },
    '.flex-justify-start': {
      'justify-content': 'flex-start'
    },
    '.flex-justify-end': {
      'justify-content': 'flex-end'
    },
    '.flex-justify-between': {
      'justify-content': 'space-between'
    },
    '.flex-justify-around': {
      'justify-content': 'space-around'
    },
    '.flex-justify-evenly': {
      'justify-content': 'space-evenly'
    },
    '.flex-items-center': {
      'align-items': 'center'
    },
    '.flex-items-start': {
      'align-items': 'flex-start'
    },
    '.flex-items-end': {
      'align-items': 'flex-end'
    },
    '.flex-items-between': {
      'align-items': 'space-between'
    },
    '.flex-items-around': {
      'align-items': 'space-around'
    },
    '.flex-items-evenly': {
      'align-items': 'space-evenly'
    }
  })
})

export default flexPlugin

```


#### 2.新建行样式 plugin/lineClamp.js 文件

```js
import plugin from 'windicss/plugin'

/**
 * 溢出隐藏插件
 */
const lineClampPlugin = plugin(({ addUtilities }) => {
  addUtilities({
    '.line-clamp-1, .line-clamp-2, .line-clamp-3, .line-clamp-4, .line-clamp-5, .line-clamp-6, .line-clamp-7': {
      overflow: 'hidden'
    },
    '.line-clamp-1': {
      'text-overflow': 'ellipsis',
      'white-space': 'nowrap'
    },
    '.line-clamp-2, .line-clamp-3, .line-clamp-4, .line-clamp-5, .line-clamp-6, .line-clamp-7': {
      overflow: 'hidden',
      display: '-webkit-box',
      '-webkit-box-orient': 'vertical'
    },
    '.line-clamp-2': {
      '-webkit-line-clamp': '2'
    },
    '.line-clamp-3': {
      '-webkit-line-clamp': '3'
    },
    '.line-clamp-4': {
      '-webkit-line-clamp': '4'
    },
    '.line-clamp-5': {
      '-webkit-line-clamp': '5'
    },
    '.line-clamp-6': {
      '-webkit-line-clamp': '6'
    },
    '.line-clamp-7': {
      '-webkit-line-clamp': '7'
    }
  })
})

export default lineClampPlugin

```

#### 3.将新建样式添加进配置 ~ 属性模式配置 plugin/index.js 文件

```js
import flexPlugin from './plugin/flex'
import lineClampPlugin from './plugin/lineClamp'
import { FullConfig } from 'windicss/types/interfaces'

const windPreset: FullConfig = {
  preflight: false,
  prefix: 'w-',
  attributify: {
    prefix: '_'
  },
  plugins: [flexPlugin, lineClampPlugin]
}

export default windPreset
```

#### 4.项目使用 引入 plugin/index.js

```js
import plugin from 'windicss/plugin'
import { windPreset } from '@/plugin/index.js'

const mpptxPlugin = plugin(({ addComponents, addUtilities }) => {
  addUtilities({
    '.click': {
      cursor: 'pointer',
      transition: 'filter 300ms',
      '&:active': {
        filter: 'brightness(1.1)'
      }
    }
  })

  addComponents({
    '.btn, .btn-info, .btn-primary': {
      paddingLeft: '16px',
      paddingRight: '16px',
      paddingTop: '7px',
      paddingBottom: '7px',
      transition: 'all 100ms', 
      backgroundColor: '#fff',
      borderRadius: '6px',
      borderWidth: '1px',
      borderStyle: 'solid',
      borderColor: '#DEDEDE',
      cursor: 'pointer',
      userSelect: 'none',
      textAlign: 'center',
      fontSize: '16px',
      '&:active': {
        filter: 'brightness(0.9)'
      }
    },
    '.btn-info': {
      color: '#535353',
      borderColor: '#DEDEDE'
    },
    '.btn-primary': {
      color: '#fff',
      backgroundColor: '#FF5E2A'
    },
    '.btn-disabled': {
      opacity: '0.5',
      pointerEvents: 'none'
    }
  })
})

windPreset.plugins!.push(mpptxPlugin)

export default windPreset
```

#### 5.vite.config.ts配置

<font color="#08c">安装依赖</font>

```bash
npm install vite-plugin-windicss windicss
```

<font color="#08c">vite.config.ts</font>

```js
import { defineConfig } from "vite";
import WindiCSS from 'vite-plugin-windicss'
export default defineConfig({
  plugins: [WindiCSS({
    scan: {
      dirs: ['.'], // 当前目录下所有文件
      fileExtensions: ['vue', 'js', 'ts'], // 同时启用扫描vue/js/ts
    },
  }),],
});
```

<font color="#08c">main.ts</font>

```js
import { createSSRApp } from 'vue'
import App from './App.vue'
import 'virtual:windi-components.css'
import 'virtual:windi-utilities.css'
export function createApp() {
	const app = createSSRApp(App)
	return {
		app
	}
}
```

#### 6.webpack构建vue.config.ts配置

<font color="#08c">vue.config.ts</font>

```js
// 其他配置忽略
module.exports = {
  pluginOptions: {
    windicss: {}
  },
}
```

<font color="#08c">main.ts</font>

```js
import { createApp } from 'vue'
import App from './App.vue'
import 'windi.css'
const app = createApp(App)
app.mount('#app')
```

#### 7.常用的命令


| 样式 |  格式  |
|  ----  | ----  |
|  边框圆角，左上角圆角	|  _border="2px solid [#B6B6B6] rounded-6px rounded-tl-17px"  |
|  鼠标样式	  |  _cursor="pointer"  |
|  内边距 y轴上下各6px，x轴左右各2px ，外边距同理	  |  _p="y-6px x-2px"  |
|  出现动画时间 |  _transition="duration-300"  |
|  文本超出1行溢出隐藏  |  _line="clamp-1"  |
|  1/3 宽度   |  _overflow="y-auto" |
|  滚动条 |  application/msword	          |
|  弹性布局、水平垂直居中、纵轴  |  _flex="~ items-center justify-center col"  |
|  鼠标禁用样式、点击样式、三元切换 |  :class="[ visible ? 'w-cursor-not-allowed' : 'w-cursor-pointer' ]"  |
|  绝对定位，0，右120px，层级9 |  _pos="absolute" _bottom="0" _right="120px" _z="9"  |


<!-- more -->
