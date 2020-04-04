---
title: mapbox-gl双指倾斜地图
date: 2019-11-01 19:35:00
tags: mapbox
categories: mapbox
---

## 项目环境
移动端采用：ionic+angular混合开发；
地图框架采用：mapbox-gl
## 遇到问题
在web端，mapbox-gl的地图可以通过鼠标右键操作倾斜、旋转，但是在移动端需要通过触摸事件来操作，mapbox-gl在TouchZoomRotateHandler类中只加入了触摸平移、缩放、旋转事件，并没有地图倾斜事件；因此APP上的地图可以单指/双指拖动，双指缩放和旋转，但是不能像web端一样倾斜；
## 解决思路
<!--more-->
修改mapbox-gl源码中的TouchZoomRotateHandler类，增加双指滑动倾斜地图效果。
监听touchstart时的地图pitch值和双指起始位置，touchmove时判断双指是否平行滑动，根据滑动的长度计算地图当前的倾斜度即pitch的值，调用设置地图倾斜度的方法setPitch实现想要的效果。
关键实现代码：
``` bash
 const Y0 = p0.y - this._startP0.y; //计算第一个手指滑动距离
 const Y1 = p1.y - this._startP1.y; //计算第二个手指滑动距离
 const b = (Y0 <= 0 && Y1 <= 0 || Y0 >= 0 && Y1 >= 0); //判断双指是否平行滑动
 if (b) {
     const diff = (Y0 + Y1) / 2 * 0.5;
     this._map.setPitch(this._startPitch + diff); //设置地图倾斜度
 }
```
## 成果展示
github:[https://github.com/Minaszy/mapbox-gl-js.git](https://github.com/Minaszy/mapbox-gl-js.git)
<img src="001.jpg" width=50% />