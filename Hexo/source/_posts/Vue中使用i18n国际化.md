---
title: Vue中使用i18n国际化
date: 2018-08-09 10:28:11
updated: 2018-08-09 10:28:11
categories:
- i18n
tags:
- Element-ui
- Vue
---

## 前言

我司主要是针对印尼客户，做的系统每次都要翻译成英文，但是公司内部员工是需要使用中文，就想着需要国际化一下，这样中英文切换就很方便，而且两者都可以使用,系统是基于vue、elementUI的，就查了资料更改。

## 1. 安装 vue-i18n

npm install vue-i18n --save

## 2. 新建文件'@/lang/index.js'

该文件主要是导出一个国际化的实例，里面有本地翻译的信息

```javascript
import Vue from 'vue'
import VueI18n from 'vue-i18n'
import Cookies from 'js-cookie'
import elementEnLocale from 'element-ui/lib/locale/lang/en' // element-ui lang
import elementZhLocale from 'element-ui/lib/locale/lang/zh-CN' // element-ui lang
import enLocale from './en'
import zhLocale from './zh'

Vue.use(VueI18n)

const messages = {
  en: {
    ...enLocale,
    ...elementEnLocale
  },
  zh: {
    ...zhLocale,
    ...elementZhLocale
  }
}
// 创建带有选项的 VueI18n 实例
const i18n = new VueI18n({
  locale: Cookies.get('language') || 'en', // set locale
  messages // set locale messages
})

export default i18n

```
使用的是ElementUI库 elementEnLocale，elementZhLocale都是element的语言切换
enLocale，zhLocale是我本地的语言翻译,在相同的目录下建en.js zh.js

```javascript
// en.js
export default {
  inputplace: 'please input'
  title: {
    headers: 'LaiDian Service Background System'
  }
}
// zh.js
export default {
  inputplace: '请输入'
  title: {
    headers: '来电后台管理系统'
  }
}
```

## 3. 把i18n挂载到Vue实例上

在main.js中引入lang/index.js
```javascript
import Vue from 'vue'
import App from './App'
import router from './router'
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
import * as filters from './filters' // global filters
import './icons' // icon
import store from './store'
import i18n from './lang' // 国际化

Vue.use(ElementUI, {
  size: 'small',
  i18n: (key, value) => i18n.t(key, value)
})

// 注册全局filters
Object.keys(filters).forEach(key => {
  Vue.filter(key, filters[key])
})
Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  store,
  i18n,
  components: { App },
  template: '<App/>'
})

```

## 4. 在html模板中使用国际化
```html
<template>
  <div class="title">
        {{$t('title.headers')}}
   </div>
   <el-input :placeholder="$t('inputplace')"></el-input>
</template>
```

## 5.页面切换语言
这里切换预言是用的vuex的状态管理，给定一个state:language，然后交互的时候进行分发提交，修改本地的i18n语言设置

```html
<template>
    <el-col :span="12" style="text-align: right;">
        <el-dropdown trigger="click" class='international' @command="handleSetLanguage">
          <span class="el-dropdown-link">
            {{language==='zh' ? '中文': 'English'}}
            <i class="el-icon-arrow-down el-icon--right"></i>
          </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item command="zh" :disabled="language==='zh'">中文</el-dropdown-item>
            <el-dropdown-item command="en" :disabled="language==='en'">English</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
    </el-col>
</template>
<script>
export default {
  name: 'App',
  computed: {
    language () {
      return this.$store.getters.language
    }
  },
  methods: {
    handleSetLanguage (lang) {
      this.$i18n.locale = lang // 这句是重点
      this.$store.commit('SET_LANGUAGE', lang)
      this.$message({
        message: 'switch language success',
        type: 'success'
      })
    }
  }
}
</script>
```
还有就是状态管理的store,主要是让界面响应式变化

```javascript
import Vue from 'vue'
import Vuex from 'vuex'
import Cookies from 'js-cookie'

Vue.use(Vuex)
const store = new Vuex.Store({
  state: {
    language: 'en'
  },
  mutations: {
    SET_LANGUAGE: (state, language) => {
      state.language = language
      Cookies.set('language', language)
      console.log('SET_LANGUAGE==', Cookies.get('language'))
    }
  },
  getters: {
    language: state => state.language
  }
})

export default store

```