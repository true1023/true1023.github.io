---
layout: post
title: 如何用VUE2构建一个简单的单页应用(SPA) 上
tags: [JS, 教程, Vuejs]
excerpt_separator: <!--more-->
---

今天我们将学习如何用VUE构建一个简单的单页应用(SPA)

如果没有其他特殊声明，此教程中的VUE全部指的是VUE2.X版本

<!--more-->
{% raw %}

# 预览

让我们先看看最终的的单页程序是什么样子的

![成果](/assets/img/post/2018-07-29/1.gif "成果")

阅读本教程之前希望你能有如下的基础知识：

1. VUE基础
2. 如何创建VUE组件

如果你没有任何VUE或者VUE组件的知识，可以看我之前的文章

[VueJS简明教程(一)之基本使用方法](http://www.qinluo1023.com/2018/06/13/simple-vuejs-01.html)

# 使用Vue CLI 脚手架

我们将使用VUE提供的脚手架模块Vue CLI，它可以使我们构建的程序兼容ES5版本的浏览器。

> NOTE: 当然这需要你在Node.js环境下进行开发，如果你还没有Node.js和NPM的基本知识，建议你花半个小时的时间配置好Node.js环境，相信我，很简单，百度随便一搜就出来一大把教程。

如何你还没有安装Vue CLI，你可以用下面的命令进行安装

```bash
npm install -g vue-cli
```
> NOTE: vue-cli已经有了3.0版本，改名为 @vue/cli, 但是当前vue-cli还是可以使用的，因为大部分用户还是在用vue-cli，所以本教程也继续使用vue-cli作为教学。

安装完Vue CLI，我们将通过下面的命令构建我们的VUE项目。

```bash
vue init webpack spa
```

在上面的命令中，我们说明一下`webpack`和`spa`这两个参数.

Webpack是指你想用哪个脚手架模板，这里其实用很多模板供我们选择,比如简化版`webpack-simple`，具体使用方法可以参考[这里](https://github.com/vuejs/vue-cli)

SPA指的是你要把项目放在哪个文件夹里，这里就是SPA这个文件夹，如果没有系统会自动创建。

运行上面这个命令后，会有一些选项让你选择，比如项目的名字等等，全部默认就可以。

运行完上面的命令后，我们需要将当前路径改变到SPA这个文件夹内，然后安装需要的模块：

```bash
//改变路径到spa文件夹下
cd spa
//安装所有项目需要的npm模块
npm install
//在开发环境下运行程序
npm run dev
```

当上面的命令运行起来的时候，用浏览器访问 http://localhost:8080 这个地址，我们会看到如下页面：

![成果](/assets/img/post/2018-07-29/2.png "成果")

# VUE Router 的安装与配置

在我们正式开始配置我们的单页程序之前，还有一个东西必须安装，vue-router。

vue-router 是VUE官方提供的一个路由组件，专门用来构建VUE的单页程序，它包含如下一些特点：

1. 支持嵌套路由
2. 组件化路由配置
3. 支持路由参数传递，支持通配符
4. 比传统路由更加优化的导航控制
5. 自动激活不同CSS样式
6. 支持HTML5历史模式或哈希模式，支持IE9的自动回退功能
7. 支持自定义的滚动轴特性  

如果你之前接触过`angular router`或者`react-router`，其实`vue-router`跟这两个非常相似。

我们使用`vue-router`的原因就是它可以使我们的页面在前端就完成切换，而不是每次切换页面都要向服务器重新请求数据，重载页面。

换句话说，一个单页程序就像是一个安装在本地APP一样，使用起来比传统的网页更加的流畅。

我们可以用下面的命令安装`vue-router`.

```bash
npm install vue-router --save
```

现在让我们打开 src/main.js 文件夹把路由模块配置到我们的程序里.

复制下面的内容并替换原来的 src/main.js:

```javascript
// 导入vue实例
import Vue from 'vue'
//导入 App 组件
import App from './App'
//导入 vue router
import VueRouter from 'vue-router'
//告诉vue使用vue-router路由组件
Vue.use(VueRouter)
//定义路由表
const routes = []

// 创建路由器实例，并且传入`routes`变量作为路由。
// 你还可以传入别的参数，不过在这里尽量简单化就可以了
const router = new VueRouter({
  routes, 
  mode: 'history'
})
//实例化Vue实例
new Vue({
  //定义Vue绑定的跟元素
  el: '#app',
  //用<App/>代替根元素
  template: '<App/>',
  //声明App组件，这样上面的<App/>元素就可以生效
  components: { App },
  //将上面声明的路由器传递到根Vue实例
  router
}).$mount('#app')//将这个实例挂载到id=app的根元素上
```

让我们屡一下上面这一段代码，首先我们从node模块中导入了vue模块，接着我们又导入了App模块(这个是本地定义的模块)。

这个App模块是我们在用vue cli脚手架创建这个项目的时候默认帮我们创建的，作为我们整个项目的根模块。

之后，我们导入了vue-router, 接着我们使用vue的静态方法Vue.use，告诉vue我们将使用vue-router这个组件。

在下一行，我们定义了一个空的routes数组变量。每一个routes里面的元素为一条路由信息。当前里面还没有路由信息，后续会添加。

> NOTE：注意英文中routes和router的区别，router是一个组件，route是一条路由信息，routes是由多个路由信息(route)组成的路由表。
> 可以这么理解，把router当成一个工人，这个工人(router)拿到一张由很多路由信息(route)组成的路由表(routes)，然后每一次用户请求页面变化时，工人(router)会逐条比对路由表(routes)上的路由信息(route)，遇到第一条匹配的信息就将对应的页面反馈给用户

接下来我们做的是创建我们的路由器(router)，一个vue-router的实例，在这里我们给它传入了两个参数。

第一个参数是一个我们之前声明过的数组变量，路由表(routers)，另一个参数在声明一种路由模式，这种模式下我们在URL中不会看到前导的`#`号。
有兴趣的读者可以试试不加这个参数观察一下URL有什么变化。同时，去掉这个`#`号也会使SEO优化更加好。

最后，我们创建了一个Vue实例，传入了根组件`<App/>`，声明了Vue的挂载点。

现在，如果我们重新运行这个程序，会发现页面没有任何变化。下面我们将会开始设置路由。

我将会把路由管道标签(`<router-view></router-view>`)加入到根组件`<App/>`中，也就是当url符合某个路由信息(route)时，对应的组件就会被渲染(render)到这个路由管道标签内。

现在打开src/App.vue文件，也就是根组件文件，用下面的内容替换原有的内容：

```javascript
<template>
  <div id="app">
  <!--路由管道标签，任何符合某一路由(route)信息的组件都会在这个标签内展示出来 -->
  <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'app',
}
</script>
<!-- css格式 -->
<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

上面的代码和用vue-cli自动生成的代码有几点不一样：

1. 多了 router-view 标签用来渲染符合路由条件的组件
2. 删除了导入hello组件的语句`import hello`
3. 删除了`script`中关于component的定义
4. 删除了默认的component是因为我们不需要了，因为我们在上面已经定义了路由管道标签`<router-view>`

这时候我们在重新运行程序，会发现是空白页面

# 路由初始化

这时候我们需要定义`Hello`模块作为我们的Home页面，然后把相应的路由信息(route)加到路由表(routes)中

打开`main.js`文件，把`routes`变量修改成如下形式并导入Hello组件：

```javascript
//导入hello组件
import Hello from './components/Hello'
//定义路由表
const routes = [
//将根URL加入到路由表并声明对应Hello组件.
{ path: '/', component: Hello }
]
```

在上面的代码中我们引入了脚手架默认添加的Hello组件，并且将根URL `/` 对应到了Hello组件上，这时候如果我们重新运行程序，会像下面这样：

![成果](/assets/img/post/2018-07-29/3.png "成果")

上面这个图片里，Vue的logo已经不见了，这是因为之前在`APP`组件中已经将显示VUE logo图标的代码删除了。

现在，让我们定义更多的路由，在这之前先再创建一个组件。

在 src/somponents 文件夹下创建一个 About.vue 文件，然后把下面的代码放进去。

```javascript
<template>
  <div id="about">
    When you have a great story about how your product or service was built to change lives, share it. The "About Us" page is a great place for it to live, too. Good stories humanize your brand, providing context and meaning for your product. What’s more, good stories are sticky -- which means people are more likely to connect with them and pass them on.
  </div>
</template>

<script>
export default {
  name: 'about'
}
</script>
<style>
#about {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

让我们来看看上面这个About组件，这个组件里面只有简单的一段话，当URL正好对应这个组件的时候，它就会被渲染到`<router-view>`标签里面。

在这之前，我们需要在路由表中加入这个组件对应的路由。

打开 main.js 然后把路由表(routes)改成如下形式。

```javascript
//导入Hello组件
import Hello from './components/Hello'
//导入Aboiut组件
import About from './components/About'
//定义路由表
const routes = [
//对应Hello组件的路由地址
{ path: '/', component: Hello },
//对应About组件的路由地址
{ path: '/about', component: About }
]
```

上面代码唯一的改动就是增加了About组件的引入和对应的路由信息。

这时候如果我们在浏览器中访问 http://localhost:8080/about 这个地址，我们就会看到刚刚我们在About组件中写的那段话。

# 在页面中使用`<router-link>`标签

但是我们不能总是手工在浏览器中输入地址去切换页面，所以我们需要用到另一个路由器标签`<router-link></router-link>`。

打开 App.vue 文件，然后在`<router-view>`标签上面加入两个`<router-link>`标签：

```javascript
<router-link v-bind:to="'/'">Home</router-link>
<router-link v-bind:to="'/about'">About</router-link>
```

上面两个标签的作用就是创建两个指向对应URL的超链接，用来做动态的路由转换 (不用重载页面的页面转换)。

这时候如果你重新启动程序，会注意到页面上多了两个可以点击的超链接，点击任意一个，页面和URL也会随着变动，而且页面并没有重载。

程序看起来是这个样子的：

![成果](/assets/img/post/2018-07-29/4.gif "成果")

# 总结

好了，这就是如何构建一个单页程序(SPA)。

当然，这个程序也可以构建的更加复杂一些，比如：

1. 添加更多的路由
2. 通过路由传递参数信息
3. 使用路由守护(route guards)来在保证没有权限的用户不能访问特定页面

在下一个教程中，我会分别介绍路由的参数传递和路由守护功能。

[源代码](https://github.com/true1023/vue-tech/tree/master/vue03/spa)

[如何用VUE2构建一个简单的单页应用(SPA) 下](http://www.qinluo1023.com/2018/07/29/simple-vue-spa-02.html)

**码字不易，喜欢请点赞**
{% endraw %}