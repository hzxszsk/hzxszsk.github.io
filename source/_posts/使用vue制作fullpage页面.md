---
title: 使用vue制作FullPage页面
date: 2017-04-05 00:11:29
tags: [vue,前端,fullpage]
keywords: vuejs,fullpage
categories: 前端相关
---

## 前言 ##

已经有好久没有更新博客了，大三下了要准备找实习了，才发现自己很多东西都不会，所以赶紧找了个现在流行的MVVM框架学习一下。我学习的是Vue，所以拿Vue写了一个FullPage的模板，可以供自己和其他人使用。

项目的地址是：[https://github.com/hzxszsk/vue-fullpage](https://github.com/hzxszsk/vue-fullpage)

[演示效果在这里](http://hzxszsk.github.io/vue-fullpage)

<!-- more -->

## 项目讲解 ##

为了加深理解，我把我制作这个FullPage页面的思路和流程记录下来，也可以给其他和我一样的初学者一个参考。

### 分解思路 ###

首先，我根据Vue的组件开发思想，把这个FullPage页面分为两个主要的组件模块：页面组件（Page）和页面控制组件（PageController）。除了这两个组件模块之外，还有一个整合的App.vue文件。

其中，Page组件主要负责每个FullPage页面的样式和位置控制，而PageController组件则用来发起页面的切换请求。

因为Vue是单向数据流形式的，因此将所有需要的数据都定义在App.vue文件中，由App组件向下传递数据。

### 数据需求分析 ###

要制作一个FullPage页面，每个Page页面需要的是页面自身的内容（页面的内容和样式配置），而控制器所需要的数据有当前正在显示的页面以及总的页面数，用这两个属性计算出其他需要的属性。

其中，总页面数即是Page总数，有多少个Page，就有多少个Page的配置，因此可以通过计算Page配置信息对象的数量来获得总页面数。

我在App.vue的组件data中定义了两个属性：

- currentPage表示的是当前页面的序号（从1开始计算）；
- options是一个数组，其中的每一个对象表示的是对应序号的页面的配置信息，可以通过修改里面的对象属性从而改变对应Page的样式。

Page组件只需要知道当前页面是第几个页面和自身的配置，因此只含有两个属性：

- currentPage
- option 表示自身的配置

PageController需要对页面进行切换控制，因此需要两个属性：

- currentPage
- pageNum 表示总的页面数量

### 编写页面样式 ###

**App组件的结构为：**

``` html
	<div id="app" class="app">
		<!-- page为单独的页面组件，page内可以编写任意的页面内容 -->
		<page :currentPage="currentPage"></page>
		<!-- page-controller为控制器组件 -->
		<page-controller :pageNum="pageNum" :currentPage="currentPage" @changePage="changePage" :arrowsType="arrowsType"></page-controller>
	</div>
```

**App组件主要样式：**

``` css
	/* 页面宽高为100%，overflow为hidden隐藏溢出部分 */
	html,body {
	  margin: 0;
	  padding: 0;
	  height: 100%;
	  width: 100%;
	  overflow: hidden;
	}
	.app {
	  height: 100%;
	  width: 100%;
	}
```

**Page组件结构为：**

``` html
	<section class="page" v-if="options"
	:style="{background:options.background,color:options.color||'#fff'}"
	:class="{'page-before': options.index < currentPage,'page-after': options.index > currentPage}">
	    <div :class="{'page-center': options.isCenter}">
	        <slot></slot>
	    </div>
	</section>
	<section class="page" v-else>页面正在渲染中。。。</section>
```

其中slot内为在App.vue的page标签内编写的html内容，并且根据Page组件内的option属性，为Page页面添加不同的样式（包括背景颜色、字体颜色、居中等，而且可以自由扩展）

**Page组件的主要样式为：**

``` css
	.page {
	    position: absolute;
	    width: 100%;
	    height: 100%;
	    transition: all 0.5s ease 0s;
	}
	.page-before {
	    transform: translate3d(0,-100%,0);
	}
	.page-after {
	    transform: translate3d(0,100%,0);
	}
```

通过比较当前页面的index与currentPage的大小，可以判断Page组件处于之前的页面，当前的页面和之后的页面三个状态中的哪一种。

将所有页面都用absolute定位在同一个位置上，对于处于之前的页面的Page，添加page-before类标签，使其在Y轴上向上偏移自身高度距离，同理，对之后的页面做Y轴上向下的自身高度偏移。

在page标签上添加transition样式，使其在切换时可以产生动画效果。

**PageController组件的结构为：**

``` html
	<nav class="controller">
		<button v-if="arrowsType != 'no'" class="prev-btn" :class="{moving:arrowsType === 'animate'}" @click="changePage(prevIndex)"></button>
		<ul>
		    <li @click="changePage(index)" v-for="index in pageNum" :key="'controller-'+index" class="controller-item"></li>
		</ul>
		<button v-if="arrowsType != 'no'" class="next-btn" :class="{moving:arrowsType === 'animate'}" @click="changePage(nextIndex)"></button>
	</nav>
```

PageController组件中分为两块，控制上下滚动的上下箭头按钮和控制所有页面的ul列表。

因为PageController中的样式较多，因此不在文章中详细说明，只做简单介绍。

button和ul标签用fixed定位，使其处于整个页面的上方、下方和右侧。

button使用rotate和border属性，制作出箭头的样式，并添加对应的动画效果（可以通过传递props选择关闭）。


### 编写页面逻辑 ###

#### 切换页面逻辑 ####

因为所有的页面切换都是由PageController发起，而控制currentPage的组件并不是PageController，所以需要有一个父子组件事件，由子组件PageController发起，传递一个参数表示要切换到第几个页面，因此在PageController中定义一个method：

``` javascript
	changePage (index) {
		this.$emit('changePage', index);
	}
```

父组件接受该事件并调用自己定义的changePage方法，修改自身的currentPage属性

``` javascript
	changePage (index) {
		// 改变page
		this.currentPage = index;
	}
```

#### 箭头按钮上下滚动 ####

为了实现上下滚动，需要知道当前页面的前一个页面和后一个页面分别是第几个页面，因此可以使用计算属性，计算出前一个和后一个页面的index值：

``` javascript
	// PageController.vue
	nextIndex () {
		if (this.currentPage === this.pageNum) {
			return 1;
		} else {
			return this.currentPage + 1;
		}
	},
	prevIndex () {
		if (this.currentPage === 1) {
			return this.pageNum;
		} else {
			return this.currentPage - 1;
		}
	}
```

在点击箭头时，将对应的nextIndex或prevIndex值当做参数传给changePage方法。

#### 滚轮滚动和移动端滚动 ####

滚轮滚动和移动端滚动主要依靠window的监听事件，根据传入的event属性，计算出页面是应该向上还是向下滚动，将需要滚动的方向作为参数传递给处理函数handler。

因为代码略长，因此不全部显示在文章中，只显示处理函数相关逻辑

``` javascript

	let _this = this;
	let timer = null;
	function scrollHandler (direction) {
		// 防止重复触发滚动事件
		if (timer != null) {
			return;
		}
		if (direction === 'down') {
			_this.changePage(_this.nextIndex);
		} else {
			_this.changePage(_this.prevIndex);
		}
		timer = setTimeout(function() {
			clearTimeout(timer);
			timer = null;
		}, 500);
	}
```

需要注意的一点是，移动端做滚动判断时，要求touches和changedTouches之间需要一定的间隔，不然容易误触发滚动事件。

#### options属性的分发 ####

为了使使用者更加方便地编写页面内容而不在意具体的页面序号，我采用了自动对page内的option赋值的方法。其实现原理是在App.vue文件中，使用钩子函数mounted，对page中的option属性进行设置。

``` javascript
	mounted () {
		this.$children.forEach((child, index) => {
			// 动态设置各个page内的options
			if (child.option === null) {
				let childOption = this.options[index];
				this.$set(childOption,'index',index+1);
				child.option = childOption;
			}
		});
	}
```

#### 高级属性：新的钩子函数 ####

为了满足部分使用者的需求，我在设置了两个钩子函数：beforeLeave和afterEnter。

这两个钩子函数可以设置在对应页面的options属性对象中，并且含有一个默认的参数，为对应页面的page组件实例对象。

其实现方式为在原先的changePage函数（App.vue）内添加新的逻辑：

``` javascript
	changePage (index) {
		// beforeLeave
		let beforeIndex = this.currentPage - 1;
		let leaveFunction = this.options[beforeIndex].beforeLeave;
		typeof leaveFunction === 'function' && leaveFunction.call(this,this.$children[beforeIndex]);
		// 改变page
		this.currentPage = index;
		// afterEnter
		let nextIndex = index-1;
		let enterFunction = this.options[nextIndex].afterEnter;
		this.$nextTick(function () {
			typeof enterFunction === 'function' && enterFunction.call(this,this.$children[nextIndex]);
		})
	}
```

## 总结 ##

这篇文章记录了我开发一个FullPage页面的总体流程，将主要的逻辑重新顺理了一遍，还有一些小的细节没有写在文章中，有兴趣的可以去[具体的项目页面](https://github.com/hzxszsk/vue-fullpage)看源码，当然您要是觉得写得还可以，希望给个star(*^__^*) ，如果您有什么想法，欢迎给我留言或加我的QQ私聊。