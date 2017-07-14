---
layout: post
title: "原生Javascript实现瀑布流布局"
subtitle: "适用于图片网站的瀑布流布局，原生Javascript实现"
date: 2016-10-29 00:00:00
categories: blog
author: "isheng52"
header-img: "/cdn.isheng.top/zhihu.jpg"
tags:
  - 前端
  - Javascript
description: "适用于图片网站的瀑布流布局，原生Javascript实现"
---

> 第一次写前端的博文，如有好的建议或者不对之处欢迎在评论里面丢香蕉。

## 瀑布流
瀑布流，又称瀑布流式布局。是比较流行的一种网站页面布局，视觉表现为参差不齐的多栏布局，随着页面滚动条向下滚动，这种布局还会不断加载数据块并附加至当前尾部。最早采用此布局的网站是Pinterest，逐渐在国内流行开来。国内大多数清新站基本为这类风格。

## 实现方法
* 原生Javascript
* Jquery
* CSS3

## 适用场景
瀑布流布局大都适用于图片站点，主要是为了展示不同大小尺寸的图片或者图文，但也有用于一些特别网站，下面给出一些使用瀑布流布局网站供参考。

> [Pinterest - 等宽布局](https://www.pinterest.com/)  
> [Pexels - 等高布局](https://www.pexels.com/?s=)  
> [淘宝商品检索 - 等高等宽布局](https://s.taobao.com/search?q=%E5%A2%99%E7%BA%B8)

## 瀑布流实例 - 原生Javascript

> 由于我只写了原生Javascript实现的等宽瀑布流代码，所以就只更新这种，Jquery差不多，下次补上CSS3的。


### 主要思路

用原生Javascript实现瀑布流布局主要是获得当前网页的宽度，通过不断的计算当前页面的宽度来计算出需要加载多少列图片，再用Javascript来定位图片。

### 源代码

Javascript部分

```javascript
//demo.js源代码
function srollInit() {
    //获取页面参数，为刷新布局做准备
    var pageWidth = document.documentElement.offsetWidth;
    var aBox = document.getElementById('demo');
    var picBox = aBox.getElementsByTagName('li');
    var liWidth = picBox[0].offsetWidth + 10;
    var boxNum = Math.floor(pageWidth / liWidth);
    aBox.style.marginLeft = (pageWidth - (liWidth + 3) * boxNum) / 2 + 'px';
    aBox.style.width = liWidth * boxNum + 'px';

    //设置每张图片的参数
    var onArr = [];
    for (var i = 0; i < picBox.length; i++) {
        if (i < boxNum) { onArr.push(0); }
        var min = getMin(onArr);
        picBox[i].style.top = onArr[min] + 10 + 'px';
        picBox[i].style.left = liWidth * min + 'px';
        picBox[i].style.opacity = '1';
        onArr[min] = picBox[i].offsetHeight + onArr[min] + 10;
    }

    //获得当前数组中的最小值
    function getMin(arr) {
        var min = arr[0];
        var minNum = 0;
        for (var x in arr) {
            if (arr[x] < min) {
                min = arr[x];
                minNum = x;
            }
        }
        return minNum;
    }
}

//响应事件，当页面的大小改变时调用函数来改变图片布局。
window.onresize = function() {
    var retime;
    clearTimeout(retime);
    retime = setTimeout(function() { srollInit(); }, 100);
}
```

html部分

```html
<!DOCTYPE html>
<html lang="zh-cn">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <link rel="stylesheet" href="./css/style.css" media="screen" title="no title">
    <title>图片瀑布流排版</title>
</head>

<body onload="srollInit()">
    <!--加载完成后运行初始化函数-->
    <h1>瀑布流布局演示</h1>
    <ul id="demo">
        <li><img src="./img/1.jpg" alt="图片1"></li>
        <li><img src="./img/2.jpg" alt="图片2"></li>
        <li><img src="./img/3.jpg" alt="图片3"></li>
        <li><img src="./img/4.jpg" alt="图片4"></li>
        <li><img src="./img/5.jpg" alt="图片5"></li>
        <li><img src="./img/6.jpg" alt="图片6"></li>
        <li><img src="./img/7.jpg" alt="图片7"></li>
        <li><img src="./img/8.jpg" alt="图片8"></li>
        <li><img src="./img/9.jpg" alt="图片9"></li>
        <li><img src="./img/10.jpg" alt="图片10"></li>
        <li><img src="./img/11.jpg" alt="图片11"></li>
        <li><img src="./img/12.jpg" alt="图片12"></li>
        <li><img src="./img/13.jpg" alt="图片13"></li>
        <li><img src="./img/14.jpg" alt="图片14"></li>
        <li><img src="./img/15.jpg" alt="图片15"></li>
        <li><img src="./img/16.jpg" alt="图片16"></li>
        <li><img src="./img/17.jpg" alt="图片17"></li>
        <li><img src="./img/18.jpg" alt="图片18"></li>
        <li><img src="./img/19.jpg" alt="图片19"></li>
        <li><img src="./img/20.jpg" alt="图片20"></li>

    </ul>

</body>
<script src="./js/demo.js" type="text/javascript"></script>

</html>
```

样式表

```css
body {
    background-color: #353559;
    font-family: "Microsoft YaHei";
    color: white;
    margin: 0px;
}

body h1 {
    text-align: center;
    font-size: 60px;
}

#demo {
    position: absolute;
    margin-left: auto;
    padding: 0px;
}

#demo li {
    position: absolute;
    margin: 10px;
    width: 200px;
    list-style: none;
    opacity: 0;
}

#demo li img {
    border: 3px solid #674950;
    border-radius: 10px;
    width: 100%;
}
```

> 代码到此为止，有什么问题欢迎到评论区投香蕉。