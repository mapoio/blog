---
layout: page
title: 仙术 - 并查集
date: 2016-07-20
description: 本篇文章介绍数据结构中的一种-并查集。
header-img: "img/blue.jpg"
author: "isheng5"
special: true
---
### 正在编写中 先占个地方

## 中间数的判断之sort函数使用

> ### 原题题目
>
>Time Limit:1000MS     Memory Limit:32768KB     64bit IO Format:%I64d & %I64u
>
>### Description
>
>FJ is surveying his herd to find the most average cow. He wants to know how much milk this 'median' cow gives: half of the cows give as much or more than the median; half give as much or less.
>
>Given an odd number of cows N (1 <= N < 10,000) and their milk output (1..1,000,000), find the median amount of milk given such that at least half the cows give the same amount of milk or more and at least half give the same or less.
>
>#### Input
>
>* Line 1: A single integer N
>
>* Lines 2..N+1: Each line contains a single integer that is the milk output of one cow.
>
>#### Output
>
>* Line 1: A single integer that is the median milk output.
>
>#### Sample Input
>5
>
>2
>
>4
>
>1
>
>3
>
>5
>
>#### Sample Output
>3
>
>#### Hint
> INPUT DETAILS: Five cows with milk outputs of 1..5 OUTPUT DETAILS: 1 and 2 are below 3; 4 and 5 are above 3.

### 我的思路
这道题目还是比较简单的，直接使用函数sort()对输入的数据进行排序就可以然后输出中间那个数据就1可以了。

### 调试过程
一次通过，无调试信息。

### 改进
后期有用快排试了一下，发现效率不如函数sort()所以没啥子改进的。

### 我的代码
```c++
#include <iostream>
#include <algorithm>//sort函数的头文件
#include <cstdio>

using namespace std;

int w2tgGa[10001];

int main(){
    int g;
//    freopen("in.txt", "r", stdin);
    while(cin>>g){
        for (int i = 0; i < g; ++i)
            cin>>w2tgGa[i];
        sort(w2tgGa,w2tgGa + g);//使用sort函数排序
        cout << w2tgGa[(g-1) / 2] << endl;
    }
}
```
Conding是一件有趣的事情！

---
markdown数学公式MathJax测试
$$x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}$$
