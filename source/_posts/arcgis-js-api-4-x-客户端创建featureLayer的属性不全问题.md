---
title: arcgis js api 4.x 客户端创建featureLayer的属性不全问题
date: 2020-05-13 15:15:57
tags:
---
在arcgis js api 4.x中，除了通过featureserver服务创建featureLayer以外，还可以在客户端动态创建featureLayer。客户端创建需要满足以下几个条件：
1. 以下爱几个字段是必须的：source（graphic要素数组）fields （指定图层要素的属性）；
2. 单个featureLayer里的空间数据格式只能存在一种；
3. 数据属性里必须存在ObjectID属性，在字段可以通过objectIdField属性单独指定，也可以放在fields数组里；

这里通过客户端创建featureLayer后，发现在客户端要素交互时只能拿到两个属性ObjectID和name，经过排查，发现可以通过设置字段outFields: ["*"]让图层携带所有属性字段，设置后再点击获取到的要素里就可以看到所有属性信息了。
```bash
const layer = new FeatureLayer({
          source: graphics, 
          fields: [{
            name: "ObjectID",
            alias: "ObjectID",
            type: "oid"
          }, {
            name: "projectId",
            alias: "projectId",
            type: "string"
          }, {
            name: "name",
            alias: "name",
            type: "string"
          }],
          outFields: ["*"]
        });
```
