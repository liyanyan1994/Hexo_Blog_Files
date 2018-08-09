---
title: Vue中使用Echarts
date: 2018-08-09 12:19:11
updated: 2018-08-09 12:19:11
categories:
- Echarts
tags:
- Echarts
- Vue
---

## 前言

大部分的后台系统都会有很多的数据，为了看得更直观，一般都会使用图表，如常见的 hightChart，chart,echarts，其中百度的 echarts 我认为是相对来说比较好的，个人一直在再使用，今天主要讲解在 vue 中使用 echarts

## 1.完整的引入 echarts

### 1.1 安装 echarts

npm install echarts --save

### 1.2 在组建中直接 import 使用

```html
<template>
    <div :class="className" :style="{height:height,width:width}"></div>
</template>
<script>
import echarts from 'echarts'
export default {
    props: {
        className: {
            type: String,
            default: 'chart',
        },
        width: {
            type: String,
            default: '200px'
        },
        height: {
            type: String,
            default: '200px'
        }
    },
    data() {
        return {
            chart: null
        }
    },
    mounted() {
        this.initChart()
    },
    beforeDestroy() {
        if (!this.chart) {
            return
        }
        this.chart.dispose()
        this.chart = null
    },
    methods: {
        initChart() {
            this.chart = echarts.init(this.$el)
            this.setOptions({
                '***各种options****'
            })
        }
    }
}
</script>
```
好处：直接引入整体的echarts,不需要单独引入，很简单
坏处：整个包的体积很大，打包的时候可以进行优化

### 1.3 单独的一个独立组件

大部分情况下，如果多个地方使用同一样的图表，就可以写成一个单独的组件进行多次复用，与上面大部分相同，只是需要传递一个props的原始数组，然后进行setOptions,值得注意的是，一般需要根据父组件的日期选择不同，chart的数据需要进行变化，当前使用的是watch某个props属性，然后进行相应的处理。

```javascript
export default{
    props: {
        orignChartData: {
            type: Array
        }
    },
    mounted() {
        if(this.orignChartData.length !== 0){
            this.initChart()
        }else {
            return null
        }
    },
    watch: {
        orignChartData: {
            deep: true,
            hanlder(val){
                if(val.length !== 0){
                    '这里可以算options里面的一些东西'
                    if(this.chart){
                        this.chart.clear()
                        this.settingOptions()
                    }else{
                        this.initChart()
                    }
                }else {
                    if(this.chart){
                        this.chart.clear()
                    }
                }
            }
        }
    },
    methods: {
        initChart(){
            this.chart = echarts.init(this.$el)
            this.settingOptions()
        },
        settingOptions(){
            '这里可以算options里面的一些东西'
            this.chart.setOption()
        }
    }
}
```

## 2.按需加载使用echart，减少体积

### 2.1新建一个'@/lib/echarts.js'
```javascript
import echarts from 'echarts/lib/echarts'
import 'echarts/lib/chart/bar'
import 'echarts/lib/component/legend'
import 'echarts/lib/component/title'
export default echarts

```
在组件中直接import echarts from '@/lib/echarts'

### 2.2.通过插件babel-plugin-enquire配合实现echarts按需引入

#### 下载babel-plugin-enquire插件 
  npm install babel-plugin-enquire -D
#### 修改.babelrc文件中的配置

```javascript
  "plugins": [
    "...其他插件",
    "enquire"
  ]
```
#### 在'@/lib/echarts.js'
```javascript
  const echarts = enquire([
    'bar',
    'legend',
    'title'
  ])
  export default echarts
```
### 2.3在组件中直接引入

```javascript
import Echart from '@/lib/echarts'
```


