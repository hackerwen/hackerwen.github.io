---
title: 发布react组件到npm总结
date: 2018-03-24
tags: [react]
categories: js
---

提测了公司最近一个需求，开始捣鼓我的小项目[music-react](https://github.com/worldzhao/music-react)，这次是要给她添加一个轮播图组件。

我超爱轮播图的。

2017年3月，我第一次接触js，对着《javascript dom编程艺术》后面的实战撸了一个轮播图，绝对定位配合定时器（或者是：css3/requestAnimationFrame），产生动画的效果。

看见自己的代码在chrome中动起来，真的很奇妙。

花了一天半重新用react的思维去编写轮播图以及发布到npm，也算是学到了不少知识。

好了，现在说发布react组件到npm上。

## 正文

我们要知道一点：

*发布到npm上的组件是经过编译的代码*

es6不行,jsx不行，stylus/sass/less都不行，你可以用gulp/webpack配合各种插件loader去编译你的源码，然后发布即可。

当然挫一点的就直接用命令行工具处理es6/jsx/sass/less/stylus等等。

这样满足了发布的前置条件，但是开发测试真的很不方便。

我难道要每次在一个已有的react项目中去开发调试组件，然后再新建一个项目去发布吗？

显然是很麻烦的，直到我看见了这一篇文章：
[如何创建一个前端 React 组件并发布到 NPM](https://www.jianshu.com/p/db6113c94dbc)

1. 通过配置webpack编译打包我们的源码 

2. 通过creat-react-app创建的项目去进行调试。

3. 中间通过npm link进行链接。

这篇文章的亮点就在于

```
npm link
```

这条命令，完美解决了开发过程中调试的问题。

### npm link

>开发NPM模块的时候，有时我们会希望，边开发边试用，比如本地调试的时候，require('myModule')会自动加载本机开发中的模块。Node规定，使用一个模块时，需要将其安装到全局的或项目的node_modules目录之中。<br>
> 对于开发中的模块，解决方法就是在全局的node_modules目录之中，生成一个符号链接，指向模块的本地目录。<br>
> npm link就能起到这个作用，会自动建立这个符号链接。

## 其他

```
npm install packagename --save-dev
```

```
npm install packagename --save
```

我们做项目时第一条命令是安装开发时需要的依赖，打包时会忽略这些依赖，比如各种loader。

而第二条命令安装的依赖会被一起打包。

我们做的项目是跑在浏览器上，所以在webpack配置中node_modules中通过第二条命令安装的包会被一起打包。

但是，我们组件是跑在别人的项目中的，我们不需要对任何依赖进行打包。

使用`webpack-node-externals`这个插件，忽略node_modules。

只打包我们的组件代码，并且告诉npm，这个组件依赖了哪些包即可["dependencies"]。
当其他使用者。

```
npm install react-tiny-swiper 
```

的时候，会一起将这些依赖安装。

参考资料：

1. [如何创建一个前端 React 组件并发布到 NPM](https://www.jianshu.com/p/db6113c94dbc)

2. [javascript标准参考教程：npm link](http://javascript.ruanyifeng.com/nodejs/npm.html#toc18)

安利我的项目：
1. [music-react](https://github.com/worldzhao/music-react)

2. [react-tiny-swiper](https://github.com/worldzhao/react-tiny-swiper)