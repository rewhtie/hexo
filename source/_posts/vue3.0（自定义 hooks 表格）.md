---
title: vue3.0自定义表格hooks
data: 2022-08-14 17:00
tags:
  - 全局函数
  - vue3.0
---

表格组件，搜索的时候都需要赋值然后调用接口，一旦表格多了，写太多重复代码，直接写一套公用的方法暴露出去，避免代码重复

<!-- more -->


 validate文件
```ts
// 格式化参数,判断值为空时,把这一项删除
export const formatParams = params => {
  const obj = {}
  for (let key in params) {
    // isEmpty是判空的方法
    if (!isEmpty(params[key])) {
      obj[key] = params[key]
    }
  }
  return obj
}
```

 useList文件
```ts
import { ref, reactive } from 'vue'
import { formatParams } from '@/utils/validate'
// 分页器
export interface Pagination {
  total: number
  current: number
  pageSize: number
  showSizeChanger: boolean
  showQuickJumper: boolean
  showTotal: (total: number, range: number) => string
  onChange: (page: number) => void
  onShowSizeChange: (current: number, size: number) => void
}

export type UseList = (api: any, params: any, pageType?: any, removerLsit?: any[]) => any

/**
 * 获取列表数据
 * @param {api} queryListApi 接口
 * @param {object} params 接口参数
 * @param {Array} removerLsit 去除不传后台的字段
 * @returns {list, loading, pagination, getList, formRef, handleReset, handleCurrentChange, handleSizeChange}
 */
// 去除 属性 Remover
const pageTypeObj = {
  pageNum: 'pageNum',
  pageSize: 'pageSize',
  total: 'total',
}
const useList: UseList = (queryListApi, params, pageType = { ...pageTypeObj }, removerLsit) => {
  // 不传分页类型且要传去除字段就做数据转移
  // Array.isArray()判断是否为数组
  if (pageType && Array.isArray(pageType)) {
    removerLsit = pageType
    pageType = { ...pageTypeObj }
  }

  // 分页
  const pagination = reactive<Pagination>({
    // 数据总数
    total: 0,
    // 当前页数
    current: 1,
    // 每页条数
    pageSize: 10,
    // 是否可以改变 pageSize
    showSizeChanger: true,
    // 是否可以快速跳转至某页
    showQuickJumper: true,
    // 页码或 pageSize 改变的回调，参数是改变后的页码及每页条数
    showTotal: total => `共${total}条`,
    // pageSize 变化的回调
    onChange: page => handleCurrentChange(page),
    onShowSizeChange: (current, size) => handleSizeChange(size),
  })

  // 获取列表数据
  const loading = ref<boolean>(false)
  const dataObj = reactive<{ data: object }>({ data: {} })
  const getList = async (): Promise<void> => {
    loading.value = true
    const _params = JSON.parse(JSON.stringify(params))
    if (removerLsit && removerLsit.length) {
      removerLsit.forEach(item => {
        let list = item.split('.')
        if (list.length == 2) {
          delete _params[list[0]][list[1]]
        } else {
          delete _params[item]
        }
        list = null
      })
    }
    try {
      const filterParams = formatParams(_params)
      const { code, data } = await queryListApi(filterParams)
      if (code === 200) {
        dataObj.data = Array.isArray(data) ? { list: data } : data
        pagination.total = data.total
      }
    } catch (error) {
      console.log(error)
    }
    loading.value = false
  }

  // 切换分页
  const handleCurrentChange = page => {
    pagination.current = params[pageType.pageNum] = page
    getList()
  }

  // 切换每页几条
  const handleSizeChange = size => {
    pagination.current = params[pageType.pageNum] = 1
    pagination.pageSize = params[pageType.pageSize] = size
    getList()
  }

  // 排序
  let order = ''
  let columnKey = ''
  const tableChange = (args, orderType) => {
    const sorter = args[2]
    if (sorter.columnKey === undefined || (order == sorter.order && columnKey === sorter.columnKey))
      return
    order = sorter.order
    columnKey = sorter.columnKey
    params.sortWay = order === 'descend' ? 1 : 2
    params.sortType = orderType[columnKey] || 1
    getList()
  }

  // 重置
  const formRef = ref()
  const handleReset = () => {
    formRef.value?.resetFields()
    handleCurrentChange(1)
  }

  return {
    dataObj,
    loading,
    pagination,
    getList,
    formRef,
    handleReset,
    handleCurrentChange,
    handleSizeChange,
    tableChange,
  }
}

export default useList
```

 使用示例
```vue
<template>
    <a-form class="search" ref="formRef" :model="params" :label-col="{ span: 4 }">
        <a-row>
            <a-col :span="3">
            <a-form-item name="startTime">
                <a-form-item>
                <a-date-picker
                    v-model:value="params.startTime"
                    show-time
                    type="date"
                    valueFormat="YYYY-MM-DD HH:mm:ss"
                    format="YYYY-MM-DD HH:mm:ss"
                    placeholder="请输入开始时间" /></a-fo rm-item
            ></a-form-item>
            </a-col>
            <a-col :span="3">
            <a-form-item name="endTime">
                <a-form-item>
                <a-date-picker
                    v-model:value="params.endTime"
                    show-time
                    type="date"
                    valueFormat="YYYY-MM-DD HH:mm:ss"
                    format="YYYY-MM-DD HH:mm:ss"
                    placeholder="请输入截至时间"
                />
                </a-form-item>
            </a-form-item>
            </a-col>
            <a-col :span="3">
            <a-form-item name="impactCategory">
                <a-form-item>
                <a-select
                    allowClear
                    v-model:value="params.impactCategory"
                    placeholder="请选择影响类别"
                    @change="selectChange"
                >
                    <a-select-option
                    v-for="item in impactCategoryList"
                    :value="item.label"
                    :key="item.value"
                    >
                    {{ item.label }}
                    </a-select-option>
                </a-select>
                </a-form-item>
            </a-form-item>
            </a-col>
            <a-col :span="3">
            <a-form-item name="influenceFactor">
                <a-form-item>
                <a-select
                    allowClear
                    v-model:value="params.influenceFactor"
                    placeholder="请选择影响因素"
                >
                    <a-select-option
                    v-for="item in influenceFactorListCheck"
                    :value="item.label"
                    :key="item.label"
                    >
                    {{ item.label }}
                    </a-select-option>
                </a-select>
                </a-form-item>
            </a-form-item>
            </a-col>
            <a-col :span="3">
            <a-form-item name="operator">
                <a-form-item>
                <a-input  v-model:value="params.operator" placeholder="请选择操作人" />
                </a-form-item>
            </a-form-item>
            </a-col>
            <a-col :span="4" class="topButton">
            <a-button type="primary" size="medium" @click="getList">查询</a-button>
            <a-button size="medium" @click="handleReset">重置</a-button>
            </a-col>
        </a-row>
    </a-form>
    <a-table
        row-key="id"
        :columns="operationLogColumns"
        :data-source="dataObj.data.records || []"
        :loading="loading"
        size="small"
        :pagination="{
        ...pagination,
        onChange: (page) => handleCurrentChange(page),
        onShowSizeChange: (current, size) => handleSizeChange(size),
        }"
    >
    </a-table>
</template>

<script lang="ts">
  import { defineComponent, ref, reactive, onMounted, toRefs, UnwrapRef } from "vue";
  // 表格的设置取值
  import { operationLogColumns } from "@/views/configManage/data";
  // 接口
  import { getInfo } from "../service";
  // 引入封装好的一套方法
  import useList from "@/hooks/useList";
  // 这些是数据,可以封装放在其他ts文件,也可以放在页面
  import {
    impactCategoryList,
    impactCategory,
    influenceFactor,
    operatorList,
    operator,
    influenceFactorListChange
  } from "@/utils/filter";

  export default defineComponent({
    name: "operationLog",
    setup() {
      // 接口请求参数
      const params = reactive({
        startTime: undefined,
        endTime: undefined,
        impactCategory: undefined,
        influenceFactor: undefined,
        operator: undefined,
        pageNum: 1, // 当前页码1
        pageSize: 10, // 页容量
      });
      // 一二级下拉框对应出现值的处理
      const influenceFactorListCheck: any = ref([])
      const selectChange = (val, option) => {
        influenceFactorListCheck.value = influenceFactorListChange(option?.key || null)
      };
      // 解构赋值获取封装的方法,接口请求的数据
      const {
        loading,
        dataObj,
        pagination,
        getList,
        formRef,
        handleReset,
        handleCurrentChange,
        handleSizeChange,
      } = useList(getInfo, params, {
        pageNum: "pageNum",
        pageSize: "pageSize",
        total: "totalCount",
      });
      onMounted(()=>{
        getList()
      });
      return {
        params,
        operationLogColumns,
        loading,
        dataObj,
        pagination,
        getList,
        formRef,
        handleReset,
        handleCurrentChange,
        handleSizeChange,
        impactCategoryList,
        impactCategory,
        influenceFactorListChange,
        influenceFactor,
        operatorList,
        operator,
        selectChange,
        influenceFactorListCheck
      };
    },
  });
</script>
```


<!-- more -->
