---
layout: post
title: VueJS简明教程(二)之组件
tags: [JS, 教程, Vuejs]
excerpt_separator: <!--more-->
---
上一篇：[VueJS简明教程(一)之基本使用方法](http://www.qinluo1023.com/2018/06/13/simple-vuejs-01.html)

> 组件(Component)是Vue最强大的功能之一。组件可以扩展HTML元素，封装可重用的代码。在较高层面上，组件是自定义元素，Vue的编译器为它添加特殊功能。
> 在有些情况下，组件也可以表现为用is特性进行了扩展的原生HTML元素。
> 所有的Vue组件同时也都是Vue的实例，所以可以接受相同的选项对象（除了一些根级特有的选项）并提供相同的生命周期钩子。

<!--more-->
# 一，基础
以上是VUE组件的官方定义，让我说的直白一点，比如'<button>Click</button>'这个html的按钮就可以理解为一个组件，因为这么写就会展示出一个按钮在界面。

那么如果我写'<button-demo>Click</button-demo>'界面就不会有任何显示，因为浏览器不认识这个'<button-demo>'这个组件，html语法没有定义。

那么VUE的组件使我们有能通过现有的浏览器认可的组件自定义一个像是'<button-demo>'这样的组件的显示方式和内部运行逻辑。

{% raw %}
举个栗子：

```js
Vue.component('button-demo',{
	template:'<button>Hello here!</button>'
})

```

通过Vue注册一个叫做'button-demo'的组件，这个自定义组件是由'<button>Hello here!</button>'组成的。

可以这么理解，注册了'button-demo'以后，'<button-demo></button-demo>'就等价于'<button>Hello here!</button>'

看一下[演示效果](https://jsfiddle.net/true1023/asjnbhtr/)

这里需要注意下，通过Vue注册的组件只能在Vue的注册范围内用，就像演示里面一样，必须要'<div id="app"></div>'里面用。

# 二，进阶

VUE组件当然不可能就只能组合组合html标签，每一个VUE组件对相当于一个VUE实例，在组件内可以定义方法，数据，甚至可以引用其他组件

```js
//定义button-counter组件
Vue.component('button-counter',{
	data:function(){
		return {
			count: 0
		}
	},
	methods:{
		clickAdd: function(){
			this.count++
		}
	},
	template:'<button @click="clickAdd">You clicked me {{count}} times</button>'

})
```

看一下[演示效果](https://jsfiddle.net/true1023/4b28n53e/)

这里需要注意的是`data`并不是像这样直接提供一个对象：

```js
data: {
	count: 0
}
```
取而代之的是，一个组件的 data 选项必须是一个函数，因此每个实例可以维护一份被返回对象的独立的拷贝（看不懂记住就好，以后慢慢理解）：
```js
data: function(){
	return {
		count: 0
	}
}
```

# 三，全局注册与局部注册

# 四，注意问题

### 单个根元素

### 特殊元素特殊对待



最后的最后，转载请注明出处，谢谢

附:[源代码](https://github.com/true1023/vue-tech/tree/master/vue01init)
{% endraw %}