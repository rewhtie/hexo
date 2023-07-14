---
title: execl导出
data: 2022-05-13 17:00
tags: 
- 文件
categories: 组件
---

数据导出成execl表格

<!-- more -->

```html
<p style="font-size: 20px;color: red;">使用a标签方式将json导出csv文件</p>
<button onclick='tableToExcel()'>导出</button>
```

```js        
const jsonData = [
    {
        name:'路人甲',
        phone:'123456789',
        email:'000@123456.com'
    },
    {
        name:'炮灰乙',
        phone:'123456789',
        email:'000@123456.com'
    },
    {
        name:'土匪丙',
        phone:'123456789',
        email:'000@123456.com'
    },
    {
        name:'流氓丁',
        phone:'123456789',
        email:'000@123456.com'
    },
];
// 列标题，逗号隔开，每一个逗号就是隔开一个单元格
let str = `姓名,电话,邮箱\n`;
// 增加\t为了不让表格显示科学计数法或者其他格式
for(let i = 0 ; i < jsonData.length ; i++ ){
	for(const key in jsonData[i]){
		str+=`${jsonData[i][key] + '\t'},`;     
	}
	str+='\n';
}
// encodeURIComponent解决中文乱码
const uri = 'data:text/csv;charset=utf-8,\ufeff' + encodeURIComponent(str);
// 通过创建a标签实现
const link = document.createElement("a");
link.href = uri;
// 对下载的文件命名
link.download =  "我是你要的execl模板哦.csv";
link.click();
```

<!-- more -->
