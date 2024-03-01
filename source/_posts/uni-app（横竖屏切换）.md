---
layout: post
title: uni-app（横竖屏切换）
date: 2024-03-01 09:28:12
tags: uni-app
---

vue3、uni-app A页面竖屏、B页面横屏，代码示例

<!-- more -->
#### 没营养的配置项

mainifest.json文件 flexible || screenOrientation

```json
{
	"app-plus": {
		"flexible": true,
		"screenOrientation": ["portrait-primary", "landscape-primary", "portrait-secondary", "landscape-secondary"],
	},
	"vueVersion": "3"
}

```

#### A页面

A页面问题不大，难的在B页面

```ts
onShow(() => {
  // #ifdef APP-PLUS
  plus.screen.lockOrientation('portrait-primary')
  // #endif
})
```

#### B页面

> 同A页面强制设置横屏，需要用到onWindowResize监听窗口变化，（因为我的使用了rpx单位）
rpx 是根据屏幕宽度来计算的，本来300*600的变成 600*300，会导致 使用rpx的字体会变大，间距也是
所以得计算宽高之间的比例，将使用rpx的元素 等比例 缩小（魅族手机好像失效，模拟器完美适配）
计算公式：info.screenHeight / info.screenWidth （因为横屏肯定是高 比 宽 大，不存在其他情况）

```ts
onShow(() => {
	// #ifdef APP-PLUS
	plus.screen.lockOrientation('landscape-primary')
	uni.onWindowResize(windowResizeCallback)
	// #endif
})
const windowResizeCallback = () => {
	const info = uni.getWindowInfo()
	let zoom = (info.screenHeight / info.screenWidth) * 100 + '%'
	let style = {
		zoom: zoom
	}
	maxRPX.value = style
}
const contentStyle = ref({})

// 为了示例好区分，故意使用 onMounted 生命周期
onMounted(async () => {
	contentStyle.value = {
		transformOrigin: `0% 0%`,
		width: '100%',
		height: '100%',
		position: 'fixed'
	}
})
```

```html
<view class="content" :style="contentStyle"></view>
```

<!-- more -->