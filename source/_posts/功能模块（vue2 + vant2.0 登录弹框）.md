---
title: vue2 + vant2.0登录功能模块
tag:
  - 功能模块
---

vue2.0 + vant2.0(button按钮、Totast轻提示) 实现登录获取验证码, 验证码60s倒计时，手机合规校验，验证码校验

<!-- more -->
```html
<template>
  <div
    class="ensd__login flex__center__center"
    @click="show = false"
    v-if="show"
  >
    <div class="ensd__login__content flex__column-center__center" @click.stop>
      <div class="ensd__login__title">登 录</div>
      <div class="ensd__login__box">
        <input
          v-model="phone"
          pattern="[0-9]*"
          maxlength="11"
          type="tell"
          placeholder="请输入手机号码"
          :class="[
            'ensd__login__input',
            focus === 'phone' && 'ensd__login__input2'
          ]"
          @focus="focus = 'phone'"
        />
        <div class="ensd__login__icon" @click="phone = ''">
          <ensdIconsvg name="clear" size="14" />
        </div>
      </div>
      <div class="ensd__login__box">
        <input
          v-model="code"
          pattern="[0-9]*"
          maxlength="6"
          type="tell"
          placeholder="请输入验证码"
          :class="[
            'ensd__login__input',
            focus === 'code' && 'ensd__login__input2'
          ]"
          @focus="focus = 'code'"
        />
        <div
          :class="[
            'ensd__login__code',
            'flex__center__center',
            code_btn && 'ensd__login__code2'
          ]"
          @click="getPhoneCode"
        >
          {{ code_text }}
        </div>
      </div>
      <div class="ensd__login__line"></div>

      <van-button
        :loading="loading"
        :class="[
          'ensd__login__btn',
          'flex__center__center',
          login_btn && 'ensd__login__btn2'
        ]"
        loading-type="spinner"
        @click="login"
        >登录</van-button
      >
    </div>
  </div>
</template>
```

```js
import ensdIconsvg from "@/components/iconsvg";
import api from "@/api";
import { Toast } from "vant";
import { Button } from "vant";
import { osType } from "@/utils/index";
import { localStorage } from "@/utils/localStorage";

export default {
  components: {
    ensdIconsvg,
    [Button.name]: Button
  },
  watch: {
    phone(newVal) {
      if (newVal.length == 11) {
        if (!/^[1][3,4,5,6,7,8,9][0-9]{9}$/.test(newVal)) {
          Toast("手机号错误");
        }
      }
    },
    code(newVal) {
      if (newVal.length == 6) {
        if (!/^[0-9]*$/.test(newVal)) {
          Toast("验证码错误");
        }
      }
    }
  },
  data() {
    return {
      show: false,
      phone: "",
      code: "",
      count: 60,
      timer: null,
      flag: false,
      loading: false,
      focus: "",
      next: () => {}
    };
  },
  methods: {
    // 获取验证码
    async getPhoneCode() {
      if (this.code_btn) {
        const { code } = await api.login.getNoteForLogin({
          phone: this.phone
        });
        if (code === 200) {
          this.flag = true;
          this.timer = setInterval(() => {
            this.count--;
            if (this.count == 0) {
              this.flag = false;
              clearInterval(this.timer);
              this.count = 60;
            }
          }, 1000);
        }
      }
    },
    // 登录按钮
    async login() {
      if (this.login_btn) {
        this.loading = true;
        try {
          const { data } = await apiFn({
            phone: this.phone,
            smsCode: this.code,
          });
          this.loading = false;
          this.show = false;
          localStorage.set("userInfo", data);
          this.next();
        } catch (err) {
          this.loading = false;
        }
      }
    },
    // 外部调用open方法传入箭头函数，实现登录之后的操作，默认是刷新页面，可以在指定的路由路径做对应的相关操作（作为回调函数传入）
    open(next = () => {}) {
      this.show = true;
      this.next = next;
    }
  },
  computed: {
    // 获取是否禁用
    code_btn() {
      return /^[1][3,4,5,6,7,8,9][0-9]{9}$/.test(this.phone) &&
        !this.flag &&
        !this.loading
        ? true
        : false;
    },
    // 获取按钮文本
    code_text() {
      return this.flag ? `重新发送(${this.count})` : `获取验证码`;
    },
    // 登录是否禁用
    login_btn() {
      return /^[1][3,4,5,6,7,8,9][0-9]{9}$/.test(this.phone) &&
        /^[0-9]*$/.test(this.code) &&
        this.code.length === 6
        ? true
        : false;
    },
    // 登录按钮文字
    login_text() {
      return "登录";
    }
  }
};
```

```css
::-webkit-input-placeholder {
  font-size: 14px;
  font-family: PingFangSC-Regular, PingFang SC;
  font-weight: 400;
  color: #cccccc;
}
.ensd__login {
  position: fixed;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
  margin: auto;
  background-color: rgba(0, 0, 0, 0.7);
  z-index: 999;
  &__content {
    background-color: #fff;
    width: 320px;
    border-radius: 12px;
    padding: 16px 0;
  }
  &__title {
    height: 22px;
    font-size: 18px;
    font-family: PingFangSC-Medium, PingFang SC;
    font-weight: 600;
    color: #333333;
    line-height: 22px;
    text-align: center;
    margin-bottom: 16px;
  }
  &__box {
    position: relative;
    margin-bottom: 12px;
  }
  &__icon {
    position: absolute;
    right: 12px;
    top: 50%;
    transform: translateY(-50%);
  }
  &__code {
    position: absolute;
    width: 87px;
    right: 0;
    top: 50%;
    transform: translateY(-50%);
    width: 87px;
    height: 20px;
    font-size: 11px;
    font-family: PingFangSC-Regular, PingFang SC;
    font-weight: 400;
    color: #cccccc;
    line-height: 14px;
    border-left: 1px solid #dcdee0;
  }
  &__code2 {
    color: #337aff;
  }
  &__input {
    width: 271px;
    height: 50px;
    border-radius: 12px;
    border: 1px solid #dcdee0;
    padding-left: 12px;
    font-size: 14px;
    font-family: PingFangSC-Regular, PingFang SC;
    font-weight: 400;
    color: #333333;
  }
  &__input2 {
    border: 1px #ff681c solid;
  }
  &__line {
    width: 271px;
    height: 1px;
    box-shadow: inset 0px 1px 0px 0px #f2f5f7;
    margin-top: 4px;
    margin-bottom: 16px;
  }
  &__btn {
    width: 271px;
    height: 44px;
    background: linear-gradient(90deg, #ff8e56 0%, #ff6034 100%);
    border-radius: 22px;
    opacity: 0.3;
    font-size: 14px;
    font-family: PingFangSC-Medium, PingFang SC;
    font-weight: 500;
    color: #ffffff;
  }
  &__btn2 {
    opacity: 1;
  }
}
```

<!-- more -->
