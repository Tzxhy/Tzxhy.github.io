---
title: js小工具
date: 2016-09-06 16:03:42
tags:
---

小工具一:先收藏以下代码为书签. 在网页中选取了文本后, 若需要查询, 则可直接点击该书签
``` bash
javascript: 
var q;
if(window.getSelection) q = window.getSelection().toString();
else if(document.selection) q = document.selection.createRange().text;
void window.open('http://baidu.com/s?wd='+q);

```


