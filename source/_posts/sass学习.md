---
title: sass学习
date: 2016-10-22 19:47:36
tags:
---

# sass
## 编译风格
1, --style nested
``` bash
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }

  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}
```
2, sass --watch test.scss:test.css --style expanded
``` bash
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }

  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}
```
3, sass --watch test.scss:test.css --style compact
4, sass --watch test.scss:test.css --style compressed

Sass 调试: sass --watch --scss --sourcemap style.scss:style.css.在 Sass3.3 版本之上（我测试使用的版本是 3.4.7），不需要添加这个参数也可以

## 基础
### [Sass]声明变量
声明变量的符号“$”
变量名称
赋予变量的值
``` bash
$brand-primary : darken(#428bca, 6.5%) !default; // #337ab7
$btn-primary-color : #fff !default;
$btn-primary-bg : $brand-primary !default;
$btn-primary-border : darken($btn-primary-bg, 5%) !default;
```
如果值后面加上!default则表示默认值。
### [Sass]普通变量与默认变量
普通变量：定义之后可以在全局范围内使用。
``` bash
$fontSize: 12px;
body{
	font-size:$fontSize;
}
```
默认变量：sass 的默认变量仅需要在值后面加上 !default 即可。
``` bash
$baseLineHeight:1.5 !default;
body{
	line-height: $baseLineHeight; 
}
```
sass 的默认变量一般是用来设置默认值，然后根据需求来覆盖的，覆盖的方式也很简单，只需要在默认变量之前重新声明下变量即可。
``` bash
$baseLineHeight: 2;
$baseLineHeight: 1.5 !default;
body{
	line-height: $baseLineHeight; 
}
```
编译后的css代码：
``` bash
body{
	line-height:2;
}
```
### [Sass]变量的调用
在 Sass 中声明了变量之后，就可以在需要的地方调用变量。调用变量的方法也非常的简单。
``` bash
.btn-primary {
	background-color: $btn-primary-bg;
	color: $btn-primary-color;
	border: 1px solid $btn-primary-border;
}
```
### [Sass]局部变量和全局变量
Sass 中变量的作用域在过去几年已经发生了一些改变。直到最近，规则集和其他范围内声明变量的作用域才默认为本地。如果已经存在同名的全局变量，从 3.4 版本开始，Sass 已经可以正确处理作用域的概念，并通过创建一个新的局部变量来代替。
先来看一下代码例子：
``` bash
//SCSS
$color: orange !default;//定义全局变量(在选择器、函数、混合宏...的外面定义的变量为全局变量)
.block {
  color: $color;//调用全局变量
}
em {
  $color: red;//定义局部变量
  a {
	color: $color;//调用局部变量
  }
}
span {
  color: $color;//调用全局变量
}
```
``` bash
//CSS
.block {
  color: orange;
}
em a {
  color: red;
}
span {
  color: orange;
}
```
上面的示例演示可以得知，在元素内部定义的变量不会影响其他元素。如此可以简单的理解成，全局变量就是定义在元素外面的变量，而定义在元素内部的变量，是一个局部变量。
我的建议，创建变量只适用于感觉确有必要的情况下。不要为了某些骇客行为而声明新变量，这丝毫没有作用。只有满足所有下述标准时方可创建新变量：
1. 该值至少重复出现了两次；
2. 该值至少可能会被更新一次；
3. 该值所有的表现都与变量有关（非巧合）。

### [Sass]嵌套-选择器嵌套
1、选择器嵌套
如果有：
``` bash
<header>
<nav>
    <a href=“##”>Home</a>
    <a href=“##”>About</a>
    <a href=“##”>Blog</a>
</nav>
<header>
```
想选中 header 中的 a 标签，在写 sCSS 会这样写：
``` bash
nav {
  a {
	color: red;
	header & {
		color:green;
	}
  }  
}
```
``` bash
nav a {
  color:red;
}

header nav a {
  color:green;
}
```
### [Sass]嵌套-属性嵌套
Sass 中还提供属性嵌套，CSS 有一些属性前缀相同，只是后缀不一样，比如：border-top/border-right，与这个类似的还有 margin、padding、font 等属性。假设你的样式中用到了：
``` bash
.box {
	border-top: 1px solid red;
	border-bottom: 1px solid green;
}
```
scss中可以这么写：
``` bash
.box {
  border: {
   top: 1px solid red;
   bottom: 1px solid green;
  }
}
```
注意border后面的冒号。
### [Sass]嵌套-伪类嵌套
其实伪类嵌套和属性嵌套非常类似，只不过他需要借助`&`符号一起配合使用。我们就拿经典的“clearfix”为例吧：
``` bash
.clearfix{
&:before,
&:after {
    content:"";
    display: table;
  }
&:after {
    clear:both;
    overflow: hidden;
  }
}
```
& 和冒号之间没有空格。
``` bash
clearfix:before, .clearfix:after {
  content: "";
  display: table;
}
.clearfix:after {
  clear: both;
  overflow: hidden;
}
```

选择器嵌套最大的问题是将使最终的代码难以阅读。开发者需要花费巨大精力计算不同缩进级别下的选择器具体的表现效果。
选择器越具体则声明语句越冗长，而且对最近选择器的引用(&)也越频繁。在某些时候，出现混淆选择器路径和探索下一级选择器的错误率很高，这非常不值得。

为了防止此类情况，我们应该尽可能避免选择器嵌套。然而，显然只有少数情况适应这一措施。
### [Sass]混合宏-声明混合宏
如果你的整个网站中有几处小样式类似，比如颜色，字体等，在 Sass 可以使用变量来统一处理，那么这种选择还是不错的。但当你的样式变得越来越复杂，需要重复使用大段的样式时，使用变量就无法达到我们目了。这个时候 Sass 中的混合宏就会变得非常有意义。
``` bash
@mixin border-radius{
    -webkit-border-radius: 5px;
    border-radius: 5px;
}
```	
其中 @mixin 是用来声明混合宏的关键词，有点类似 CSS 中的 @media、@font-face 一样。border-radius 是混合宏的名称。大括号里面是复用的样式代码。
除了声明一个不带参数的混合宏之外，还可以在定义混合宏时带有参数，如：
``` bash
@mixin border-radius($radius:5px){
    -webkit-border-radius: $radius;
    border-radius: $radius;
}
```
上面是一个简单的定义混合宏的方法，当然， Sass 中的混合宏还提供更为复杂的，你可以在大括号里面写上带有逻辑关系，帮助更好的做你想做的事情,如：
``` bash
@mixin box-shadow($shadow...) {
  @if length($shadow) >= 1 {
    @include prefixer(box-shadow, $shadow);
  } @else{
    $shadow:0 0 4px rgba(0,0,0,.3);
    @include prefixer(box-shadow, $shadow);
  }
}
```
这个 box-shadow 的混合宏，带有多个参数，这个时候可以使用“ … ”来替代。简单的解释一下，当 $shadow 的参数数量值大于或等于“ 1 ”时，表示有多个阴影值，反之调用默认的参数值“ 0 0 4px rgba(0,0,0,.3) ”。
### [Sass]混合宏-调用混合宏
``` bash
button {
    @include border-radius;
}
```
@mixin 类似函数声明，@include 类似函数调用

### [Sass]混合宏的参数--传一个不带值的参数
在混合宏中，可以传递一个不带任何值得参数，比如：
``` bash
@mixin border-radius($radius){
  -webkit-border-radius: $radius;
  border-radius: $radius;
}
```
``` bash
.box {
  @include border-radius(3px);
}
```
注意，不传值会报错。

### [Sass]混合宏的参数--传一个带值的参数
在 Sass 的混合宏中，还可以给混合宏的参数传一个默认值，例如：
``` bash
@mixin border-radius($radius:3px){
  -webkit-border-radius: $radius;
  border-radius: $radius;
}
```
在混合宏“border-radius”传了一个参数“$radius”，而且给这个参数赋予了一个默认值“3px”。
在调用类似这样的混合宏时，会多有一个机会，假设你的页面中的圆角很多地方都是“3px”的圆角，那么这个时候只需要调用默认的混合宏“border-radius”:
``` bash
.btn {
  @include border-radius;
}
```
但有的时候，页面中有些元素的圆角值不一样，那么可以随机给混合宏传值，如：
``` bash
.box {
  @include border-radius(50%);
}
```
### [Sass]混合宏的参数--传多个参数
``` bash
@mixin center($width,$height){
  width: $width;
  height: $height;
  position: absolute;
  top: 50%;
  left: 50%;
  margin-top: -($height) / 2;
  margin-left: -($width) / 2;
}
```
调用：
``` bash
.box-center {
  @include center(500px,300px);
}
```
有一个特别的参数“…”。当混合宏传的参数过多之时，可以使用参数来替代，如：
``` bash
@mixin box-shadow($shadows...){
  @if length($shadows) >= 1 {
    -webkit-box-shadow: $shadows;
    box-shadow: $shadows;
  } @else {
    $shadows: 0 0 2px rgba(#000,.25);
    -webkit-box-shadow: $shadow;
    box-shadow: $shadow;
  }
}
```
### [Sass]混合宏的参数--混合宏的不足
混合宏在实际编码中给我们带来很多方便之处，特别是对于复用重复代码块。但其最大的不足之处是会生成冗余的代码块。比如在不同的地方调用一个相同的混合宏时。如：
``` bash
@mixin border-radius{
  -webkit-border-radius: 3px;
  border-radius: 3px;
}

.box {
  @include border-radius;
  margin-bottom: 5px;
}

.btn {
  @include border-radius;
}
```




























































































































































































































































