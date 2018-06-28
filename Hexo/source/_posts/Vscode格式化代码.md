---
title: Vscode格式化代码
date: 2018-06-27 15:23:20
categories:
- 编辑器
tags:
- Vscode
- Elint
- Prettier
---

### 1.Alt+Shift+F格式化代码不成功

问题1. vue文件里面的template格式化不成功

    安装了vetur插件 =》 文件->首选项->设置->"vetur.format.defaultFormatter.html": "js-beautify-html" //格式化vue Template代码

问题2. vue文件里面的js格式化不成功
    "prettier.eslintIntegration": false  #之前设置成了true

