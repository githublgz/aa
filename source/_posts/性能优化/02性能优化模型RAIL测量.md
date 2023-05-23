---
title: 前端性能优化-02
cover: false
top: false
toc: false
mathjax: false
tags:
  - 优化
categories:
  - 前端性能优化
abbrlink: 23375
date: 2021-03-02 20:49:27
author:
img:
coverImg:
password:
summary:
keywords:
---


## 什么是RAIL：

缩写每一个字母表示一个性能指标

Response：响应  对用户而言用户点击有没有即使访问

Animation：动画 给用户的体验好。会不会出现卡顿

Idie：空闲 有足够的空闲时间才能够进行响应交互   不让他始终处于繁忙的时间，不然无法处理其他请求

![image-20220427100359004](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427100359004.png)

Load：加载 资源网络加载的时间



**RAIL目标**

**评估指标**

响应：处理时间应在50ms以内完成

动画：没10ms产生一帧

空闲：尽可能增加空闲时间

加载：在5s内完成内容加载并可以交互

逐步发现问题解决问题



性能测量工具

Chrome DevTools 开发者调试工具，性能评测

Lighthouse 网站整体质量评估

WebPageTest 多测量地点、全面行能报告（通过 它的问斩对自己的网站就行进行评估）



## **WenPageTest**

webpagetest.org

![image-20220427101136898](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427101136898.png)

用户首次方位  第二次访问  （第一次对一些静态资源做缓存）

录视频

![image-20220427101340209](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427101340209.png)

测试结果：

![image-20220427101426225](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427101426225.png)

第一次加载，首屏渲染，

speed 速度指数等



图片并行加载  时间有加载最长时间决定

![image-20220427103510108](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427103510108.png)

高亮（重定向了）  可以优化  直接去访问重定向后的位置

![image-20220427103527654](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427103527654.png)



![image-20220427103716493](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427103716493.png)

前提：docker

docker pull webpagetest/server

docker pull webpagetest/a

docker run -d -p 4000:80 webpagetest/server

![image-20220427103841249](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427103841249.png)



自定义镜像

![image-20220427103951183](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427103951183.png)



![image-20220427103923723](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427103923723.png)

![image-20220427103940202](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427103940202.png)



![image-20220427104025309](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427104025309.png)

![image-20220427104016148](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427104016148.png)

打包

![image-20220427104104578](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427104104578.png)

![image-20220427104110269](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427104110269.png)

这样就可以本地测试了。



## Lighthouse

npm install -g lighthouse

lighthouse 测试网站地址

自动打开一个浏览器窗口

生成而是报告到本地

打开即可查看

![image-20220427104412546](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427104412546.png)



第一个有意义的内容

什么时候用户可以交互了

用户访问过程截屏。

Opportunity：告诉我们还可以做些什么做到了可以提升那些多长时间



![image-20220427104938078](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427104938078.png)

![image-20220427105002256](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427105002256.png)

可以指定什么样的资源不进行加载。

## Chrome devtool调试

Network：

资源名称  大小   耗时

![image-20220427133838888](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427133838888.png)

对请求资源进行压缩在返回前端

![image-20220427134024090](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427134024090.png)

实际大小 parse  1.4MB   网络传输（index）55kb



Performance：

开始出现性能分析：可以定位出导致长任务（耗费时间长）的方法。

![image-20220427134236511](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427134236511.png)

主线程任务。

Disabel cache缓存。 Online：选择网络

![image-20220427134610044](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427134610044.png)



![image-20220427134719082](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427134719082.png)



![image-20220427134737203](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20220427134737203.png)

















