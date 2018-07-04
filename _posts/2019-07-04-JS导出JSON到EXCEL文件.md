---
layout: post
title: JS导出JSON到EXCEL文件
tags: [nodejs, javascript, js, 前端]
---

> 书接上回, [JS读取本地EXCEL文件](http://www.qinluo1023.com/2018/07/03/JS%E8%AF%BB%E5%8F%96%E6%9C%AC%E5%9C%B0EXCEL%E6%96%87%E4%BB%B6.html)

xlsx组件几个重要组成对象和函数
1. workbook, 技术xlsx整个文件
2. worksheet, xlsx文件里的sheet,一个workbook由多个worksheet组成
3. XLSX.utils.json_to_sheet方法，将json数据转换成worksheet对象

例子：

```js

/* 需要导出的JSON数据 */
var data = [
    {"name":"John", "city": "Seattle"},
    {"name":"Mike", "city": "Los Angeles"},
    {"name":"Zach", "city": "New York"}
];

/* 如果没有导入xlsx组件则导入 */
if(typeof XLSX == 'undefined') XLSX = require('xlsx');

/* 创建worksheet */
var ws = XLSX.utils.json_to_sheet(data);

/* 新建空workbook，然后加入worksheet */
var wb = XLSX.utils.book_new();
XLSX.utils.book_append_sheet(wb, ws, "People");

/* 生成xlsx文件 */
XLSX.writeFile(wb, "sheetjs.xlsx");
```
[源代码](http://jsfiddle.net/true1023/yn15fbcL/2/)