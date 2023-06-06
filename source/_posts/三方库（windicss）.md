---
title: Winicss 原子类（样式库）
data: 2023-06-19 16：39：45
tags: 
	- 三方库
---

快捷使用 <font color="red">"<\div _flex="~ items-center justify-cente"></\div>"</font> 实现div弹性布局水平垂直居中

<!-- more -->

## 项目配置

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

const defaultConfig: FullConfig = {
  preflight: false,
  prefix: 'w-',
  attributify: {
    prefix: '_'
  },
  plugins: [flexPlugin, lineClampPlugin]
}

export default defaultConfig
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


<!-- more -->
