---
title: JavaScript数组方法
data: 2022-2-11 10:42
tags: JavaScript
categories: 开发语言
---

系统化的数组方法; 数组元素过滤; 数组对比; 数组去重

<!-- more -->

## 数组方法

- <font color="#08c" size=3>findIndex()函数是已知元素，找寻元素所在的数组下标位置，找不到返回-1，跟indexOf有点类似，（传的是回调函数）</font>

```js
    const a = [1,2,3];
    const b = a.findIndex((item)=>{
      return item == '3'
    })
    console.log(a) //打印[1, 2, 3]
    console.log(b) //打印2，2是数组的下标位置
```
- <font color="#08c" size=3>indexOf()函数是最常用的存取函数之一，用来查找传进来的参数在目标数组中是否存在。包含则返回索引，不包含则返回-1。（字符串同样适用）</font>

```js
    const a = [3,1,2,3]
    const b = a.indexOf(3)
    console.log(b) //打印0
```
- <font color="#08c" size=3>lastIndexOf()函数返回相同元素中最后一个元素的索引。</font>

```js
    const a = [3,1,2,3]
    const b = a.lastIndexOf(3)
    console.log(b) //打印3
```

- <font color="#08c" size=3>join()和toString()都可以将数组转换为字符串</font>
    - join()
    ```js
    const a = [1,2,3]
    console.log(a.join('-')) //打印1-2-3
    ```
    - toString()
    ```js
        const a = [1,2,3]
        console.log(a.toString()) //打印1,2,3
    ```
    - toString()还能用于检测变量类型的工作
    ```js
        const a = [1,2,3];
        const b = { 1:1,2:2,3:3 }
        const c = '我是字符串'
        console.log(toString.call(a)) //打印[object Array]
        console.log(toString.call(b)) //打印[object Object]
        console.log(toString.call(c)) //[object String]
    ```

- <font color="#08c" size=3>concat()方法可以合并多个数组创建一个新数组</font>

```js
    const a = [1,2,3]
    console.log(a.concat([4,5])) //打印[1,2,3,4,5]
```

- <font color="#08c" size=3>reverse()方法可以将数组中的元素进行翻转</font>

```js
    const a = [1,2,3]
    console.log(a.reverse()) //打印[3,2,1]
```

- <font color="#08c" size=3>every(),该方法接收一个返回值为布尔类型的函数，对数组中的每个元素使用该函数。如果对于所有的元素，该函数均返回true，则该方法返回true。</font>

```js
    const a = [1,2,3]
    const b = a.every((item)=>{
      return item>0
    })
    console.log(b) //打印true
    // 数组每一项元素符合才会返回true，有一个不符合就返回false
```

- <font color="#08c" size=3>fill() 方法用于将一个固定值替换数组的元素。（会改变原数组）第一个参数必需：填充的值。第二个参数可选：开始填充位置。第三个参数可选：结束填充位置。</font>

```js
    const a = [1,2,3];
    const b = a.fill('1',0,2)
    console.log(a) //打印["1", "1", 3]
    console.log(b) //打印["1", "1", 3]
    //fill('1',0,2)就是数组下标0到2的元素替换成字符串'1'
```

- <font color="#08c" size=3>filter() ：对数组的每一项运行给定函数，返回该函数会返回true的项组成的数组。</font>

```js
    const a = [1,2,3]
    const b = a.filter((item)=>{
      return item / 2
      //  return item%2 打印b的结果就会是这个数组的奇数下标项所组成的新数组
    })
    console.log(b) //打印[3]
    console.log(a) //打印[1,2,3]
```

- <font color="#08c" size=3>forEach()：对数组进行遍历循环，对数组中的每一项运行给定函数。这个方法没有返回值。参数都是function类型，默认有传参，参数分别为：遍历的数组内容item；第对应的数组索引index，数组本身arr。</font>

```js
    const a = ['a','b','c']
    a.forEach((item,index,arr)=>{
      console.log(item,index,arr)
    }) //打印 a 0 ["a", "b", "c"] b 1 ["a", "b", "c"] c 2 ["a", "b", "c"]
```

- <font color="#08c" size=3>map() ：对数组的每一项运行给定函数，返回每次函数调用结果所组成的数组</font>

```js
    const a = [1,2,3,4,5,6];
    const b = a.map((item,index,arr)=>{
      return item / 2
    })
    console.log(a) //打印[1, 2, 3, 4, 5, 6]
    console.log(b) //打印[0.5, 1, 1.5, 2, 2.5, 3]
```

- <font color="#08c" size=3>splice() 可以实现删除、插入和替换。（该方法会改变原始数组）index:数组开始下标;len: 替换/删除的长度;item:替换的值，删除操作的话 item为空</font>
    - 删除
    ```js
        const a = [1,2,3,4]
        const b = a.splice(0,1) //0从数组下标0开始，1是删除从0下标之后的一个元素
        console.log(b) //打印[1]
        console.log(a) //打印[2,3,4]
        //删除了数组下标0开始的一个元素
    ```
    - 插入
    ```js
        const a = [1,2,3,4]
        const b = a.splice(0,0,2)
        console.log(b) //打印[]
        console.log(a) //打印[2,1,2,3,4]
        //splice(0,0,2)其实就是删除下标0之后0个元素，也就是不删除元素，然后在下标0的位置插入元素2
    ```
    - 替换
    ```js
        const a = [1,2,3,4]
        const b = a.splice(0,1,2)
        console.log(b)  //打印[1]
        console.log(a)  //打印[2,2,3,4]
        // 把元素1替换成了2；(其实可以理解成删除了元素1，然后在元素1的下标位置插入了元素2)
    ```

- <font color="#08c" size=3>slice()该方法是对数组进行部分截取（该方法不会改变原始数组），并返回一个数组副本；参数start是截取的开始数组索引，end参数等于你要取的最后一个字符的位置值加上1（可选）</font>
    - 两个参数
    ```js
        const a = [1,2,3,4]
        const b = a.slice(0,2)
        console.log(a) //[1,2,3,4]
        console.log(b) //[1,2]
        // 截取下标0到下标1的数组，第二个参数它会默认+1，所以我们要slice(0,2)
    ```
    - 单个参数
    ```js
        //当只传入一个参数，且是负数时，length会与参数相加，然后再截取，负数参数的绝对值比length大，那会截取整个数组
        const a = [1,2,3,4,5,6];
        const b = a.slice(-2)
        console.log(a) //打印[1,2,3,4,5,6]
        console.log(b) //打印[5,6]

        //当只传入一个参数，且是正数时，比length大时，会返回空数组
        const a = [1,2,3,4,5,6];
        const b = a.slice(10)
        console.log(a) //打印[1,2,3,4,5,6]
        console.log(b) //打印[]
    ```
<font color="red">*注：假如参数一正一负，也会先和length相加，再进行截取的操作</font>
- <font color="#08c" size=3>push()函数将元素添加进数组的尾部</font>

```js
    const a = [1,2,3]
    a.push(4)
    console.log(a) //打印[1,2,3,4]
```

- <font color="#08c" size=3>pop()函数删除数组的最后一个元素</font>

```js
    const a = [1,2,3]
    a.pop()
    console.log(a) //打印[1,2]
```

- <font color="#08c" size=3>unshift()函数将元素添加进数组的头部</font>

```js
    const a = [1,2,3]
    a.unshift(4)
    console.log(a) //打印[4,1,2,3]
```

- <font color="#08c" size=3>shift()函数删除数组的第一项元素</font>

```js
    const a = [1,2,3]
    a.shift()
    console.log(a) //打印[2,3]
```

- <font color="#08c" size=3>some()函数找寻数组是否拥有某一元素，有返回true、无则反之。(1.剩余的元素不会再执行检测；2.传回调函数)</font>

```js
    const a = [1,2,3]
    const b = a.some(i=>i===2)
    console.log(b) //打印true
```

- <font color="#08c" size=3>includes()函数找寻数组是否包含某一元素，有返回true、无则反之。(1.会全部执行完检测；2.传变量)</font>

```js
    const a = [1,2,3]
    const b = a.includes(1)
    console.log(b) //打印true
```

- <font color="#08c" size=3>reduce()迭代数组所有的项，然后构建一个最终的值返回。（第一个参数传回调函数，第二个参数传输出的变量类型）</font>

```js
    const a = ['J','a','v','a'];
    const b = a.reduce((pre,cur,index,array)=>{
      return pre+cur
    })
    console.log(a) //打印["J", "a", "v", "a"]
    console.log(b) //打印Java
    /*
    第一次执行，pre的初始值是空的，cur是数组下标0，它会执行数组长度-1的次数，
    第二次执行，pre为J，cur为a，
    第三次执行，pre为Ja（J加a的结果），cur为v（数组的第三项），
    依次类推，直到将数组的每一项都访问一遍，最后返回结果。
    */
```

<font color="red">（*注：整个循环执行的次数等于cur的console次数，pre的console次数-1)</font>
- <font color="#08c" size=3>reduceRight()函数：跟reduce()是一样的，从后往前遍历，正好与其相反</font>

```js
    const a = ['J','a','v','a'];
    const b = a.reduce((pre,cur,index,array)=>{
      return pre+cur
    })
    console.log(a) //打印["J", "a", "v", "a"]
    console.log(b) //打印avaJ
```

- every 与 some 相反，every 需要是数组中每一项都符合参数函数中的条件才返回 true，如果遇见任何一项不符合立即返回 false，也不再继续遍历后续项。

```js
    /*
        其实效果类似forEach循环
        arr.forEach(item=>{
        function(item){
            return item < 10
            }
        })
        就是数组每一元素都能到返回布尔值函数里面执行一遍，然后数组元素执行布尔值函数都是true则返回true，一项不符合就返回false
    */
    var arr = [1, 2, 3, 4, 5]
    var arr2 = arr.every(function (x) {
        return x < 10
    })
    console.log(arr2) //true
    var arr3 = arr.every(function (x) {
        return x < 3
    })
    console.log(arr3) // false
```

- some()和 includes()的区别

```js
    //every:一假即假
    //some:一真即真
    例：[1,2,3].some( i => i===2)
    例：[1,2,3].includes(2)
    两者都是返回true，some传的是回调函数，includes传的是变量
    例如：[1,2,3].some( i => i*2===2)可以加前缀处理

```

## 数组元素遍历过滤

- 方法一 forEach 循环遍历 replace 替换字符串 push 添加进去和 splice 再删除进行过滤（小白误区）

```js
    var data = ['wo100_100', 'zhangsan100_100', 'lisi100_100']
    //定义一个变量等于数组的长度
    var length = data.length
    //forEach遍历数组，replace检索到'100_100'替换成空，push添加进数组尾部下标
    //整句代码就是遍历数组data的每一项检索到100_100替换成空添加进data数组，注意这里data数组包含了未过滤和已过滤的元素
    data.forEach(item => {
        data.push(item.replace('100_100', ''))
    })
    //splice删除数组下标0开始到data原先数组的长度length
    data.splice(0, length) //输出data=['wo','zhangsan','lisi']
```

- 方法二 map 循环遍历 replace 替换字符串返回一个数组

```js
    //更加简洁优雅的方法，有助于提升技术
    const arr = ['111.jpg', '222.jpg', '333.jpg']
    const newArr = arr.map(item => {
    return item.replace('.jpg', '')
    })
    console.log(newArr) //newArr:['111','222','333']
```

- 方法三(通过 map 过滤数组对象，改变属性名)

```js
    const data = [
    { time: '2021-10-23', retData: '01OCT2021|@|29|@|2|@|2|@|0|@|0|@|0' },
    { time: '2021-10-22', retData: '01OCT2021|@|2|@|2|@|1|@|0|@|0|@|0' },
    { time: '2021-10-21', retData: '01OCT2021|@|40|@|40|@|40|@|0|@|0|@|0' },
    { time: '2021-10-06', retData: null },
    { time: '2021-10-05', retData: null },
    ]
    const newData = data.map(item => {
    const { time, retData } = item
    const progress = (retData ? retData.split('|@|').splice(2, 6) : [...new Array(5)]).map(
        (item, i) => ({
        task: `任务${i + 1}进度`,
        day: item || '-',
        })
    )
    return { time, progress }
    })
    console.log(newData)
```

- replace动态替换全局匹配

``` js
    let arr = ['hello','world','come']
    let string = 'helloworldcome'
    arr.forEach(item=>{
        var reg = new RegExp(item,"g");
        string = string.replace(reg, `-${item}`)
    })
```

## 数组去重

- 方法一 ES6 运算符和 new Set()去重（会新增一个新数组）

```js
    const a = [0, 1, 2, 3, 3, 1, 4, 5]
    const b = [...new Set(a)]
    console.log(b) //打印 [0, 1, 2, 3, 4, 5]
```

- <font>方法二 循环遍历 indeOf 找寻数组是否拥有了元素值（会新增一个新数组）</font>

```js
    const a = [1, 1, 2, 3, 4, 5, 5, 2]
    const b = new Array()
    for (index in a) {
    if (b.indexOf(a[index]) == -1) {
        b.push(a[index])
    }
    }
    console.log(b) //打印[1, 2, 3, 4, 5]
```

- <font>方法三 通过键值对的方式去重，数组的每一项作为对象的键名，键名具有唯一性</font>

```js
    const a = [1, 1, 2, 3, 4, 5, 5, 2]
    const obj = {}
    a.forEach(val => {
        obj[val] = 'haha'
    })
    const b = []
    for (let key in obj) {
        b.push(Number(key))
    }
    console.log(obj) //{1: "haha", 2: "haha", 3: "haha", 4: "haha", 5: "haha"}
    console.log(b) // 打印[1, 2, 3, 4, 5]
```

-  方法四 双重循环 splice 删除

```js
    const a = [1, 1, 2, 3, 4, 5, 5, 2, 3, 'ss', 'ss', 'ccsz', 'ss']
    for (let i = 0; i < a.length; i++) {
    for (let j = i + 1; j < a.length; j++) {
        if (a[i] === a[j]) {
        /*①
            a.splice(i, 1)
            i--
            保留的是数组后面出现的重复元素
        */
        /*②
            a.splice(j, 1)
            j--
            保留的是数组前面出现的重复元素
        */
        }
    }
    }
    console.log(a) // ① 打印[1, 4, 5, 2, 3, "ccsz", "ss"]   ② 打印[1, 2, 3, 4, 5, "ss", "ccsz"]

    /* 
        循环的时候它是接着往下的，你中间删除数组元素并不会改变它接着往下循环的，所以要让循环跳回上一层循环，因为a[i]或者a[j]已经是另外一项元素了。使用j和使用i，区别就在于删除重复的项是首项和尾项了
    */
```

- 方法五 reduce 数组去重

```js
    const names = [1, 1, 1, 1, 1, 1, 1, 2, 3]
    const nameNum = names.reduce((pre, cur) => {
    if (!pre.includes(cur)) {
        return pre.concat(cur)
    } else {
        return pre
    }
    }, [])
    console.log(nameNum) //打印[1, 2, 3]
    /*
        pre初始值是一个空数组，需要合并cur
    */

```

## 数组对比

- 两个数组对比，返回一个共同拥有元素值的数组

```js
    const Arr1 = ['AA', 'BB', 'CC', 'DD', 'EE']
    const Arr2 = ['AA', '124234', 'DD', '八月未央', 'abc']
    const res_Arr = Arr1.filter(val => {
        return Arr2.indexOf(val) > -1
    })
    console.log(res_Arr) // 打印['AA','DD']
    //filter会返回一个符合条件的数组，indexOf索引>-1作为判断条件
```

- 两个数组对比，去除两个数组里相等的值

```ts
    // 同上找出相同数组同样使用indexOf，只是判断条件改变
    const a = [1, 2, 3, 4, 5];
    const b = [3, 4, 5];
    a = a.filter(item => b.indexOf(item) == -1);
    console.log(a); // [1, 2]
```

```ts
    // 另一种实现
    const a = [1,2,3,4,5];
    const b = [3,4,5];
    const set = new Set(b);
    const filtered = a.filter(item => !set.has(item));
    console.log(filtered); // [1, 2]
```

<font>lodash库的without,pull,xor也能实现</font>

## 计算数组元素出现的次数(列举最装逼简洁写法)

```js
    const names = ['Alice', 'Bob', 'Tiff', 'Bruce', 'Alice']
    const nameNum = names.reduce((pre, cur) => {
        if (cur in pre) {
            pre[cur]++
        } else {
            pre[cur] = 1
        }
        return pre
        }, {}
    )
    console.log(nameNum) //打印{Alice: 2, Bob: 1, Tiff: 1, Bruce: 1}
```

<!-- more -->
