---
title: ionic4集成arcgis api for js 4.x
date: 2019-11-09 11:03:06
tags: [arcgisApi, ionic]
categories: 前端框架整合gis框架
---
## 概述
主要使用esri-loader这个插件来加载arcgis api的模块。直接安装使用esri-loader，会默认从CDN上最新版本的arcgis api加载需要的模块。本文将arcgis api部署到本地使用,api大概60多M，这种方式会使app包变大。具体的使用方法可以参考[官方esri-loader使用介绍](https://github.com/Esri/esri-loader#usage)。
## 搭建ionic4环境
先给个官网地址，后面再写一篇博客。[官方环境搭建](https://ionicframework.com/docs/installation/environment)
## 安装esri-loader
```bash
npm i esri-loader --save
```
<!--more-->
## 引入arcgis api css
一、使用esri-loader的loadCss
```bash
import { loadCss } from 'esri-loader';

// 默认情况加载CDN上的最新版本
loadCss();

// 加载CDN上的指定版本
loadCss('3.30');

// 从指定url或者本地服务上的地址加载
loadCss('http://server/path/to/esri/css/main.css');

setDefaultOptions({ css: true });
或者
loadModules(['esri/views/MapView', 'esri/WebMap'。。。],{ css: true })
```
二、使用esri-loader的setDefaultOptions
```bash
import { setDefaultOptions, loadModules } from 'esri-loader';
setDefaultOptions({ css: true });
或者
setDefaultOptions({ url:'',css: 'http://server/path/to/esri/css/main.css' });
或者
loadModules(['esri/views/MapView', 'esri/WebMap'。。。],{ css: true })
```
三、loadModules时加载
```bash
import { loadModules } from 'esri-loader';
loadModules(['esri/views/MapView', 'esri/WebMap'。。。],{ css: true })
或者
loadModules(['esri/views/MapView', 'esri/WebMap'。。。],{ url:'',css: 'http://server/path/to/esri/css/main.css' })
```
## 引入arcgis api，创建地图
在html中创建地图div并设置好样式，在页面中通过esri-loader引用arcgis api的模块：
```bash
import { loadModules } from 'esri-loader';

loadModules(['esri/views/MapView', 'esri/WebMap'],{ css: true })
  .then(([MapView, WebMap]) => {
    var webmap = new WebMap({
      portalItem: {
        id: 'f2e9b762544945f390ca4ac3671cfa72'
      }
    });

    var view = new MapView({
      map: webmap,
      container: 'viewDiv'
    });
  })
  .catch(err => {
    console.error(err);
  });
```
默认情况下，esri-loader将从CDN上加载最新版的arcgis api，当然也可以从CDN上加载指定版本的api或者从自己的服务器上加载，通过setDefaultOptions或者loadModules时指定version参数/本地服务url。更详细的配置参考[esri-loader配置](https://github.com/Esri/esri-loader#configuring-esri-loader)

## 本地部署arcgis api for js
去官网下载arcgis api
[下载地址：https://developers.arcgis.com/downloads/apis-and-sdks?product=javascript](https://developers.arcgis.com/downloads/apis-and-sdks?product=javascript)
<img src="api.jpg" width=60% />

将下载好的api放在web服务器下，根据自己的服务器ip和端口修改arcgis api的init.js和dojo.js,在这两个文件里搜索[HOSTNAME_AND_PATH_TO_JSAPI]，将baseUrl的值替换为:www.example.com/arcgis_js_api/library/4.11/

将下载好的arcgis api中的4.x文件夹放到项目assets/lib目录下，修改arcgis api的init.js和dojo.js,在这两个文件里搜索[HOSTNAME_AND_PATH_TO_JSAPI]，将baseUrl的值替换为:
```bash
(location.protocol === 'file:' ? 'http:' : location.protocol) + '//' + location.host + "/assets/lib/4.11/dojo"
```
然后通过setDefaultOptions或者loadModules时指定本地api服务url。
```bash
const options1 = { url: 'http://localhost:8085/arcgis_js_v411_api/arcgis_js_api/library/4.11/init.js', css: 'http://localhost:8085/arcgis_js_v411_api/arcgis_js_api/library/4.11/esri/css/main.css' };
```
## 成果展示及代码分享
demo项目代码: [https://github.com/Minaszy/ionicWithArcApi4x](https://github.com/Minaszy/ionicWithArcApi4x)
<img src="earth.jpg" width=20% />
<img src="3d.jpg" width=20% />

## 其他框架
arcgis api for js与angular、ember、react、vue一起使用的方法参见[官网介绍](https://developers.arcgis.com/javascript/latest/guide/using-frameworks/)

## 其他问题
是否要安装npm install @types/arcgis-js-api --save，待述。
