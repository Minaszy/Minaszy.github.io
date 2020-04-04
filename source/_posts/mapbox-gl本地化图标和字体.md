---
title: mapbox-gl本地化图标和字体
date: 2019-11-02 14:49:31
tags: mapbox
categories: mapbox
---
首先，我们来看一下mapbox-gl官网图标和字体的说明：
{% asset_img sg.jpg mapbox-gl官网图标和字体的说明 %}
<!--more-->
### sprite
要求参数是可以获取sprite图片和元数据的基本url，扩展名为.png和.json的文件和比例因子@2x.png将自动请求。如果有任何图层使用了background-pattern，fill-pattern，line-pattern，fill-extrusion-pattern或者icon-image属性，则必须使用sprite参数。这个url必须是绝对路径，包含 scheme, authority 和 path components。
### glyphs
要求参数是一个用来加载PBF格式 signed-distance-field 字形的url模板。该url必须包含{fontstack}和{range}令牌。如果有任何图层使用了text-field属性，则必须使用glyphs参数。这个url必须是绝对路径，包含 scheme, authority 和 path components。

在没有进行本地化的情况下，直接采用官网样式'mapbox://styles/mapbox/streets-v11'，可以在浏览器后台看到以下请求：
https://api.mapbox.com/styles/v1/mapbox/streets-v11/sprite.json
{% asset_img spjson.jpg mapbox-gl官网图标和字体的说明 %}

https://api.mapbox.com/styles/v1/mapbox/streets-v11/sprite.png
{% asset_img sprite.png mapbox-gl官网图标和字体的说明 %}

https://api.mapbox.com/fonts/v1/mapbox/DIN Offc Pro Medium,Arial Unicode MS Regular/0-255.pbf

前两个本地化只要把sprite.json和sprite.png下载到本地web服务器或者自制一套css sprites精灵图；字体本地化则需要使用mapbox提供的字体转换工具node-fontnik，将我们常用的tff字体文件转换成pdf格式。自制精灵图和转换tff后面单独讲，现在贴出一个本地化后的简单效果demo，包含demo里图标和字体：
demo项目代码: [https://github.com/Minaszy/mapbox-gl-offline](https://github.com/Minaszy/mapbox-gl-offline)
{% asset_img demo.jpg mapbox-gl官网图标和字体的说明 %}
