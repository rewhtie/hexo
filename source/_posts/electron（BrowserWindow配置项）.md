---
title: electron（new BrowserWindow配置项）
date: 2023-07-17 19:46:01
tags:
  - electron
categories: 框架
---

基础配置项

<!-- more -->
### new BrowserWindow 基础配置使用

[BrowserWindow api](https://www.electronjs.org/zh/docs/latest/api/browser-window#new-browserwindowoptions)

```ts
const mainWin = new BrowserWindow({
	show: false, // 先隐藏窗口，不然加载index.html会出现短暂空白
	frame: false // 隐藏默认菜单、无边框窗口
	width: 100, // 窗口宽度 ; maxWidth | minWidth
	height: 200, // 窗口高度 ; maxHeight | minHeight
	icon: '', // 图标路径
	title: '萌新', // 窗口标题，记得index.html的title要为空
	autoHideMenuBar: true,
	// 允许渲染进程使用node.js集成环境
	webPreferences: {
		preload: path.resolve(__dirname, process.env.QUASAR_ELECTRON_PRELOAD!),
		webSecurity: false,
		nodeIntegrationInWorke: true, //是否在Web工作器中启用了Node集成. 默认值为 false. 
		nodeIntegration: true // 是否启用Node integration. 默认值为 false.
	}
})
mainWin.loadFile('index.html')
mainWin.on('ready-to-show', () => {
	// 加载完页面窗口显示
	mainWin.show()
})
// 关闭窗口需要释放窗口
mainWin.on('closed', () => {
	mainWin = null
})
```

### 调用api实现窗口的 - 口 X 事件

[ipc 进程通信](https://www.electronjs.org/zh/docs/latest/tutorial/ipc)

<!-- more -->