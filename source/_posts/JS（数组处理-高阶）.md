---
title: JavaScript数组方法——高阶
data: 2023-06-11 10:42
tags: JavaScript
categories: 开发语言
---

系统化的数组方法: 偏客制化

<!-- more -->
#### 1.找出对象数组某属性值为空的元素，生成一个新数组

<font color="#08c" size=3>传统：findIndex()函数找寻元素所在的数组下标位置，和push()配合使用实现</font>

<font color="#08c" size=3>进阶：reduce()、push()配合使用</font><font color="red" size=3>（好处：能同时获取该元素、或者下标，同时实现findIndex()和find()）</font>

```ts
/*
**data：数组
**propertyName: 属性名
*/
const reducePush = (data: Array, propertyName: String) => {
	const array = data.reduce((indexes, obj, index) => {
		if (obj[propertyName]) {
			// index下标、obj元素内容
			indexes.push(obj);
		}
		return indexes;
	}, [])
	return array
}
const data = [{ name: '', age: '11' }, { name: '小明', age: '12' }]
const newData = reducePush(data, 'name') 
// find效果newData: [{ name: '小明', age: '12' }]
// findIndex效果newData: [1]
```

#### 2.递归过滤整个数组对象某属性值为空的值

<font color="#08c" size=3>一般用于树形结构，判断title属性为空过滤</font>

```ts
/*
**data：数组
**propertyName: 属性名
*/
const removeEmptyItems = (data: Array, propertyName: String) => {
	const newArr = [];
	for (let i = 0; i < arr.length; i++) {
		const item = arr[i];
		if (item[propertyName] !== '') {
			if (Array.isArray(item.children)) {
				item.children = this.removeEmptyItems(item.children);
			}
			newArr.push(item);
		}
	}
	return newArr;
}
const data = [
	{ name: '', age: '11' }, 
	{ 
		name: '小明', 
		age: '12', 
		children: [
			{ name: '', age: '110' }, 
			{ name: '小黑', age: '120' }, 
		]
	}
]
const newData = reducePush(data, 'name') 
/*
newData: 
[
	{ 
		name: '小明', 
		age: '12', 
		children: [
			{ name: '小黑', age: '120' }, 
		]
	}
]
*/
```

