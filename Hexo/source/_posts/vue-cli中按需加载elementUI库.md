---
title: vue-cli中按需加载elementUI库
date: 2018-06-20 20:13:20
updated: 2018-06-23 11:00
categories:
- Vue
tags:
- Vue
- Element-ui
---

## 前言

常常用 vue 开发一些管理系统，那么常用的 elementUI 库是大部分人的选择，不管三七二十一 npm install 一波,在 main.js 中直接一起引入，打包的时候才发现包太大，有些组件根本没有用到，于是我采取了按需引入的方法。

## 1. 安装 element-ui

```bash
npm i element-ui -S
```

## 2. 按需引入

### 1.1 安装 babel-plugin-component

```bash
npm install babel-plugin-component -D
```

### 1.2 修改.babelrc

在 plugins 中添加数组

```
"plugins":[
    "component",
        {
            "libraryName": "element-ui",
            "styleLibraryName": "theme-chalk"
        }
    ]
```

### 1.3 在 main.js 中引入所需要的组件，进行注册

```javascript
import { Button, Select } from 'element-ui'

const elementComponent = [Button, Select]

// 注册elementUI的每个按需引入的组件
elementComponent.forEach(item => {
  Vue.component(item.name, item)
})
```

### 1.4 关于 Message 等自动弹出问题

之前一直是按照全局加载的，修改成按需加载之后会出现 Comfirm 自动弹出,在 github 上可以看到问题
[按需引入 Message 等自动弹出问题](https://github.com/ElemeFE/element/issues/2024)

解决的方法是 1.把组件放在原型上

```javascript
const MsgBox = MessageBox
Vue.prototype.$confirm = MsgBox.confirm
Vue.prototype.$message = Message

# 和以前的操作一样
this.$confirm('tips Title', 'Confirm', {
  confirmButtonText: 'Yes',
  cancelButtonText: 'Cancel',
  type: 'warning'
}).then(() => {})
```

2.在单页面使用的时候直接引入组件

```javascript
import {MessageBox} from 'element-ui'

MessageBox({
   # options
})
```

### 1.5 国际化

国际化其实很简单，直接引入

```javascript
import lang from 'element-ui/lib/locale/lang/en'
import locale from 'element-ui/lib/locale'

// 设置语言
locale.use(lang)
```

默认给所有的组件设置一个默认的尺寸

```javascript
Vue.prototype.$ELEMENT = { size: 'small' }
```

那么同时设置尺寸和语言怎么设置

```javascript
Vue.use(ElementUI, { size: 'small', locale: en })
```

## 3. 使用命令行自定义主题色

### 1.1 全局安装主题生成工具

```git bash
npm i element-theme -g
```

### 1.2 初始化变量文件

命令行输入

```
et -i
```

会默认的输出一个 element-variables.scss 文件在最外层目录

### 1.3 修改 scss 的变量

打开文件 element-variables.scss,根据需要修改变量如:

```css
$--color-primary: #016d9c !default;
$--color-success: #36b3b3 !default;
$--color-warning: #e6a23c !default;
$--color-danger: #f56c6c !default;
$--color-info: #50bfff !default;
```

### 1.4 编译主题

命令行输入 et 就可以产生 theme 文件

因为是按需引入的，故修改.babelrc 的 styleLibraryName 如下：

```javascript
{
     "plugins": [["component", [
    {
      "libraryName": "element-ui",
      "styleLibraryName": "~theme"
    }
  ]]]
}
```
