---
title: hexo操作教程
date: 2020-01-07 15:05:36
tags:
---
https://blog.csdn.net/lihangll/article/details/103335246
https://hexo.io/zh-cn/docs/commands

hexo clean
hexo generate/g
hexo serve/s
hexo d 

关于图片路径问题，更换电脑或者重新下载项目包后，打开/node_modules/hexo-asset-image/index.js，将内容更换为下面的代码
'use strict';
var cheerio = require('cheerio');

// http://stackoverflow.com/questions/14480345/how-to-get-the-nth-occurrence-in-a-string
function getPosition(str, m, i) {
  return str.split(m, i).join(m).length;
}

var version = String(hexo.version).split('.');
hexo.extend.filter.register('after_post_render', function(data){
  var config = hexo.config;
  if(config.post_asset_folder){
    	var link = data.permalink;
	if(version.length > 0 && Number(version[0]) == 3)
	   var beginPos = getPosition(link, '/', 1) + 1;
	else
	   var beginPos = getPosition(link, '/', 3) + 1;
	// In hexo 3.1.1, the permalink of "about" page is like ".../about/index.html".
	var endPos = link.lastIndexOf('/') + 1;
    link = link.substring(beginPos, endPos);

    var toprocess = ['excerpt', 'more', 'content'];
    for(var i = 0; i < toprocess.length; i++){
      var key = toprocess[i];
 
      var $ = cheerio.load(data[key], {
        ignoreWhitespace: false,
        xmlMode: false,
        lowerCaseTags: false,
        decodeEntities: false
      });

      $('img').each(function(){
		if ($(this).attr('src')){
			// For windows style path, we replace '\' to '/'.
			var src = $(this).attr('src').replace('\\', '/');
			if(!/http[s]*.*|\/\/.*/.test(src) &&
			   !/^\s*\//.test(src)) {
			  // For "about" page, the first part of "src" can't be removed.
			  // In addition, to support multi-level local directory.
			  var linkArray = link.split('/').filter(function(elem){
				return elem != '';
			  });
			  var srcArray = src.split('/').filter(function(elem){
				return elem != '' && elem != '.';
			  });
			  if(srcArray.length > 1)
				srcArray.shift();
			  src = srcArray.join('/');
			  $(this).attr('src', config.root + link + src);
			  console.info&&console.info("update link as:-->"+config.root + link + src);
			}
		}else{
			console.info&&console.info("no src attr, skipped...");
			console.info&&console.info($(this));
		}
      });
      data[key] = $.html();
    }
  }
});
————————————————
版权声明：本文为CSDN博主「Ericam_」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/xjm850552586/java/article/details/84101345

##20160701-20200401

##前端技术栈
javascript、nodeJs
##数据库
mongodb、postgresql、mysql、arcSDE
##GIS框架
arcgis、cesium、openlayers、leaflet、mapbox

##博客
github

##技能
熟悉
JavaScript、ArcGIS API for JavaScript、openlayers、leaflet、mapbox、cesium
工作需要
NodeJS、Agular、ionic、MongoDB、Git

##项目
水利、国土相关
web、大屏、APP（混合式开发）

