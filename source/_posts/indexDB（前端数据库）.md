---
layout: post
title: indexDB（前端数据库）
date: 2023-07-18 08:43:15
tags: 数据库
categories: 数据库
---

[indexDB API参考官网](https://dexie.org/docs/API-Reference) 

<!-- more -->

### 基础使用

#### 创建数据库新建表

```ts
const db = new Dexie("MyDatabase");
db.version(1).stores({
	friends: "++id",
	gameSessions: "id"
});
```

#### 数据库查询

查询对应name值为'david'的所有数据
```ts
const friends = await db.friends.where("name").equalsIgnoreCase("david").toArray();
for (const friend of friends) {
  console.log("Found: " + friend.name + ". Phone: " + friend.phoneNumber);
}
```
查询对应name值为'david'，且对应age值在23-43之间符合条件的所有数据
```ts
const davids = await db.friends.where(["name", "age"])
  .between(["David", 23], ["David", 43], true, true)
  .toArray();
for (const david of davids) {
  console.log(`Found a David with age ${david.age}`);
}
```
表添加数据（object为对象）
```ts
db.friends.add(object)
```
表数据更新（key：表key值；object：表value值）
```ts
db.friends.update(key | object, changeObject)
```
得到表的所有key值，查出数据库总共有几条（数组length）
```ts
db.friends.keys()
```

```ts
where // 查表头id、name、age之类
equals // 查索引，表头id列的key
equalsIgnoreCase // 查值
keys // 获取表所有key值，得到数据库数据总数
```

### dexie.js实现二次封装实例代码

```ts
import Dexie from 'dexie'
import { databaseId } from '@/store/main'
import { Slide } from '@/types/slides'
import { LOCALSTORAGE_KEY_DISCARDED_DB } from '@/configs/storage'

export interface writingBoardImg {
  id: string | number
  dataURL: Array<string>,
}

export interface Snapshot {
  index: number
  slides: Slide[]
}

const databaseNamePrefix = 'PPTist'

// 删除失效/过期的数据库
// 应用关闭时（关闭或刷新浏览器），会将其数据库ID记录在 localStorage 中，表示该ID指向的数据库已失效
// 当应用初始化时，检查当前所有数据库，将被记录失效的数据库删除
// 另外，距离初始化时间超过12小时的数据库也将被删除（这是为了防止出现因意外未被正确删除的库）
export const deleteDiscardedDB = async () => {
  const now = new Date().getTime()

  const localStorageDiscardedDB = localStorage.getItem(LOCALSTORAGE_KEY_DISCARDED_DB)
  const localStorageDiscardedDBList: string[] = localStorageDiscardedDB ? JSON.parse(localStorageDiscardedDB) : []

  const databaseNames = await Dexie.getDatabaseNames()
  const discardedDBNames = databaseNames.filter(name => {
    if (name.indexOf(databaseNamePrefix) === -1) return false
    
    const [prefix, id, time] = name.split('_')
    if (prefix !== databaseNamePrefix || !id || !time) return true
    if (localStorageDiscardedDBList.includes(id)) return true
    if (now - (+time) >= 1000 * 60 * 60 * 12) return true

    return false
  })

  for (const name of discardedDBNames) Dexie.delete(name)
  localStorage.removeItem(LOCALSTORAGE_KEY_DISCARDED_DB)
}

// 新建PPTistDB数据库 - 数据下新建表名为‘snapshots’、‘writingBoardImgs’表 - key值为id
class PPTistDB extends Dexie {
  public snapshots: Dexie.Table<Snapshot, number>
  public writingBoardImgs: Dexie.Table<writingBoardImg, number>

  public constructor() {
    super(`${databaseNamePrefix}_${databaseId}_${new Date().getTime()}`)
    // 创建两张表
    this.version(1).stores({
      snapshots: '++id',
      writingBoardImgs: '++id',
    })
    this.snapshots = this.table('snapshots')
    this.writingBoardImgs = this.table('writingBoardImgs')
  }
}

export const db = new PPTistDB()
```
<!-- more -->