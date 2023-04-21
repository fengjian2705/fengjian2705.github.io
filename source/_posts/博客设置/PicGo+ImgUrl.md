---
title: PicGo+ImgUrl 图床
tags:
    - PicGo
    - ImgUrl
    - 图床
index_img: https://s3.uuu.ovh/imgs/2022/12/09/2ca0c42ade0e945c.png
# excerpt: JDK 动态代理
categories:
    - 博客
---

> PicGo是一款跨平台图片上传客户端，支持Windows、Linux、MacOS操作系统，支持将图片上传到多个目标，比如ImgURL、SM.MS等图床。

## 安装PicGo

1. 前往Github：https://github.com/Molunerfinn/PicGo/releases 根据你的平台下载最新版本安装。  
2. 前往Node.js官网：https://nodejs.org/zh-cn/ 下载最新版Node.js安装（PicGo插件需要） 
3. 退出PicGo并重新打开，在插件设置中搜索“web-uploader”找到下面这个插件进行安装  
![web-uploader](https://img.rss.ink/imgs/2022/04/08/7a96e13c5c3519ce.png)

## 获取ImgURL API
1. 首先您需要在ImgURL注册一个账号：https://www.imgurl.org/vip/user 亦或者其它ImgURL Pro站点均可。
2. 注册完毕并登录后在ImgURL用户后台找到API地址/UID/Token三个参数并记录，稍后需要使用
![PicGo参数](https://img.rss.ink/imgs/2022/04/07/b93817857013e273.png)

## 设置PicGo

打开PicGo - 图床设置 - 自定义Web图床，填写上一步获取到的API信息，如下图:

![设置PicGo](https://img.rss.ink/imgs/2022/04/08/2b47bcaf29f2c2f1.png)

* API地址：填写ImgURL的API地址，比如：http://imgurl.rss.ink/api/v2/upload
* POST参数名：填写file
* JSON路径：填写data.url
* 自定义Body：填写下面的json
```shell script
{"uid":"your uid","token":"your token"}
```
1. your uid：改成你在ImgURL获取到的UID
2. your token：改成你在ImgURL获取到的Token

然后点击确定进行保存，同时你也可以将其设置为默认图床。


