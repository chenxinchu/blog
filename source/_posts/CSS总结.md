---
title: CSS总结
date: 2020-07-06 09:24:53
tags:
---

## 盒子模型
CSS盒子模型包括`IE盒子模型`和`标准W3C盒子模型`
- W3C标准盒子模型：左右border + 左右padding + contentwidth
- W3C盒子模型包括margin、border、padding、content，并且content部分不包含其他部分
- IE盒子模型border-box:width = content + padding + border 元素指定的任何内边距和边框都将在已设定的宽和高进行绘制
- IE盒子模型包括margin、border、padding、content，和W3C盒子模型不同的是，IE盒子模型的content部分包括了padding和border  
inherit:从父类继承box-sizing的值  
border-sizing: border-box,inherit,content-box

## 行内元素和块级元素
### 行内元素
常见行内元素：   
`<span>、<img>、<input>、<br>、<a>`   
行内元素特征：   
1. 设置宽高无效
2. 对margin仅设置左右方向有效，上下无效；padding设置上下左右都有效，即会撑大空间
3. 不会自动进行换行

### 块级元素
常见块级元素：   
`<div>、<audio>、<h1>、<hr>、<nav>、<ul>、<ol>`   
块状元素特征：  
1. 能够识别宽高
2. margin和padding的上下左右均对其有效
3. 可以自动换行
4. 多个块状元素标签写在一起，默认排列方式为从上至下

### 行内块状元素
行内块状元素综合了行内元素和块状元素的特性，但是各有取舍。因此行内块状元素在日常的使用中，由于其特性，使用的次数也比较多。   
行内块状元素特征：   
1. 不自动换行
2. 能够识别宽高
3. 默认排列方式为从左到右

这三者是可以互相转换的，使用display属性能够将三者任意转换：   
1. display:inline;转换为行内元素
2. display:block;转换为块状元素
3. display:inline-block;转换为行内块状元素

## html5新特性
- 新的HTML标签像`<header>, <footer>，<nav>，<section>，<aside>`等等
- <!DOCTYPE>标签是html5标准网页声明,可告知浏览器文档使用哪种 HTML 或 XHTML 规范

## css预处理
CSS预处理器是一种专门的编程语言，用来为CSS增加一些编程特性。如sass、less。stylus   
作用：
- CSS语法不够强大，例如：CSS选择器无法进行嵌套，导致CSS中存在较多重复的选择器语句；CSS中无法定义变量以及没有合理的样式复用机制，导致整体CSS样式难以维护。
- 为了减少CSS代码冗余，为CSS提供样式复用机制，提高CSS代码的可维护性。

## flex布局
布局的传统解决方案，基于盒状模型，依赖display属性 + position属性 + float属性。它对于那些特殊布局非常不方便，比如，垂直居中就不容易实现。flex弹性布局就解决了这个问题
### flex实现水平垂直居中
```html
<body>
  <div class="content"></div>
  <style>
    html,body {
      width: 100%;
      height: 100%;
      margin: 0;
      padding: 0;
    }
    body {
      display: flex;
      align-items: center;
      /*定义body的元素垂直居中*/
      justify-content: center;
      /*定义body的里的元素水平居中*/
    }
    .content {
      width: 300px;
      height: 300px;
      background: orange;
    }
  </style>
</body>
```
### 不用flex实现水平垂直居中
```html
<body>
  <div class="content"></div>
  <style>
    html,body {
      width: 100%;
      height: 100%;
      margin: 0;
      padding: 0;
    }
    .content {
      width: 300px;
      height: 300px;
      background: orange;
      margin: 0 auto;
      position: relative;
      top: 50%;
      transform: translateY(-50%);
    } 
  </style>
</body>
```

### flex实现左固定右自适应
```html
<body>
  <div style="display: flex;">
    <div class="left"></div>
    <div class="right"></div>
  </div>
  <style>
    .left{
      width: 300px;
      height: 300px;
      background-color: red;
    }
    .right{
      height: 300px;
      background-color: green;
      flex:1;
    }
  </style>
</body>
```
### 不用flex实现左固定右自适应
```html
<body>
  <div>
    <div class="left"></div>
    <div class="right"></div>
  </div>
  <style>
    .left{
      width: 300px;
      height: 300px;
      background-color: red;
      float: left;
    }
    .right{
      height: 300px;
      background-color: green;
    }
  </style>
</body>
```

## 清除浮动
为什么要清除浮动   
当父元素不给高度的时候，内部元素不浮动时会撑开父元素   
而浮动的时候，父元素变成一条线   

### 清除浮动的4种方法
- 额外标签法（在最后一个浮动标签后，新加一个标签，给其设置clear:both;）（不推荐）
```html
<body>
  <div class="fahter">
    <div class="big">big</div>
    <div class="small">small</div>
    <div class="clear">额外标签法</div>
  </div>
  <style>
    .fahter{
      width: 400px;
      border: 1px solid deeppink;
    }
    .big{
      width: 200px;
      height: 200px;
      background: darkorange;
      float: left;
    }
    .small{
      width: 120px;
      height: 120px;
      background: darkmagenta;
      float: left;
    }
    .clear{
      clear:both;
    }
  </style>
</body>
```
 如果我们清除了浮动，父元素自动检测子盒子最高的高度，然后与其同高。  
优点：通俗易懂，方便   
缺点：添加无意义标签，语义化差   
不建议使用   

- 父级添加overflow属性（父元素添加overflow:hidden）（不推荐）  
通过触发BFC方式，实现清除浮动
```css
.fahter{
  width: 400px;
  border: 1px solid deeppink;
  overflow: hidden;
}
```
优点：代码简洁   
缺点：内容增多的时候容易造成不会自动换行导致内容被隐藏掉，无法显示要溢出的元素   
不推荐使用   

- 使用after伪元素清除浮动（推荐使用）
```html

<body>
  <div class="fahter clearfix">
    <div class="big">big</div>
    <div class="small">small</div>
  </div>
  <style>
    .clearfix:after{/*伪元素是行内元素 正常浏览器清除浮动方法*/
      content: "";
      display: block;
      height: 0;
      clear:both;
      visibility: hidden;
    }
    .clearfix{
      *zoom: 1;/*ie6清除浮动的方式 *号只有IE6-IE7执行，其他浏览器不执行*/
    }
  </style>
</body>
```
优点：符合闭合浮动思想，结构语义化正确   
缺点：ie6-7不支持伪元素：after，使用zoom:1触发hasLayout.   
推荐使用   

- 使用before和after双伪元素清除浮动
```html
<body>
  <div class="fahter clearfix">
    <div class="big">big</div>
    <div class="small">small</div>
  </div>
  <style>
    .clearfix:after,.clearfix:before{
      content: "";
      display: table;
    }
    .clearfix:after{
      clear: both;
    }
    .clearfix{
      *zoom: 1;
    }
  </style>
</body>
```
优点：代码更简洁   
缺点：用zoom:1触发hasLayout.   
推荐使用   

清除浮动的简单回答
1. 给父级元素定义高度 
2. 让父级元素也浮动 
3. 父级定义display:table 
4. 父元素设置overflow:hidden 
5. clearfix:使用内容生成的方式清除浮动

## BFC
BFC是css布局的一个概念，是一块独立的渲染区域，一个环境，里面的元素不会影响到外部的元素  

如何生成BFC：（脱离文档流）
1. 根元素，即HTML元素（最大的一个BFC）
2. float的值不为none
3. position的值为absolute或fixed
4. overflow的值不为visible（默认值。内容不会被修剪，会呈现在元素框之外）
5. display的值为inline-block、table-cell、table-caption

BFC布局规则：
1. 内部的Box会在垂直方向，一个接一个地放置。
2. 属于同一个BFC的两个相邻的Box的margin会发生重叠
3. BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此, 文字环绕效果，设置float
4. BFC的区域不会与float box重叠。
5. 计算BFC的高度，浮动元素也参与计算

BFC作用：
1. 自适应两栏布局
2. 可以阻止元素被浮动元素覆盖
3. 可以包含浮动元素---清除内部浮动 原理:：触发父div的BFC属性，使下面的子div都处在父div的同一个BFC区域之内
4. 分属于不同的BFC时，可以阻止margin重叠

## 经典三列式布局

### 圣杯布局
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>圣杯布局</title>
  <style>
    body {
      background-color: #666;
    }
    .bd {
      padding: 0px 200px 0 180px;
      height: 100px;
      text-align: center;
      font-family: Arial;
      text-align: center;
    }
    .middle, .left, .right {
      height: 500px;
      float: left;
      position: relative;
      line-height: 500px;
    }
    .middle {
      width: 100%;
      background-color: #f5c531;
    }
    .left {
      width: 180px;
      background-color: #a0c263;
      margin-left: -100%;
      left: -180px;
    }
    .right {
      width: 200px;
      margin-left: -200px;
      background-color: #a0c263;
      right: -200px;
    }
  </style>
</head>
<body>
  <div class="bd">
    <div class="middle">Content</div>
    <aside class="left">left</aside>
    <aside class="right">right</aside>
  </div>
</body>
</html>
```

### 双飞翼布局
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .wrapper {
      padding: 0;
      overflow: hidden;
      text-align: center;
      line-height: 200px;
    }
    .col {
      float: left;
    }
    .main-wrapper {
      width: 100%;
    }
    .main {
      margin: 0 100px 0 100px;
      height: 200px;
      background-color: #f5c531;
    }
    .left {
      width: 100px;
      height: 200px;
      margin-left: -100%;
      background-color: #a0c263;
    }
    .right {
      width: 100px;
      height: 200px;
      margin-left: -100px;
      background-color: #a0c263;
    }
  </style>
</head>
<body>
  <section class="wrapper">
    <section class="col main-wrapper">
      <section class="main">main</section>
    </section>
    <aside class="col left">left</aside>
    <aside class="col right">right</aside>
  </section>
</body>
</html>
```
圣杯布局缺点：宽度小于 left 的时候会掉下去   
双飞翼布局解决了圣杯布局的 bug，但缺点是 DOM 树更复杂，渲染性能不如圣杯布局
