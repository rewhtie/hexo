---
title: vue2 + vant2.0 标签页 + 折叠面板
date: 2023-02-07 14:04:02
tag:
  - 功能模块
---

vue2.0 + vant2.0(Tab, Tabs标签页、 Collapse, CollapseItem折叠面板) 实现标签页点击跳转到页面对应的位置，并打开折叠内容（手风琴效果），页面滚动、Tabs标签页会根据滚动位置进行横向滚动

<!-- more -->
<font color="#08c">原理：多个折叠面板，每一个tab标签都对应一个折叠面板，当点击tab1时打开对应1的折叠面板，关闭其他的折叠面板</font>

<font color="#08c">实现代码如下</font>

```html
<template>
  <div class="customer-tabs">
    <!-- v-model，是tabs对应的下标，刚进入页面就是停留在第一个tab -->
    <!-- scrollspy 页面滚动锚点 -->
    <!-- 官网链接https://vant-contrib.gitee.io/vant/v2/#/zh-CN/tab -->
    <van-tabs
      v-model="active"
      scrollspy
      sticky
      line-width="20"
      line-height="4"
      background="linear-gradient(270deg, #FFBB45 0%, #FF681C 100%);"
      offset-top="176"
    >
      <van-tab
        v-for="(item, index) in list"
        :title="item.tab"
        :name="item.id"
        :key="index"
      >
        <div>
          <div class="customer-tabs__context">{{ item.title }}</div>
          <!-- name=(v-model绑定的)item.active就会打开手风琴 -->
          <van-collapse v-model="item.active" accordion @change="onChange">
            <van-collapse-item
              v-for="(val, idx) in item.list"
              :title="`Q${idx + 1}：${val.title}`"
              :key="idx"
              :name="val.id"
              title-class="title"
              label-class="label"
              :border="false"
              >{{ val.context }}</van-collapse-item
            >
          </van-collapse>
        </div>
      </van-tab>
    </van-tabs>
  </div>
</template>
```

```js
<script>
export default {
  data() {
    return {
      active: "1",
      list: [
        {
          tab: "优惠券",
          id: "1",
          title: "优惠券相关问题",
          active: "1",
          list: [
            {
              title: "优惠券过期",
              id: "1-1",
              context:
                "用户在使用优惠券应同时满足以下条件：在我的优惠券中有可使用的优惠券；订单符合券面注明的使用条件（可用油站、可用商品、可用身份、满减条件、可使用时间）等。"
            },
            {
              title: "优惠券使用不了",
              context: "用户在使用优惠券应同时满足以下条件：",
              id: "1-2"
            },
            {
              title: "优惠券使用后没有信息提醒",
              context: "用户在使用优惠券应同时满足以下条件：",
              id: "1-3"
            },
            {
              title: "优惠券是否可以替换",
              context: "用户在使用优惠券应同时满足以下条件：",
              id: "1-4"
            },
            {
              title: "优惠券使用方法",
              context: "用户在使用优惠券应同时满足以下条件：",
              id: "1-5"
            }
          ]
        },
        {
          tab: "油价优惠",
          id: "2",
          title: "油价优惠相关问题",
          active: "2",
          list: [
            {
              title: "优惠价格不优惠",
              id: "2-1",
              context:
                "用户在使用优惠券应同时满足以下条件：在我的优惠券中有可使用的优惠券；订单符合券面注明的使用条件（可用油站、可用商品、可用身份、满减条件、可使用时间）等。"
            }
          ]
        },
        {
          tab: "退款相关",
          id: "3",
          title: "退款相关问题",
          active: "3",
          list: [
            {
              title: "优惠价格不优惠",
              id: "3-1",
              context:
                "用户在使用优惠券应同时满足以下条件：在我的优惠券中有可使用的优惠券；订单符合券面注明的使用条件（可用油站、可用商品、可用身份、满减条件、可使用时间）等。"
            }
          ]
        },
        {
          tab: "退款相关",
          id: "4",
          title: "退款相关问题",
          active: "4",
          list: [
            {
              title: "优惠价格不优惠",
              id: "4-1",
              context:
                "用户在使用优惠券应同时满足以下条件：在我的优惠券中有可使用的优惠券；订单符合券面注明的使用条件（可用油站、可用商品、可用身份、满减条件、可使用时间）等。"
            }
          ]
        },
        {
          tab: "退款相关",
          id: "5",
          title: "退款相关问题",
          active: "5",
          list: [
            {
              title: "优惠价格不优惠",
              id: "5-1",
              context:
                "用户在使用优惠券应同时满足以下条件：在我的优惠券中有可使用的优惠券；订单符合券面注明的使用条件（可用油站、可用商品、可用身份、满减条件、可使用时间）等。"
            }
          ]
        }
      ]
    };
  },
  methods: {
    onChange(val) {
      this.list.forEach((item) => {
        if (val.split("-")[0] !== item.id) {
          item.active = item.id;
        } else {
          this.active = val.split("-")[0];
        }
      });
    }
  }
};
</script>
```

```css
<style lang="less" scoped>
.customer-tabs {
  flex: 1;
  overflow-y: auto;
  &__context {
    width: 375px;
    height: 46px;
    font-size: 14px;
    font-family: PingFangSC-Medium, PingFang SC;
    font-weight: 600;
    color: #333333;
    background: #ffffff;
    padding: 12px;
    margin-top: 4px;
  }
}

/deep/ .title {
  font-size: 12px;
  font-family: PingFangSC-Medium, PingFang SC;
  font-weight: 600;
  color: #333333;
}
/deep/ .label {
  font-size: 12px;
  font-family: PingFangSC-Regular, PingFang SC;
  font-weight: 400;
  color: #666666;
}
</style>
```
<!-- more -->