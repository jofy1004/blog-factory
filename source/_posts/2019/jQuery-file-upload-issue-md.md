---
title: jQuery-file-upload坑爹记
premalink: jQuery-file-upload-issue.md
date: 2019-03-28 16:44:31
tags: [upload, 上传, fileupload]
categories: 疑难杂症
---
# 引言
{% link jQuery-File-Upload https://github.com/blueimp/jQuery-File-Upload%}，一款非常好用且功能强大的上传组件，各种上传必须的功能都支持，可以先看看{% link DEMO https://blueimp.github.io/jQuery-File-Upload/index.html %}，比如：
•	多个文件同时上传
•	文件拖拽
•	上传进度条
•	支持取消上传
•	支持大文件上传
•	支持客户端改变图片尺寸
•	支持预览
•	无需任何flash插件
•	支持跨域上传
•	一个页面可以有多个上传控件
•	支持标准的multipart/form-data
也许是我理解力差，也许是我英语二把刀，反正读官方给的{% link WIKI https://github.com/blueimp/jQuery-File-Upload/wiki %}来实现我的功能，着实费了一番大工夫，下面就来看看我踩得坑。

# 开始坑爹之路

## 引入后js报错

