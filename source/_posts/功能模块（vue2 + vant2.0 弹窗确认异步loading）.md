---
title: vue2 + vant2.0 弹窗确认实现异步loading 组件
data: 2022-12-09 12:00
tag:
  - 功能模块
categories: 组件
---

业务组件: 查询到用户有待支付订单，弹窗（取消订单、继续支付）两个异步事件，实现弹窗按钮loading效果

<!-- more -->

1、取消按钮：
按钮loading —— 接口请求成功 —— 取消loading关闭弹窗
按钮loading —— 接口请求失败（网络等各种原因） —— 取消loading
按钮loading —— 接口请求业务异常 —— 取消loading关闭弹窗 执行业务异常动作

2、继续按钮：
按钮loading —— 接口请求成功 —— 取消loading关闭弹窗拉起对应支付动作
按钮loading —— 接口请求失败（网络等各种原因） —— 取消loading
按钮loading —— 接口请求业务异常 —— 取消loading关闭弹窗 执行业务异常动作

```html
<template>
  <div class="ensd-eplus__wait flex__column-center__center">
    <van-dialog
      v-model="show"
      title=""
      :show-cancel-button="false"
      :show-confirm-button="false"
    >
      <div class="flex__column-center__center ensd-eplus__wait-title">
        {{ title }}
      </div>
      <div class="flex__column-center__center ensd-eplus__wait-money">
        ￥{{ money }}
      </div>
      <div class="flex__column-center__center ensd-eplus__wait-vip">
        {{ sayText }}
      </div>
      <div class="flex__between">
        <van-button
          @click="cancelClick"
          :loading="cancelLoading"
          :disabled="disabled"
          class="flex__column-center__center ensd-eplus__wait-btn ensd-eplus__wait-left"
          >取消订单</van-button
        >
        <van-button
          @click="confirmClick"
          :disabled="disabled"
          :loading="confirmLoaidng"
          class="flex__column-center__center ensd-eplus__wait-btn ensd-eplus__wait-right"
          >继续支付</van-button
        >
      </div>
    </van-dialog>
  </div>
</template>
```

```js
import { Dialog, Button } from "vant";
export default {
  components: {
    [Dialog.Component.name]: Dialog.Component,
    [Button.name]: Button
  },
  props: {
    waitPayFlag: {
      type: Boolean,
      default: false
    },
    title: {
      type: String,
      default: "您有一笔待支付订单待处理"
    },
    money: {
      type: [String, Number],
      default: "22.99"
    },
    sayText: {
      type: String,
      default: "易plus会员 | 1个月"
    },
    confirmEvent: Function,
    cancelEvent: Function
  },
  data() {
    return {
      show: false,
      cancelLoading: false,
      confirmLoaidng: false,
      disabled: false
    };
  },
  watch: {
    waitPayFlag: {
      handler(val) {
        console.log(val, "====弹框展示===");
        this.show = val;
        this.cancelLoading = false;
        this.confirmLoaidng = false;
        this.disabled = false;
      },
      immediate: true
    }
  },
  model: {
    prop: "waitPayFlag",
    event: "change"
  },
  methods: {
    changeWaitPayFlag() {
      this.$emit("change", false);
    },
    async cancelClick() {
      try {
        this.cancelLoading = true;
        this.disabled = true;
        const flag = await this.cancelEvent();
        console.log("waitPayBox组件取消事件promise成功接收:", flag);
        /*
         这里有三种情况：
        1、接口异常失败、按钮loading、不关闭弹框
        2、接口返回订单已经不是待支付、按钮loading、关闭弹框
        3、接口请求成功、按钮loading、关闭弹框
        注：后端code码不规范、无法做到第二种、所以全部默认 （接口不论成功失败、loading按钮、关闭弹框）
        */
        if (flag) {
          this.disabled = false;
          this.cancelLoading = false;
          this.$emit("change", false);
        } else {
          this.disabled = false;
          this.cancelLoading = false;
        }
      } catch (error) {
        this.disabled = false;
        this.cancelLoading = false;
        this.$emit("change", false);
      }
    },
    async confirmClick() {
      try {
        this.confirmLoaidng = true;
        this.disabled = true;
        const flag = await this.confirmEvent();
        console.log("waitPayBox组件确认事件promise成功接收:", flag);
        if (flag) {
          this.disabled = false;
          this.confirmLoaidng = false;
          this.$emit("change", false);
        } else {
          this.disabled = false;
          this.confirmLoaidng = false;
        }
      } catch (error) {
        this.disabled = false;
        this.confirmLoaidng = false;
        this.$emit("change", false);
      }
    }
  }
};
```


```css
.ensd-eplus__wait {
  &-title {
    height: 24px;
    font-size: 16px;
    font-family: PingFangSC-Medium, PingFang SC;
    font-weight: 600;
    color: #333333;
    margin-top: 16px;
  }
  &-money {
    height: 36px;
    font-size: 24px;
    font-family: MiSans-Medium, MiSans;
    font-weight: 600;
    color: #333333;
    margin-top: 16px;
  }
  &-vip {
    height: 16px;
    font-size: 12px;
    font-family: PingFangSC-Regular, PingFang SC;
    font-weight: 400;
    color: #666666;
    margin-top: 8px;
    margin-bottom: 12px;
  }
  &-btn {
    width: 100%;
    height: 44px;
    border-radius: 0px 0px 0px 8px;
  }
  &-left {
    font-size: 16px;
    font-family: PingFangSC-Regular, PingFang SC;
    font-weight: 400;
    color: #666666;
  }
  &-right {
    font-size: 16px;
    font-family: PingFangSC-Regular, PingFang SC;
    font-weight: 400;
    color: #ff681c;
  }
}
```

<!-- more -->
