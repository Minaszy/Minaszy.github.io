---
title: ArcGIS_API_for_Javacsript_4.13功能笔记
date: 2019-11-19 14:45:18
tags: arcgisApi
categories: arcgisApi
---

###坐标系支持
https://developers.arcgis.com/javascript/latest/api-reference/esri-views-SceneView.html#viewing-modes
https://developers.arcgis.com/javascript/latest/api-reference/esri-WebScene.html

There are two types of web scenes: global or local. Global scenes render the earth as a sphere while Local scenes render the earth on a flat plane. Global web scenes can be created with a WebMercator or supported GCS spatial reference: WGS84, or CGCS2000. Local web scenes can be created with any projected coordinate system. Read the ArcGIS Online help to learn more about choosing global or local scenes.

###服务发布问题
<!--more-->
1. 使用arcgis pro发布3857的矢量切片通过sceneview预览不了，加载不出来。
描述：发布4326/3857的普通切片通过sceneview可以预览、加载；
      发布4326的矢量切片通过sceneview可以预览、加载；
      发布3857的矢量切片通过sceneview预览不了，加载不出来；目前定位问题出在发布时数据框坐标系与数据源坐标系不一致；
原因：去切片根目录查看发现，切片方案选的是pro默认推荐的，选择切了十几级但是缓存目录下仅有一级矢量切片。
解决方案：在切片目录中替换正常服务的两个root.json后，可以正常预览，但是其他级别出现模糊数据缺失等情况。
2. arcgis pro发布要素服务失败。
描述：上海市区划数据可以正常发布要素服务，但是河道数据一直发布失败。
原因：由于属性里有"长度（"和"宽度（"。
解决方案：修改数据性字段名称。
3. arcgis pro发布矢量切片服务简化严重。

4. arcgis pro发布要素服务简化严重。

###前端api portal服务权限授权
1. portal的地址：https://wer.bimviewer.net/arcgis/home/；
2. arcgis api未授权情况下直接添加portal服务时会弹出portal登录框；
3. portal生成token的地址：https://wer.bimviewer.net/arcgis/sharing/rest/generateToken；
4. generateToken api教程地址：https://developers.arcgis.com/rest/users-groups-and-items/generate-token.htm；
5. 授权代码：
      "esri/identity/IdentityManager"
      "esri/identity/ServerInfo"

      portalUrl: 'https://wer.bimviewer.net/arcgis',
      userInfo: {
        username: 'gis_viewer',
        password: 'kalends123'
      },
      serverInfo: {
        adminTokenServiceUrl: 'https://wer.bimviewer.net/arcgis/sharing/rest/generateToken',
        currentVersion: '10.7',
        // server: 'https://wer.bimviewer.net/arcgis',
        shortLivedTokenValidity: '60',
        tokenServiceUrl: 'https://wer.bimviewer.net/arcgis/sharing/rest/generateToken',
        "server": 'https://wer.bimviewer.net/arcgis/sharing/rest'
      },

      esriConfig.portalUrl = this.mapConfig.arcMapConfig.portalUrl;
        const serverInfo = new ServerInfo(this.mapConfig.arcMapConfig.serverInfo);
        esriId.generateToken(serverInfo, this.mapConfig.arcMapConfig.userInfo).then((res) => {
          console.log(res);
          if (res) {
            esriId.registerToken({
              "server": this.mapConfig.arcMapConfig.serverInfo.server,
              "token": res.token,
              "expires": res.expires,
              "ssl": true
            });
          }
        }).catch((err) => {
          console.log()
          alert(JSON.stringify(err));
        });

###api使用注意点总结
1. 矢量切片mapView可以通过样式修改高亮，sceneView修改样式后需要手动触发重新渲染。
2. 本地部署后字体文件类型跨域；


###目前已使用api实现功能
1. 加载高德地图作为底图或者图层；
2. 加载arcgis pro发布的图片切片服务、矢量切片服务、要素服务、动态图层服务等；
3. 加载arcgis pro发布的点云服务、bim模型服务、i3s服务，并控制模型图层显隐；
4. 二维测距和三维测距功能；
5. globe模式下的聚合功能；
6. 河道点击高亮功能，通过点击事件查询要素服务重新叠加高亮要素实现；
7. 点击要素弹窗功能；
8. 开启、关闭地下模式；


web scene不支持的图层类型
It's important to note that GraphicsLayer, ImageryLayer and StreamLayer can't be saved to a web scene. A cached image service can be saved to a web scene, by declaring it as a TileLayer. An OpenStreetMapLayer can be saved as a baseLayer. For more information on types of layers that can be saved in a web scene, see the web scene specification.

sceneview 和 webscene的区别


### 水面升降功能

水面模型：
服务的形式：
1. featureLayer上render设置水面效果，首先需要发布一个面的featureserver；
2. 把水面制作成多面体模型，发布成服务（可编辑的三位服务），然后加载；
模型文件的形式：
3. 将水面模型转为gltf格式，客户端创建graphic，加载水面模型；
客户端：
4. 在客户端创建一个featureLayer，渲染水面效果，其他同1；

模型升降思路：
1. 之前做的无人船轨迹播放，通过set空间数据的geometry，即时修改了位置，换成修改z值，应该也可以实现z轴上的平滑移动；
2. 在编辑3d服务的例子里，选中三位服务的某个模型后，出现编辑工具，可以操作平滑的拖动模型上下移动；但是必须是使用api提供的工具，代码里找不到暴露的操作接口；不知道他这里的移动是什么样的思路。。。
3. 在可编辑三维服务里，有暴露编辑事件，通过编辑方法更新要素的z值，可以实现移动；已经测试成功，可以移动模型高度，但是请求更新需要时间，出现停顿，这种方案适用于一步到位的更新操作，如设置一定高度值，不适用于平滑移动的需求；

#### 最终解决方案：
即时改变图层的elevationInfo可以实现想要的效果，注意：
1. 是要给图层的elevationInfo重新赋值，直接改变elevationInfo里的offset是不行的；
2. 重新赋值的内容是自定义要素高度featureExpressionInfo，有这个参数时会代替Z值取显示模型的高度；
balloonLayer.elevationInfo = currentElevationInfo;