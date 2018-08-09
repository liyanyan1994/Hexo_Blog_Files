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
webpack.base.config.js中
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

### 1.4 在 main.js 中引入 icons

```javascript
import './icons' // icon
```

###1.5 在组件中直接使用<svg-icon>组件调用

```javascript
<svg-icon icon-class="password" />
```

## 2.通过参数 ID 修改 Url 复用同一个页面不进行数据刷新

同一个组件中，只是传递的 url 值如 Id 不相同，希望每次都能够请求后台获取数据

最开始是放在 created 中，发现根本不行

通过 watch 监听路由 from,to 筛选当前的路由进行操作

```javascript
watch: {
    $route(to, from) {
      // 筛选出当前路由name
      if (to.name === 'levelThree') {
        console.log(this.$route.query.device_id)
        this.deviceId = this.$route.query.device_id
        this.getDeviceInfo() // 请求后台数据
      }
    }
  },
```

## 3.vue 右键菜单栏

https://github.com/vuejs/awesome-vue#context-menu

https://github.com/xunleif2e/vue-context-menu

## 4.含图片的表单上传 formData
表单上传主要是利用 new FormData() 一个对象 然后使用append方法进行添加

```javascript
onchange(event){
  let file = event.target.files[0]
  let formdata = new FormData()
  formdata.append('file',file)
}
```

## 5.自定义弹出框样式

内部scope不生效 自定义一个class 然后用全局的样式去覆盖


## 6.时间问题

标准日期：2017-09-19 或 2017-09-19 20:00:00
中国标准时间：Mon Oct 23 2017 17:20:13 GMT+0800 (中国标准时间)
时间戳：1508750413
毫秒数：1508750413000 
注意：时间戳*1000就是毫秒数

https://blog.csdn.net/it429/article/details/78341847

结合elementUI的dateTime使用
new Date(val)
new Date().setHours(0,0,0) 得到凌晨时间戳
new Date().setHours(23,59,59) 最后一秒时间戳

当前时间戳: 毫秒级别
new Date().getTime()
Date.now()

## 7.图片上传预览功能

思路： 先用input[type=file]通过onchange事件来获取本地的图片，然后用HTML5的 File API 的 FileReader 图片本地转成base64格式的url，把这个url赋值到用于预览的src就好了。

```html
<img :src="preViewImgUrl"  width="160" height="120"/>
<input class="image-input" id="inputFile" accept="image/jpeg,image/jpg,image/png" type="file" @change="changeImg($event)" />
```

```javascript
   // 上传图片的onchange事件
    changeImg(e) {
      var imageFile = e.target.files[0]
      console.log('imageFile===', imageFile)
      if (!e || !window.FileReader) return // 看支持不支持FileReader
      let reader = new FileReader()
      reader.readAsDataURL(imageFile) // 这里是最关键的一步，转换就在这里
      let _this = this
      reader.onloadend = function() {
        console.log('reader.onloadend======')
        _this.preViewImgUrl = this.result
      }
      e.srcElement.value = '' // 清空防止上传图片时选择相同文件无法触发change事件
    }
```


## 8.element-ui 按需加载的时候v-loading不起作用 

vue.esm.js?efeb:591 [Vue warn]: Failed to resolve directive: loading


## 9.预加载的骨架屏幕  https://blog.csdn.net/w178191520/article/details/79134110

## 10.父组件异步获取的值传给子组件 采用v-if解决 mounted created中可以拿到
vue 父子组件数据同步

## 11.获取本地ip
location.hostname
location.port
window.location.href = url // 直接跳转链接

## 12. 打包后404问题
1.整个css404 修改配置文件的config/index.js build: {assetsPublicPath: './'}

2.图片的路径404 正常情况下引入图片会 background: url(assets/login-bg.jpg) no-repeat center center;
需要在build/utils.js中添加
```javascript
 if (options.extract) {
    return ExtractTextPlugin.extract({
      use: loaders,
      fallback: 'vue-style-loader',
      publicPath: '../../' //添加这句话解决图片打包404问题
    })
  } else {
    return ['vue-style-loader'].concat(loaders)
  }
```
3.去掉打包的sourceMap文件
在config/index.js中设置build下的productionSourceMap: false