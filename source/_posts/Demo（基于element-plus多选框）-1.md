---
layout: post
title: Demo（基于element-plus多选框hook）
date: 2023-07-06 11:02:49
tags: Demo
---

vue3 + hook + element-plus 实现多选单选

<!-- more -->


## 实现hook封装，可略过不看

>checkedList: 选中项
plainOptions：供选择项
./useSave.ts

```ts
import { ref, watch } from 'vue'
import { isEmpty, map } from 'lodash'
import { storeToRefs } from 'pinia'
import { useMpptxStore, useUserInfoStore } from '@/store'
const useSave = () => {
  const saveDialogVisible = ref(false as boolean)
  const indeterminate = ref(true as boolean)
  const { checkedList } = storeToRefs(useMpptxStore())
  const { plainOptions } = storeToRefs(useUserInfoStore())
  const { getUserInfo } = useUserInfoStore()
  const checkAll = ref(false as boolean)
  const checkLoading = ref(false as boolean)
  const onCheckAllChange = (e: number[]) => {
    checkedList.value = e ? map(plainOptions.value, 'value') : []
    indeterminate.value = false
  }
	const onCheckMoreChange = (e: number[]) => {
		checkedList.value = [e[e.length - 1]]
    checkAll.value = e.length === plainOptions.value.length
    indeterminate.value = e.length > 0 && e.length < plainOptions.value.length
	}
  const onCheckChange = (e: number[]) => {
		// checkedList.value = [e[e.length - 1]] 单选
		// 默认是多选
    checkAll.value = e.length === plainOptions.value.length
    indeterminate.value = e.length > 0 && e.length < plainOptions.value.length
  }

  watch(() => saveDialogVisible.value, async () => {
    if (!saveDialogVisible.value) return
    if (plainOptions.value.length > 0) return
    await getUserInfo()
    if (plainOptions.value.length > 0) checkLoading.value = true
    if (!isEmpty(checkedList.value)) checkLoading.value = false
  })

  
  return {
    saveDialogVisible,
    plainOptions,
    indeterminate,
    checkedList,
    checkAll,
    checkLoading,
    onCheckChange,
		onCheckMoreChange,
    onCheckAllChange
  }
}

export default useSave
```

## 最基础使用 hook

>无法自由切换单选多选

```ts
import { ElCheckboxGroup, ElCheckbox } from 'element-plus'
```
```html
<div v-if="checkLoading">
	<div _flex="~ justify-between items-center" _m="t-20px b-10px">
		<span _text="16px [black] bolder">请选择</span>
		<ElCheckbox v-model="checkAll" :indeterminate="indeterminate" @change="onCheckAllChange">全选</ElCheckbox>
	</div>
	<ElCheckboxGroup v-model="checkedList" @change="onCheckMoreChange">
		<ElCheckbox v-for="item in plainOptions" :key="item.value" :label="item.value">{{ item.label }}</ElCheckbox>
	</ElCheckboxGroup>
</div>
```

## 搭配封装组件使用hook

```ts
import useSave from './hooks/useSave'
import { ElCheckboxGroup, ElCheckbox } from 'element-plus'
const {
  checkAll, 
  indeterminate,
  onCheckAllChange,
	onCheckMoreChange,
  onCheckChange,
  checkedList,
  plainOptions,
  saveDialogVisible,
  checkLoading
} = useSave()
const props = defineProps({
  tip: {
    type: String,
    required: true,
  },
	all: {
		type: Boolean,
		default: false
	}
})
```
```html
<div v-if="checkLoading">
	<div _flex="~ justify-between items-center" _m="t-20px b-10px">
		<span _text="16px [black] bolder">{{ tip }}</span>
		<ElCheckbox v-model="checkAll" :indeterminate="indeterminate" @change="onCheckAllChange" v-if="all">全选</ElCheckbox>
	</div>
	<ElCheckboxGroup v-model="checkedList" @change="all ? onCheckMoreChange() : onCheckChange()">
		<ElCheckbox v-for="item in plainOptions" :key="item.value" :label="item.value">{{ item.label }}</ElCheckbox>
	</ElCheckboxGroup>
</div>
```

<!-- more -->