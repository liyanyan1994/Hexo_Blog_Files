---
title: hexo+github搭建属于自己的个人博客
date: 2018-06-22 15:25:56
categories:
- 工具
tags:
- Hexo 
- BlueLake主题
---

## 1. 前言
其实平时很少写文章，总觉得自己的水平还不够，又害怕误导别人。但是遇到问题又总想记录下来，毕竟自己工作了一段时间了。还是想自己搭建属于自己的博客，也算是一个温馨的小屋，以后就可以发一些自己总结的文章or遇到的坑or学习笔记。

## 2. 安装环境

1. 安装node.js
2. 安装Git
3. 有自己的github账号
4. vscode编辑器

作为一个程序员，这最基本的配置安装肯定是具备的（不会的可以网上搜，有很多教程）

## 3. 全局安装hexo,搭建基本的博客

1. 使用npm全局安装hexo

```
$ npm install -g hexo-cli

```

2. hexo-cli初始化

使用vscode打开一个文件夹位置,用来存放Blog文件夹，点开vscode的终端输入一下命令
```
$ npm init <folder>  
$ cd <folder>
$ npm install

```
folder是你的文件夹名称 如 npm init MyBlog

3. 查看默认的博客页面

继续在终端输入 

```
$ npm g
$ npm s

```
默认会 http://localhost:4000/ 点击就可以查看


## 4. hexo-cli基本文件夹目录介绍

```
.
├── _config.yml   # 个人网站的基本信息
├── package.json  # 依赖的npm包管理
├── scaffolds     # 模板文件夹
├── source        # 资源文件夹
|   ├── _drafts
|   └── _posts    # 默认新生成的页面在此文件夹下
└── themes        # 主题文件夹,默认下面是landscape主题
```

## 5. 自定义博客

### 5.1修改基本的配置信息

在_config.yml 根据个人的信息进行填写，详细的配置可以参考[Hexo基本配置](https://hexo.io/zh-cn/docs/configuration.html)

主要是填写site下的基本信息,以及要部署的github的repository

1. site配置
```
# Site
title: 博客名称
subtitle: 博客二级标题
description: 博客的描述,有助于引擎搜索
author: 博客作者
language: 语言
timezone: 时区

```

2. github部署配置

```
deploy:
  type: git  
  repository: github仓库地址 (如:git@github.com:liyanyan1994/liyanyan1994.github.io.git)
  message:
  branch: master
```

### 5.2下载喜欢的主题

hexo提供了很多的主题,详细的可以参考[hexo主题](https://hexo.io/themes/)进行筛选

这里根据个人喜好进行选择(个人选择的是BlueLake),终端命令输入:

#### 1.1安装主题

``` bash
$ git clone https://github.com/chaooo/hexo-theme-BlueLake.git themes/BlueLake

```

#### 1.2安装主题渲染器

BlueLake是基于`jade`和`stylus`写的，所以需要安装`hexo-renderer-jade`和`hexo-renderer-stylus`来渲染。
``` bash
$ npm install hexo-renderer-jade@0.3.0 --save
$ npm install hexo-renderer-stylus --save
```

#### 1.3 启用主题

打开根_config.yml配置文件，找到theme字段，将其值改为BlueLake(先确认主题文件夹名称是否为BlueLake)。
``` yml 根_config.yml https://hexo.io/zh-cn/docs/configuration.html_config.yml
theme: BlueLake
```

#### 1.4 本地启动服务器

``` bash git bash
$ hexo s
```

### 5.3自定义样式

在thems/BlueLake 修改layout和source的css进行样式修改,就可以得到自己的页面
最终的样子：
![page](https://i.loli.net/2018/06/22/5b2cba8d6c080.png)

## 6.常见的hexo命令

终端输入:

hexo generate(简写 hexo g) ---根据模板生成静态页面

hexo server(简写hexo s) ---本地启动服务器,查看静态页面的效果

hexo clean ---清除缓存文件 (db.json) 和已生成的静态文件 (public)

hexo init [layout] <title>--- 新建一篇文章，默认会在source/_posts生成一个名字是title的md文件，layout不填写会使用默认的布局

hexo g -d   ---生成文件并且部署到github上
