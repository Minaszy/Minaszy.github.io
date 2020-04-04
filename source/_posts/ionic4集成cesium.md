---
title: ionic4集成cesium
date: 2019-11-09 11:03:43
tags: [cesium, ionic]
categories: 前端框架整合gis框架
---

## 搭建ionic4环境
先给个官网地址，后面再写一篇博客。[官方环境搭建](https://ionicframework.com/docs/installation/environment)

## 安装cesium
```bash
npm install --save cesium
```

## 配置cesium
<!--more-->
我们需要将cesium的js、css及其他必要的附件添加到项目里，angular的项目可以通过angular.json这个文件来配置第三方库。主要有四个地方需要配置：
1、配置angular.json里的assets;
2、配置angular.json里的styles;
3、配置angular.json里的scripts;

{% asset_img config.jpg 配置截图 %}
4、配置main.ts，添加 
```bash
window['CESIUM_BASE_URL'] = '/assets/cesium/';
```

## 声明使用cesium,创建地球
```bash
import { Component, ViewChild, ElementRef, Directive, OnInit } from '@angular/core';
declare var Cesium;
@Component({
  selector: 'app-home',
  templateUrl: 'home.page.html',
  styleUrls: ['home.page.scss'],
})
export class HomePage implements OnInit {
  @ViewChild('map', null) mapContainer: ElementRef;
  constructor() { }
  ngOnInit() {
    const viewer = new Cesium.Viewer(this.mapContainer.nativeElement);
  }
}
```

## 成果展示及代码分享
demo项目代码: [https://github.com/Minaszy/ionicWithCesium](https://github.com/Minaszy/ionicWithCesium)
{% asset_img ce.jpg 成果展示 %}

## 参考
本文参考：
[Cesium and Angular](https://cesium.com/blog/2018/03/12/cesium-and-angular/)
[angular-cesium](https://www.npmjs.com/package/angular-cesium)