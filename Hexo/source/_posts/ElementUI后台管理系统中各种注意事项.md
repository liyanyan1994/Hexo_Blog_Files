---
title: ElementUI后台管理系统中各种注意事项
date: 2018-06-28 20:11:11
updated: 2018-06-28 20:11:11
categories:
- Vue
tags:
- Vue
- Element-ui
---

## 1.使用 svg 组件进行图标引用

以前图片一般用 img 来实现,后来因为请求资源太多进行优化，使用雪碧图，但雪碧图修改起来不方便，
就使用 font 库，font 的资源图标有限，找图难受
使用阿里开源库 iconfont,各种小图标，自定义图标,

使用 symbol 姿势

### 1.1 创建 icon-component 组件

```javascript
<template>
  <svg :class="svgClass" aria-hidden="true">
    <use :xlink:href="iconName"></use>
  </svg>
</template>

<script>
export default {
  name: 'svg-icon',
  props: {
    iconClass: {
      type: String,
      required: true
    },
    className: {
      type: String
    }
  },
  computed: {
    iconName() {
      return `#icon-${this.iconClass}`
    },
    svgClass() {
      if (this.className) {
        return 'svg-icon ' + this.className
      } else {
        return 'svg-icon'
      }
    }
  }
}
</script>

<style scoped>
.svg-icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
</style>
```

### 1.2 使用 svg-sprite-loader 将 svg 打包成 svg-sprite

默认的 vue-cli 对 svg 有处理，为了防止冲突，采用 webpack 的 exclude,include 来处理，代码如下：

```javascript
{
    test: /\.svg$/,
    loader: 'svg-sprite-loader',
    include:[resolve('src/icons')],
    options: {
        symbolId: 'icon-[name]'
    }
},
{
    test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
    loader: 'url-loader',
    exclude:[resolve('src/icons')],
    options: {
        limit: 10000,
        name: utils.assetsPath('img/[name].[hash:7].[ext]')
    }
},
```

### 1.3 建立@/src/icons/index.js 自动导入图标

新建 icons 文件夹下面 svg 文件夹拿来放 svg 格式的图片 index.js 代码如下

```javascript
import Vue from 'vue'
import SvgIcon from '@/components/SvgIcon'

// 全局注册icon-svg
Vue.component('svg-icon', SvgIcon)

const requireAll = requireContext => requireContext.keys().map(requireContext)
const req = require.context('./svg', false, /\.svg$/)
requireAll(req)
```

### 1.4 在main.js中引入icons
```javascript
import './icons' // icon
```

###1.5 在组件中直接使用<svg-icon>组件调用

```javascript
<svg-icon icon-class="password"></svg-icon>
```