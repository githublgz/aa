---
title: 前端性能优化-03
cover: false
top: false
toc: false
mathjax: false
tags:
  - 优化
categories:
  - 前端性能优化
abbrlink: 23375
date: 2021-03-09 14:27:08
author:
img:
coverImg:
password:
summary:
keywords:
---

## Web Api

![image-20220427135134108](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427135134108.png)



![image-20220427135953185](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427135953185.png)



![image-20220427135638721](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427135638721.png)



## 页面渲染原理

**![image-20220427140026412](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427140026412.png)**



![image-20220427140215339](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427140215339.png)

## 

![image-20220427140251251](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427140251251.png)



浏览器解释器  将他们翻译成浏览器认识的形式。

![image-20220427140455463](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427140455463.png)



![image-20220427140508402](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427140508402.png)



![image-20220427140829766](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427140829766.png)

## 回流

![image-20220427141037729](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427141037729.png)

里纳西不断的回流：

![image-20220427141659895](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427141659895.png)



![image-20220427141728695](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427141728695.png)



## fastdom

批量对DOM的读写操作     先执行读操作  后执行写操作

强制布局：很卡顿（连续的强制读写更新） 但是使用了fastdom(读写分离)后执行就会流畅

![image-20220427144318668](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427144318668.png)



![image-20220427144353664](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427144353664.png)



## 复合线程与图层

![image-20220427144903921](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427144903921.png)



在控制太我们通过frame就可以查看对应图层

这些不会触发布局和重绘，只会触发复合的过程给他们一个图层

![image-20220427145049336](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427145049336.png)

查看图层：

![image-20220427145130349](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427145130349.png)

查看交互点击第一个录制，然后停止就可以查看

第二个刷新只页面重新加载  。

![image-20220427145223610](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427145223610.png)

图层越多 开销也会越大。



## 减少重绘

![image-20220427150359238](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427150359238.png)



render：选中第一个它会告诉我们谁会重绘并且标问绿色

![image-20220427150050337](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427150050337.png)



![image-20220427150133967](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427150133967.png)



## 高频事件处理函数 防抖

![image-20220427150900593](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427150900593.png)

![image-20220427151047338](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427151047338.png)



事件触发  js  触发视觉变换   1帧开始    raf在  之前进行布局和绘制，

![image-20220427150955637](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427150955637.png)



![image-20220427151104506](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427151104506.png)













