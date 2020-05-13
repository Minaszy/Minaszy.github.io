---
title: 通过代理解决angular项目里请求跨域问题
date: 2020-05-13 11:03:19
tags:
---
在angular项目里设置代理的步骤：
1. 在项目根目录下建立proxy.config.json文件；
2. 在该文件里配置代理路径等参数如下：
``` bash
{
    "/api": {
        "target": "http://minas/server/",
        "secure": false,
        "pathRewrite": { "^/api": "" },
        "changeOrigin": true,
        "logLevel": "debug"
    }
}
``` 
``` bash
{
    "/api": {
        "target": "https://scene.natural-resources.net.cn",
        "secure": true,
        "pathRewrite": { "^/api": "" },
        "changeOrigin": true,
        "logLevel": "debug"
    }
}
``` 
注意:
1. target是请求跨域地址的域名；
2. https请求需要把secure参数设置成true；
3. https请求需要把changeOrigin参数设置成true如果遇到ERR_TLS_CERT_ALTNAME_INVALID的问题，需要将"changeOrigin"设置为true；出现这个错误的的原因是：使用TLS时，对等方的主机名/ IP subjectAltNames与其证书中的任何主机名/ IP都不匹配。
问题定位:[https://nodejs.org/api/errors.html#errors_err_tls_cert_altname_invalid](https://nodejs.org/api/errors.html#errors_err_tls_cert_altname_invalid)


写好配置以后，需要将代理应用到项目上，有两种方式：
1. 在angular.json里添加："proxyConfig": "proxy.config.json"；
2. 在运行项目时添加：--proxy-config proxy.config.json；
<img src="proxy.jpg" width=50% />