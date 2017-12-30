---
title: 如何更快地开始使用puppeteer
date: 2017-12-25 19:56:21
tags: [nodejs]
keywords: puppeteer
categories: 前端相关
---

最近看了[大前端神器安利之 Puppeteer](https://jeffjade.com/2017/12/17/134-kinds-of-toss-using-puppeteer/)这篇文章之后，想要自己试试，本文记录了我在安装puppeteer过程中遇到的问题和解决方案。

<!-- more -->

### 使用npm和yarn ###

我一开始直接使用官方文档中说的:

```bash
yarn add puppeteer
# or "npm i puppeteer"
```

的方式来进行安装，但是却在一开始的安装阶段就遇到了麻烦，当我使用npm或yarn安装库时，由于puppeteer是使用的chromium内核，puppeteer会去国外的网站上下载最新版本的chromium，在windows下大约有110MB的大小，而且因为网络原因会下载得非常慢甚至无法下载，因此我尝试使用其他方法来安装chromium。

### 使用cnpm ###

在搜索了puppeteer的issues之后，发现了这个issues——[Failed to download Chromium r515411](https://github.com/GoogleChrome/puppeteer/issues/1597)，下面有人给出了更换chromium源或使用cnpm安装的方式，但是经过试验发现cnpm安装依旧很慢，而且使用这种方式安装的话，每次新建一个项目，都要重新下载100多MB的chromium，于是我继续尝试其他方法。

### 使用本地chromium ###

既然是每次都需要下载chromium，那么我们只要将puppeteer的chromium执行文件指定为本地文件就可以免除每次都重新下载的麻烦了。

**操作步骤：**

1.在[chromium的网站](https://www.chromium.org/getting-involved/download-chromium)上下载最新版本的chromium,将其安装到本地

2.在设置了环境变量`PUPPETEER_SKIP_CHROMIUM_DOWNLOAD`（作用是让puppeteer在安装过程中跳过chromium的安装步骤）之后，重新安装puppeteer：

```bash
set PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=1
npm i puppeteer
```

3.然后编写你的node代码：

```js
const puppeteer = require('puppeteer')

puppeteer.launch({
  executablePath: '在这里填写你本地的chromium文件地址'
})
```

这样，puppeteer就会去调用本地的chromium程序了。
