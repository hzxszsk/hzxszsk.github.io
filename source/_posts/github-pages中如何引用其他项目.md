---
title: github pages中如何引用其他项目
date: 2016-08-21 20:57:56
categories: 技术文章
tags: [github,github pages]
keywords: github pages,引用GitHub项目
---

## 前言 ##

因为最近在学习React，学习慕课网上的React课程搭建了一个基于React的图片画廊，因为视频比较老，使用新版本工具时自己踩了不少坑，最后发布项目DEMO的时候折腾了很久，最后终于知道如何在github pages页面中引用别的项目的DEMO了（不知道如何创建github Pages的，可以去我的另一篇博客[hexo搭建记录](http://tianlajiangbula.com/2016/08/09/hexo搭建记录/#github)中查看），在此就把引用项目DEMO的过程做个记录。

<!-- more -->

## 创建DEMO的过程 ##

### 事前检查 ###

首先你要检查一下自己本地的DEMO能否正常运作，我指的是不用搭建本地服务器，可以直接在浏览器中查看

如果可以正常运作，则可以进入下一步，否则请把你的DEMO中的文件路径全部改为相对路径，这样应该就能在本地查看了

### 将DEMO上传 ###

1. 首先在终端中进入到你的项目目录

2. 创建一个新的gh-pages分支

		git branch gh-pages
		git checkout gh-pages

3. 将这个分支下的内容替换成你的DEMO内容

4. 将这个分支上传

		git add -A
		git commit -m "你的说明内容"
		git push -u origin gh-pages

5. 好了，现在你可以在你的github Pages页面下查看这个DEMO了，网址就是:

	https://你的账号名.github.io/你的项目名

	如果你的DEMO有index.html页面，则会直接跳转到index页面，若是没有，则需要手动添加跳转路径

### 更加简单的DEMO上传方式 ###

现代前端有着许许多多的脚手架工具，在复杂的项目目录中找到编译完后的内容重新创建DEMO可能会很麻烦，因此可以用更简单的一句命令来创建DEMO

	git subtree push --preifx=dist origin gh-pages

这句命令可以帮助你创建一个空的gh-pages分支并将某个文件夹下的内容放置到这个分支下。

其中，dist可以替换成你的DEMO所在的文件夹路径。

## 总结 ##

以上就是有关于使用gh-pages分支和github pages在github pages页面下引用其他项目的DEMO的方法，顺便放一下我使用这个方法完成的DEMO：

[基于React的图片画廊](https://hzxszsk.github.io/gallery-by-react)

[![基于React的图片画廊](http://obn5hqccb.bkt.clouddn.com/gallery-by-react-DEMO.png)](https://hzxszsk.github.io/gallery-by-react)

看到这篇文章的人或者其他遇到这方面困难的人，希望我的文章能够帮助到你。