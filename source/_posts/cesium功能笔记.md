---
title: cesium功能笔记
date: 20191.111.11 10:29:12
tags: cesium
categories: cesium
---
## cesium初始化时各个默认参数的含义
1. geocoder: 定位搜索工具，将相机飞到指定位置，默认使用Bing Maps；
2. homeButton: 将视窗飞回默认视角；
3. sceneModePicker: 用于切换3D、2D和哥伦布视图模式；
4. baseLayerPicker: 选择地球上的底图；
5. navigationHelpBotton: 提供地球操作帮助；
<!--more-->
6. animation: 控制动画播放速度；
8. timeline: 显示当前的时间，并允许用户跳转到指定时间；
9. fullscreenButton: 全屏显示按钮；
17. creditContainer: 包含creditsDisplay的dom元素或id，如果不指定，将会默认在底部显示，也就是说这个dom会被自动创建，不能让它不被创建，只能在创建后想办法隐藏；

10. imageryProvider: 指定地球的底图，只有当baseLayerPicker参数是false时，才能生效；
11. terrainProvider: 提供地形；
18. dataSources: The collection of data sources visualized by the widget. If this parameter is provided, the instance is assumed to be owned by the caller and will not be destroyed when the viewer is destroyed.小部件可视化的数据源集合。如果提供此参数，则假定实例由调用者拥有，并且在销毁查看器时不会销毁该实例。
19. terrainExaggeration: 地形夸张系数；

5. infoBox: 点击要素时自动创建属性框；
8. selectionIndicator: false,https://www.cnblogs.com/fuckgiser/p/5702544.html,测试设置为false后是否可以点击选择要素；

12. skyAtmosphere: 天空光晕效果；
13. skyBox: 渲染星星的天空盒子，值为undefined时，使用默认的星星图片；
14. sceneMode: 设置初始化地图模式，Cesium.SceneMode.SCENE3D,Cesium.SceneMode.COLUMBUS_VIEW，Cesium.SceneMode.SCENE2D，Cesium.SceneMode.MORPHING，默认是Cesium.SceneMode.SCENE3D；
15. mapProjection: 在2D和哥伦布模式下的投影系统；
16. globe: 场景中的球体，如果设置为false，将不加载球体；
20. shadows: 确定阴影是否由太阳投射；
21. terrainShadows: 确定地形阴影是否由太阳投射；
22. mapMode2D: 控制2d地图是无限拖动还是旋转模式，Cesium.MapMode2D.INFINITE_SCROLL，Cesium.MapMode2D.ROTATE；
23. projectionPicker: 选择3D转换为2D时的投影方式，Perspective Projection透视投影和Orthographic Projection正交投影
24. requestRenderMode: 如果为true，则仅根据场景中的更改确定是否需要渲染帧。启用可减少应用程序的CPU / GPU使用率，并减少移动设备上的电池消耗，但需要Scene#requestRender在此模式下显式渲染新帧。在API的其他部分对场景进行更改之后，在许多情况下这是必要的。请参见使用显式渲染提高性能。
详细见: https://cesium.com/blog/2018/01/24/cesium-scene-rendering-performance/
25. maximumRenderTimeChange: 如果requestRenderMode为true，则此值定义在请求渲染之前允许的最大仿真时间更改。请参见使用显式渲染提高性能。


## 问题集锦
# 如何控制初始化的视图中心点和缩放级别？
# 如何叠加，改变底图？如何改变图层透明度？
# 如何不使用底图？
# cesium的地球在没有底图的情况下是什么样的？
# 如何控制图层顺序？
# 如何隐藏底部的数据源信息控件？
# 如何不显示地球？
# 不显示地球时，底图图层和叠加的模型数据还显示吗？
# 如何加载geoserver发布的矢量切片服务？
# 如何加载mapbox的矢量切片服务？
# 如何加载arcgis发布的矢量切片服务？
# 如何加载WMS服务？
# 如何加载TMS服务？
# 如何加载WMTS服务？
# 能支持什么坐标系？
# 如何监听图层加载完成的事件？
# 如何加载动态绘制图层？
# 如何监听鼠标点击事件？
# cesium支持加载的图层类型有哪些？
# 没有添加任何图层的cesium地球是什么样的？
# viewer.scene.imageryLayers和viewer.imageryLayers的区别？
# 如何叠加经纬网？
# 如何叠加单张图片？
# 如何叠加雷达影像？
# 怎么禁止相机进入地下模式？
# 如何使用cesium加载大数据量的建筑物模型？思路和实践
# 如何开启光照阴影效果？







## 图层服务专题
cesium支持绘制和叠加多种服务提供的高分辨率影像，包括Cesium ion提供的服务。使用Cesium ion来流化策展的高分辨率影像或者将栅格数据切片成你自己的影像图层在cesium应用中使用。图层可以被排序和整合。每个图层的亮度、对比度、伽马、色相和饱和度都可以动态改变。本教程介绍影像图层的概念和相关的Cesium API。

添加图层的方式：
1. 在初始化参数中给imageryProvider赋值，赋值的结果类型要求是：
如：
```bash
Cesium.createWorldImagery({
    style : Cesium.IonWorldImageryStyle.AERIAL_WITH_LABELS
})
```
```bash
new Cesium.IonImageryProvider({
    assetId:3812
})
```
```bash
new Cesium.UrlTemplateImageryProvider({
    url: 'http://map.geoq.cn/ArcGIS/rest/services/ChinaOnlineStreetPurplishBlue/MapServer/tile/{z}/{y}/{x}'
})
```
2. cesium初始化时会自动从Cesium ion添加Bing Maps Aerial imagery作为底图。修改初始化参数imageryProvider后，将会用新图层替换掉默认Bing Maps Aerial图层,因此imageryProvider可以用来设置和修改底图。当你还需要叠加其他图层时，可以通过viewer上的imageryLayers添加其他imageryProvider。

3. imageryProvider imageryLayers ImageryLayerCollection addImageryProvider ImageryLayer概念
imageryProvider是初始化viewer时的参数，参数类型要求是imageryProvider，用来发送瓦片请求的类；
imageryLayers是viewer或者scene上的属性，用于存放图层组，参数类型是ImageryLayerCollection；
ImageryLayerCollection可以通过addImageryProvider方法将单个的imageryProvider添加到数组中，添加完成后会返回一个ImageryLayer类，ImageryLayer类代表瓦片图层，可以设置这个图层的属性如颜色、透明度等；
```bash
var layer = new ImageryLayer(imageryProvider);
layers.add(layer);
可以缩写成
var layer = layers.addImageryProvider(imageryProvider);
```

4. 将imageryProvider和ImageryLayer分离，可以更方便用户扩展新的图像服务提供者。

5. ImageryLayerCollection用来组织图层的叠加顺序，按照叠加的先后，图层一次放到最上层。这个集合类跟别的集合类一样，可以使用add，remove和get方法。另外，集合里的图层可以使用raise，raiseToTop，lower和lowerToBottom来控制顺序。


## 地形水域专题
Cesium支持流化和显示全球范围的高分辨率地形和水域特效如海洋、湖泊和河流。在cesium应用中可以查看山峰，山谷和其他的地形要素。使用cesium ion流化发布你自己的地形切片或者高分辨率平铺地形。地形图层和影像图层是分离的，任何影像图层都可以叠加在地形图层上。

cesium ion提供的全球地形数据包含了地形阴影和水面特效，默认情况下都没有开启，可以通过开启地形阴影和球体照明启用地形特效：
```bash
var viewer = new Cesium.Viewer('cesiumContainer', {
    terrainProvider : Cesium.createWorldTerrain({
        requestVertexNormals: true
    })
});
viewer.scene.globe.enableLighting = true;
```
通过requestWaterMask开启水面特效：
```bash
var viewer = new Cesium.Viewer('cesiumContainer', {
    terrainProvider : Cesium.createWorldTerrain({
        requestWaterMask: true
    })
});
```
水面特效会随着时间和太阳月亮反射的光波动。水面特效可以通过修改用于创建波浪的法线贴图Globe.oceanNormalMapUrl进行自定义。修改底图也会影响波浪的外观，因为波浪的颜色会跟底图的颜色混合。


## 矢量切片专题
矢量切片既保留动态绘制数据的前端可操作性，有保留了栅格大数据量分级分块的流式加载，最终可加载大数据量的动态数据。
在有大数据量geojson或者shp的情况下使用矢量切片是最佳方案。
cesium已经提供了大数据场景下的解决方案——3dTiles，name如果我们有很大数据量的shp，可以通过shp转为3DTiles实现和矢量切片服务一样的效果！
首先有跟cesium的分级分块对应拿到分片好的pbf数据，然后绘制显示，刷新时重新绘制。

shp转3dtiles
1. shp的坐标系要求？
2. 通过fme2018可以直接将shp转为3dtiles！！！赞
3. 使用cesiumlab工具可以成功转换


