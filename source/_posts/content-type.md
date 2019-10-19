---
title: content-type
date: 2018-02-25 19:59:16
tags: http
---


> content-Type 实体头部用于指示资源的MIME类型 media type 。

> 多用途Internet邮件扩展（MIME）类型 是一种标准化的方式来表示文档的性质和格式。

> 浏览器通常使用MIME类型（而不是文件扩展名）来确定如何处理文档；因此服务器设置正确以将正确的MIME类型附加到响应对象的头部是非常重要的。

类型 | 描述| 典型示例
---|---|---
text | 表明文件是普通文本，理论上是可读的语言| text/plain, text/html, text/css, text/javascript
image | 表明是某种图像。不包括视频，但是动态图（比如动态gif）也使用image类型| image/gif, image/png, image/jpeg, image/bmp, image/webp
audio | 表明是某种音频文件	| audio/midi, audio/mpeg, audio/webm, audio/ogg, audio/wav
video | 表明是某种视频文件	| video/webm, video/ogg
application	 | 	表明是某种二进制数据| application/octet-stream, application/pkcs12, application/vnd.mspowerpoint, application/xhtml+xml, application/xml,  application/pdf,application/json



### post 常用设置
1. application/x-www-form-urlencoded 是常用的表单发包方式，普通的表单提交，或者js发包，默认都是通过这种方式

2.multipart/form-data  用在发送文件的POST包。