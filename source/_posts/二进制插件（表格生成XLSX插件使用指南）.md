---
title: XLSX插件使用指南
data: 2023-06-19 16：39：45
tags: 
	- 三方库
	- 文件
categories: 插件、依赖
---

导出execl、导入execl解析树形结构

<!-- more -->

[xlsx官网文档](https://docs.sheetjs.com/docs/api/utilities/array/)

```bash
npm install XLSX
```

### 1.execl文件导出

例如

![](http://linmingqi.top/img/execl%E8%A1%A8%E6%A0%BC%E5%AF%BC%E5%87%BA.png)

```js
const exportExecl = () => {
	const workbook = XLSX.utils.book_new();
	const worksheet = XLSX.utils.aoa_to_sheet([
		['一级目录', '二级目录', '三级目录', '四级目录'],
		['第一单元 我和我的祖国', '', '', ''],
		['', '第一章节 我的祖国', '', ''],
		['', '', '欣赏', ''],
		['', '', '', '我的中国心'],
		['', '', '演唱', ''],
		['', '', '', '我的祖国'],
	]);
	const columnWidths = [ // 设置每列宽度
		{ wpx: 200 },
		{ wpx: 200 },
		{ wpx: 200 },
		{ wpx: 200 }
	];
	worksheet['!cols'] = columnWidths;
	XLSX.utils.book_append_sheet(workbook, worksheet, 'Sheet1'); // 也可以导出csv纯json格式数据，具体实现官网
	XLSX.writeFile(workbook, '模板.xlsx');
}
```

### 2.execl文件导入解析树状结构

拿到上面导出的execl导入解析需要转换为以下格式、树状结构

```ts
const jsonData = [
  {
    title:'第一单元 我和我的祖国',
    level: 1,
    children: [
      {
        title:'第一章节 我和我的祖国',
        level: 2,
        children: [
          {
            title:'欣赏',
            level: 3,
            children: [
              {
                title:'我的中国心',
                level: 4,
                children: null
              }
            ]
          },
          {
            title:'演唱',
            level: 3,
            children: [
              {
                title:'我的祖国',
                children: null
              }
            ]
          },
        ]
      }
    ],
  },
];
```

#### 导入execl获取到表格的数据

通过reducePush找到符合条件的各个元素下标位置

```js
function importExecl() {
	const file = await uploadFileEvent({
		accept: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
	})
	const data = await readBlobAsArrayBuffer(file)
	const workbook = XLSX.read(data, { type: 'array' })
	const worksheet = workbook.Sheets[workbook.SheetNames[0]]
	const jsonData = XLSX.utils.sheet_to_json(worksheet)
	// console.log('jsonData最开始结构', jsonData)
	const oneIndex = reducePush(jsonData, '一级目录')
	// console.log('jsonData第一级目录下标', oneIndex) [1,9,10,11,12]
	const twoIndex = reducePush(jsonData, '二级目录')
	// console.log('jsonData第二级目录下标', twoIndex) [2,8,13,16]
	const threeIndex = reducePush(jsonData, '三级目录')
	// console.log('jsonData第三级目录下标', threeIndex) [3,14,17]
	const fourIndex = reducePush(jsonData, '四级目录')
	// console.log('jsonData第四级目录下标', fourIndex) [4,5,6,7,15,18,19]
}
```

#### 分析数据结构

<font color='red' id='back'>缕清之间的关系oneIndex、twoIndex、threeIndex、fourIndex</font> [具体结构](#json) 

- 一级目录有1,9,10,11,12,发现1-9中间就是1的子级，9、10、11没有子级，大于12的就是12的子级 
- 二级目录2,8是1的子级，13,16是12的子级
- 三级目录3是2的子级，14是13的子级，17是16的子级
- 四级目录4,5,6,7是3的子级，15是14的子级，18,19是17的子级

<font color='red'>其实就是相邻之间的范围值就是要填充的位置，最后一位时肯定是空的，只需要判断 下标+1 的时候是undefined 就能知道是最后一位，直接大于最后一位</font>


#### 逻辑实现代码

```js
let arrayOne = []
oneIndex.forEach((one, oneIdx) => {
	let a = {
		title: jsonData[one]['一级目录'],
		children: []
	}
	twoIndex.forEach((two, twoIdx) => {
		if (two > one && (two < oneIndex[oneIdx + 1] || oneIndex[oneIdx + 1] == undefined)) {
			let b = {
				title: jsonData[two]['二级目录'],
				children: []
			}
			a.children.push(b)
			threeIndex.forEach((three, threeIdx) => {
				if (three > two && (three < twoIndex[twoIdx + 1] || twoIndex[twoIdx + 1] == undefined)) {
					let c = {
						title: jsonData[three]['三级目录'],
						children: []
					}
					b.children.push(c)
					fourIndex.forEach(four => {
						if (four > three && (four < threeIndex[threeIdx + 1] || threeIndex[threeIdx + 1] == undefined)) {
							let d = {
								title: jsonData[four]['四级目录'],
								children: null
							}
							c.children.push(d)
						}
					})
				}
			})
		}
	})
	arrayOne.push(a)
})
```


#### reducePush函数

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



<font id='json'>树形数据结构展示</font> [回到触发点](#back) 

```js
data = [
	{
		id_1: jsonData[1],
		children: [
			{
				id_2: jsonData[2],
				children:[
					{
						id_3: jsonData[3],
						children: [
							{
								id_4: jsonData[4],
								children: null
							},
							{
								id_4: jsonData[5],
								children: null
							},
							{
								id_4: jsonData[6],
								children: null
							},
							{
								id_4: jsonData[7],
								children: null
							}
						]
					}
				]
			},
			{
				id_2: 8,
				children: null
			},
		]
	},
	{
		id_1: jsonData[9],
		children: null
	},
	{
		id_1: jsonData[10],
		children: null
	},
	{
		id_1: jsonData[11],
		children: null
	},
	{
		id_1: jsonData[12],
		children: [
			{
				id_2: jsonData[13],
				children: [
					{
						id_3: jsonData[14],
						children: [
							{
								id_4: jsonData[15],
								children: null
							}
						]
					},
				]
			},
			{
				id_2: jsonData[16],
				children: [
					{
						id_3: jsonData[17],
						children: [
							{
								id_4: jsonData[18],
								children: null
							},
							{
								id_4: jsonData[19],
								children: null
							},
						]
					},
				]
			},
		]
	}
]

```

<!-- more -->