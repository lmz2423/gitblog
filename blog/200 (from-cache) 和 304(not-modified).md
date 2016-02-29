<!--
author: 滇西之王
head: http://q.qlogo.cn/qqapp/100229475/C96DA226D9D07DECADAA54A78E9FEDF9/100
date: 2016-01-06
title: 200 (from-cache) 和 304(not-modified)
tags: http协议
category: http
status: publish
summary: 介绍了浏览器的两种缓存机制200(from cache) 304(not-modified)产生原因,以及服务端的设置
-->
为了提高网站的响应速度，我们通常将一些静态资源进行缓存，但是浏览器为了确定资源是否变更，总是会向服务器发送http请求，来判定静态资源是否发生变化（**图片**，**css文件**,**js文件**等），当静态资源没有发生变化的时候，服务器就会响应**304**。这的http请求，对于高访问量的网站来说，是一个值得优化的地方。
本文的目地就是，**怎么才能减少304响应?**。
### 浏览器怎么判断缓存是否可以使用
给大家上一个经常看到的图
![http-flow-view](http://www.alonehero.com/blog/img/first-request.png)
浏览器第二次请求，（假设在正常的操作下，不能在清除缓存的情况下操作）都是否使用缓存的判断。
![second-http-flow-view](http://www.alonehero.com/blog/img/second-http-request.png)。
我们从图中，大概知道304响应的原因，其实上面图片解释浏览器验证两缓存的两种机制**强缓存机制**和**协商缓存机制**。利用到http协议头中的
**“Cache-Control”,"Expires","Etag","Last-Modified"**  。**强缓存机制**利用**Cache-Control**和**Expires**来判断。**Cache-Control**的权重比**Expires**大。**协商缓存机制**利用**Etag**和**Last-Modified**来判断,**Etag**的权重更大。

###304响应的产生
既然要减少304响应，那第一个要了解的是，什么时候，服务器会返回**304响应**。这个就需要从http协议的web缓存验证机制讲起，在http1.1协议中，有一个ETag(entity tag)字段。ETag 是HTTP协议提供一种web缓存验证机制，该机制允许客户端进行缓存协商。304响应是协商缓存机制产生的原因。
当我们的web客户端，此处以浏览器为例，向web服务器（后台web服务为nginx）请求一张图片的时候，请求的协议如下：
![get请求](http://www.alonehero.com/blog/img/http-get.png)。
我们从响应头里可以看到，**ETag:5689f02b-358** 。如果我们点击浏览器的**刷新按钮**（以chrome为例，不强制清除）![chorme-f5](http://www.alonehero.com/blog/img/chrome-f5.png)。
返回的信息如下。
![get请求](http://www.alonehero.com/blog/img/http-304.png)
我们的请求头，会同时添加**If-None-Match:"5689f02b-358"**字段和**If-Modified-Since: Mon, 04 Jan 2016 04:08:11 GMT**发送给服务器，然后服务器校验一下对应的文件是否发生变化，那么为什么会发送两个字段呢？我们从**If-Modified-Since: Mon, 04 Jan 2016 04:08:11 GMT** 中可以看出该字段对缓存的控制精度在秒级，而假如我们的服务器文件变更的时间在毫秒之间，那通过这个字段来判断缓存是否更新，那是无法判断。但同时添加上**If-None-Match:"5689f02b-358"**字段，这样的话，字段里面带着上次请求返回的**Etag的值**。而Etag的值的产生的方法是基于文件内容的（不知道是否正确），颗粒度更精细，这样，服务器自己生成一个Etag，然后一比较就会知道服务器上的资源是否有更新。如果没有更新，那么返回一个304响应，这样**浏览器就利用存在客户端的缓存**。在chrome浏览器中查看缓存的的信息可以在地址栏中输入**chrome://view-http-cache**。如下图所示
![chrome浏览器查看缓存](http://www.alonehero.com/blog/img/view-http-cache.png) .
上述说的就是**http的协商缓存机制**
那么什么时候，不启用协商缓存机制，而启用**强缓存机制**，这个要看用户怎么去刷新页面了。下图给一个强缓存的图,这个是我在地址栏中输入地址，点击回车，返回的信息。
![from-cache](http://www.alonehero.com/blog/img/from-cache.png)
浏览器没有去服务器发送请求。

| **用户操作**  | **Expires** | **Last-Modified/Etag **  |
| --------|--------------------  |-----------------------|
| 地址栏回车 | 有效 | 有效 |
| 页面链接跳转 | 有效 | 有效 |
| 新开窗口 | 有效 | 有效 |
|前进、后退|有效|有效|
|F5刷新|**无效**|**无效**|
|Ctrl+F5刷新|**无效**|**无效**|
###减少304请求
这个我可以在后台服务器设置，如在nginx的设置，我们可以设置

```roboconf
 location ~ .*\.(jpg|png|gif|js|css|swf|flv|ico)$ { 
                 expires 12h; 
        } 
 location ~ .*\.(js|css)?$ {
               expires 12h;
        }
```
这样在浏览器第一次请求中，对应文件的响应头会出现**Cache-control: max-age= 43200**
```
HTTP/1.1 200 OK
Server: nginx/1.4.6 (Ubuntu)
Date: Wed, 06 Jan 2016 03:25:26 GMT
Content-Type: image/png
Content-Length: 1097
Last-Modified: Mon, 04 Jan 2016 04:08:10 GMT
Connection: keep-alive
ETag: "5689f02a-449"
Expires: Wed, 06 Jan 2016 15:25:26 GMT
Cache-Control: max-age=43200
Accept-Ranges: bytes
```
### 参考文档
[wikipedia-Http-Etag](https://zh.wikipedia.org/wiki/HTTP_ETag)<br/>
[HTTP头的Expires与Cache-control](http://www.cnblogs.com/yuyii/archive/2008/10/16/1312238.html)<br/>
[浏览器缓存机制](http://www.cnblogs.com/skynet/archive/2012/11/28/2792503.html)<br/>
[阿里云存储如何让浏览器始终以200 (from cache)缓存图片](https://www.zhihu.com/question/28725359)<br/>
[配置错误产生的差距：200 OK (FROM CACHE) 与 304 NOT MODIFIED ](http://div.io/topic/854)<br/>

