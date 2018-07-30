---
layout: post
title: 三十分钟学会使用VUE搭建单页应用(SPA) 下
tags: [JS, 教程, Vuejs]
excerpt_separator: <!--more-->
---

在上一章中，我们学习了用VUE2构建简单的单页应用 (SPA)。

在本章中，我们将要学习：

1. 通过路由传递参数
2. 使用路由守卫 (route guards)使未授权的用户不能访问某些特殊页面

我会在[上一章](http://www.qinluo1023.com/2018/07/29/simple-vue-spa-01.html)写好的程序上继续添加功能，你可以直接在我的github上克隆上一章的[源代码](https://github.com/true1023/vue-tech/tree/master/vue03/spa)

<!--more-->
{% raw %}

# 预览

我们会在[上一章](http://www.qinluo1023.com/2018/07/29/simple-vue-spa-01.html)已经写好的程序上加入路由参数传递和路由守护两个功能。


![成果](/assets/img/post/2018-07-30/1.gif "成果")



# 通过路由传递参数

在VUE中，我们可以通过在某条路由信息 (route,希望你们还能记得route和router和routes的区别，我在上一章说过的) 中添加类似 `:id`的属性来传递参数。

这种路由参数传递机制在Laravel也有。

每一条路由信息都由一对大括号括起来，就像下面这样

```javascript
{ path: '/user/:id', component: User } 
```
像上面这条路由信息，如果我在地址栏输入`/users/2`，那么在目的组件中 (在这里就是User组件)，我们就可以通过`$route.params.id`来得到2这个整数。

其实就是把2赋值给了一个变量id， 而这个变量id则定义在`$route.params.id`中。

下面来实际操作一下。

# 创建一个带参数的路由

在 `scr\components`文件夹下创建新的组件文件`Param.vue`，并且填入下面的内容:

```html
<!--src\components\Param.vue-->
<template>
	  <div class="param">
	   <input type="text" size="30" v-model="UserInput" />
	   <input type="button" value="Go to route" @click="GoToRoute()">
	  </div>
</template>
	
<script>
	export default {
	  name: 'Param',
	  data () {
	    return {
	    	UserInput :''
	    }
	  }, 
    methods:{
	    	GoToRoute : function(){
	    		this.$router.push({ name: 'Paramdetails', params: { id: this.UserInput }})
	    	}
	  }
	}
</script>
```

上面这段代码我们定义了一个 `param` 组件， 这个组建中有一个 `input` 输入框和一个导航按钮，这个导航按钮会触发 `GoToRoute` 函数。

接着我们打开`main.js`文件，把上面的`Param.vue`组件导入，并且创建一个与之对应的路由信息。

就在 `Const routes` 语句上面，加上下面这句话:

```javascript
//main.js
import Param from './componets/Param'
```

然后把 `routes` 替换成如下内容：

```javascript
//main.js
//定义路由表
const routes = [
//主页路由
{ path: '/', component: Hello },
//about页面路由
{ path: '/about', component: About }, 
//param页面路由
{ path: '/param', component: Param }
]
```

可以注意到上面这个代码块中，我仅仅是添加了一句话，一句param的路由信息。

在 `App.vue` 文件中加入对应 `Param` 模块的超链接点击 `<router-link></router-link>`，就在 `About` 模块的超链接点击之后。

```html
<!--src\components\App.vue-->
<router-link v-bind:to="'/param'">Param Link</router-link>
```

现在运行程序，我们的主页应该像是下面这个样子：

![成果](/assets/img/post/2018-07-30/2.png "成果")

现在紧接着 "about" 之后就是 `param link` 的超链接按钮，点击它，你会看到下面的页面

![成果](/assets/img/post/2018-07-30/3.png "成果")

点击F12进入开发者工具，然后点击 `Go to route` 的按钮，在开发者工具栏中会出现下面的错误

```bash
  [vue-router] Route with name 'Paramdetails' does not exist
```

这是因为当我们点击 `Go to route` 按钮的时候触发了 `this.$router.push({name: 'Paramdetails', params: { id: this.UserInput }})`这个方法，但是我们还没有定义任何关于 `paramdetails` 的路由。

所以下一步，我们就要创建一个关于 `paramdetails` 的组件和与之对应的路由信息。

在 `src/components` 文件夹中创建 `paramedetails.vue` 文件，然后把下面的内容粘贴进去。

```html
<!--src/components/paramdetails.vue-->
<template>
  <div class="paramdetails">
	   <span>The paremter value that was passed to me is: {{ $route.params.id }}</span>
  </div>
</template>
	
<script>
export default {
	  name: 'paramdetails'
}
</script>
```

这个组件唯一的作用的就是把路由传过来的参数显示在页面上。

接着，在 `main.js` 中引入这个组件，就在 `Const routes` 这句话上面写：

```javascript
//main.js
//import paramdetails component
import paramdetails from './components/paramdetails'
```

把 `routes` 替换成下面的内容：

```javascript
//main.js
//定义路由表
const routes = [
    { path: '/', component: Hello },
    { path: '/about', component: About }, 
    { path: '/param', component: Param },
    //定义可以传参的路由信息
    { path: '/Paramdetails/:id', component: paramdetails, name: 'Paramdetails' }
]
```

上面这段代码加入了对 `paramdetails` 组件的路由信息，并且路由信息中加入对参数 `:id` 的引用。

另外，还加入了另外一个参数 `name`， 关于 `name` 参数，其实它是为了我们更加方便的记忆，这样在切换路由的时候就不需要输入完整的URL地址，取而代之用 `name` 定义的名字就可以正确导航到对应的路由。

可以回过头看看上面我们是怎么定义 `GoToRoute` 这个方法的，在 `param.vue` 文件中。

我们在 `this.$router.push({name: 'Paramdetails', params: { id: this.UserInput }})` 中就通过使用 `name` 参数来简化路由的导航。

现在，我们重新运行程序，点击 `param link` 导航到 `param` 组件，然后在输入框中输入52， 接着点击 `go to route` 按钮， 你将会看到下面的页面:

![成果](/assets/img/post/2018-07-30/4.png "成果")

好了！你已经成功的通过路由把参数传递过来了，恭喜！

# 使用路由守护 (route guards)

路由守护我更愿意把他看成是一种钩子方法，简单说就是他可以在路由没有跳转之前 (before) 和已经跳转之后 (after) 分别执行一些动作 (action)。

路由守护分为全局守护和组件守护两种，顾名思义，全局守护会检查每一次路由跳转，组件守护只会在当前组件内部出现路由跳转时触发。

下面我们将在 `main.js` 文件中添加全局路由守护 (beforeEach)。

打开 `main.js` 文件， 紧挨着 `routes` 常量下面加入下面的内容：

```javascript
//main.js
//设置路由守护
router.beforeEach((to, from, next) => {
    //检查这个跳转是否是要跳转到 param 组件
    if(to.path == '/param'){
        //如果'user' 对象还未赋值
        if(localStorage.getItem('user')==undefined){
            //提示输入用户名
            var user = prompt('please enter your username');
            //提示输入密码
            var pass = prompt('please enter your password');
            //检查用户输入的用户名密码是否和我们预设的一样
            if (user == 'username' && pass == 'password'){
              //赋值user对象
              localStorage.setItem('user', user);
              //跳转到对应的组件
              next();
            }else{//
              //如果用户名密码不正确则显示错误信息
              alert('Wrong username and password, you do not have permission to access that route');
              //返回并且不进行路由跳转
              return;
            }
        }
    }
    //如果要跳转的路径不是 param 组件，则不进行检查直接跳转
    next()
})
```

上面的 `beforeEach` 方法有三个参数, `to` 参数代表跳转目的地对象, 而 `from` 这是代表原目的地对象。

在 `beforeEach` 方法中，首先检查 "to.path" 是否等于 `/param`， 如果是， 则检查 "[localStorage](http://www.runoob.com/html/html5-webstorage.html)" 数据中是否已经存有用户 (user) 的信息。

如果没有，则弹出提示框要求输入用户名和密码，如果正确就创建 "localStorage" 本地存储，用户下次再访问同样的页面就别用重复输入用户信息。

> NOTE: next 方法用来跳转到用户将要跳转的页面，是一个必须调用的方法。 如果不调用此方法，那么用户将不能进行路由跳转。所以在上面的例子中如果用户输入错误，则直接警告并且返回，没有调用 next 方法。上面只是一个最简单的例子，只能作为理解使用，千万不要应用到实际开发中。


# 总结

现在你应该已经学会了：

1. 通过路由传递参数
2. 路由名称 (name) 的运用和创建
3. 路由守护的使用

对了，与 `beforeEach` 方法相对应的是 `afterEach` 方法，相信聪明的你肯定能举一反三。

再加上上一章学会的注册路由，所有这一切知识点加起来，我相信，你已经可以独立完成一个不错的单页程序 (SPA) 了！

最后如果你对我的文章有任何疑问，不要犹豫，在下方留言，或者通过QQ,微信，邮箱，微博任何一个方式联系我，我希望有更多志同道合的朋友可以一起探讨。


[源代码](https://github.com/true1023/vue-tech/tree/master/vue03/spa2)

**码字不易，喜欢请点赞**
{% endraw %}