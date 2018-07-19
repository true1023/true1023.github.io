---
layout: post
title: VueJS简明教程(一)之基本使用方法
tags: [JS, 教程, Vuejs]
excerpt_separator: <!--more-->
---

> 简介：这是一篇超级简单的入门文章
<!--more-->
如果说是JQuery是手工作坊，那么Vue.js就像是一座工厂，虽然Vue.js做的任何事情JQuery都可以做，但无论是代码量还是流程规范性都是前者较优。

Vue.js的[官方中文教程](https://cn.vuejs.org/v2/guide/)其实也是一个不错的教程，不过相比于一次性把所有概念掌握，我更倾向于先会用，之后再在实际应用中把未涉及到的知识点逐步补全。

就像开车，不是非要知道发动机的工作原理才能上路的，甚至你可能一辈子也不用知道。

好了，开始吧

{% raw %}
# 准备

首先，以下几点希望你已经知道或者做到了:

* 你会用html+css+javascript 写一些网页
* 你知道什么是[DOM](https://baike.baidu.com/item/DOM/50288?fr=aladdin)和BOM(Browser Object Model)
* 你知道Vue.js是一个框架而不是一个新语言
* 你会用百度或者Google其中之一去查找答案

接着，我会围绕以下几个Vue.js的基本（核心）使用方法逐个做测试和使用说明：

* 新建vue对象
* 数据绑定
* 事件绑定
* 表单控件绑定

最后，根据上面的知识点结合起来写出我们的第一个vuejs前端页面


# 新建Vue对象

1. 引用vue.js
在桌面建立一个 Vue.html文件, 然后引入 vue.js的CDN地址 **https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js** 

```html
 <script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
```
2. 新建Vue实例
在Vue.html中插入一个id为*firstVue*的`<div>`

```html
<div id="firstVue"></div>
```

在Vue.html中插入下面js代码:

```js
<script type="text/javascript">
	var myVue = new Vue({
		el:"#firstVue"
	})
</script>
```

然后整个代码看起来是这样的:

```html
<!DOCTYPE html>
<html>
<head>
	<title>Vue Demo</title>
	<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
</head>
<body>
	<div id="firstVue">
	</div>
</body>
<script type="text/javascript">
	var myVue = new Vue({
		el: "#firstVue"
	})
</script>
</html>
```

解释一下代码

js变量 *myVue* 就是Vue创建的一个对象，可以理解成把`<div id="firstVue></div>`和这个标签里面包含的所有DOM都实例化成了一个JS对象， 这个对象就是`myVue` 

*el*是Vue的保留字，用来指定实例化的DOM的ID号, *#firstVue*这句话就是标签选择器，告诉Vue要实例化`ID=“firstVue”`的这个标签。

至此，Vue.js框架在html页面的引入工作完成，但是如果我们访问这个页面并不能看到任何效果,一篇空白

# 数据绑定

**VUE这个框架的数据流向是单向的**，不懂没关系，记住就好了
所以数据绑定后的数据流向是从

**vue实例**——>**DOM文档**的

我们给上一步的`<div>`标签添加下面一句话

```
{{ my_data }}
```

变成这样

```html
<div id="firstVue">
	{{my_data}}
</div>
```

这个双大括号的语法叫做**mustache** 语法，大括号里面的是作为变量形式出现的。

然后在创建 vue实例的代码中加入下面数据声明:

```js
data:{
	my_data: "test"
}
```

整个代码看起来向下面这样:

```html
<!DOCTYPE html>
<html>
<head>
	<title>Vue Demo</title>
	<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
</head>
<body>
	<div id="firstVue">
	{{my_data}}
	</div>
</body>
<script type="text/javascript">
	var myVue = new Vue({
		el:"#firstVue",
		data:{
			my_data: "test"
		}
	})
</script>
</html>
```

`data`参数用来绑定VUE实例的数据变量，每个不同变量之间用逗号分隔，上面我们绑定了自定义变量`my_data`，并赋初值'test'

完成数据绑定工作，`<div>`标签里的`{{myData}}`数据会随着myVue实例里的`myData`数据的变动而变动，浏览器查看当前页面,会出现'test'字符串，说明数据绑定成功

这是在HTML标签内部的数据绑定，那么如果想绑定某个HTML标签的属性值，就要用到`v-bind:`属性了，比如我想绑定一个标签是否可见的属性(hidden)，那么就应该这么写:

```html
<div id="firstVue" v-bind:hidden="my_hidden">{{my_data}}</div>
```

`v-bind:`后面写想要绑定的属性，`my_hidden`也不需要用两个大括号括起来了，直接写就可以了，然后在VUE实例中声明这个绑定数据

```js
var myVue = new Vue({
	el:'#firstVue',
	data:{
		my_data: "test",
		my_hidden: "hidden"
	}
})
```

这时候再浏览这个网页,又变成了空白，但是F12查看源代码，发现其实是div标签的hidden属性被激活了

当然，`v-bind:`不仅可以绑定hidden属性，disabled属性，style属性, color属性，凡是标签有的属性，都可以通过这个方法进行绑定

`v-bind`由于经常会用到，所以也可以缩写成冒号`:`，比如上面的`<div>`标签就完全可以这样写

```html
<div id="firstVue" :hidden="my_hidden">{{my_data}}</div>
```

这是和`v-bind:hidden="my_hidden"`完全等价的一种写法

至此，Vue.js两种绑定数据的方法都写完并进行了测试

# 事件绑定
`v-bind:`是用来绑定数据的，`v-on:`则是用来绑定事件的，比如我要绑定一个`<button>`的 `click`事件就这么写

```html
<button v-on:click="clickButton()">Click Me</button>
```

当然这里的*click*可以换成任意一个html事件，比如load , doubleclick , mouseon , mousedown这些，不过*click*肯定是我们最常用的

将*click*动作绑定到clickButton()函数之后就需要实现这个函数了,我们要在之前的vue实例中加入新字段`methods`

```js
var myVue = new Vue({
	el:'#firstVue',
	data:{
		my_data: "test",
		my_hidden: "hidden"
	},
	methods:{
		clickButton:function(){
			this.my_data = "Wow! I'm changed!"
		}
	}
})
```

我们在methods关键字里面定义了clickButton方法，并在方法内改变了之前定义的my_data变量的值

这里涉及到如何在vue实例中引用data字段的变量，如上所示，需要加`this`后面直接写要引用的变量就可以了

如果不加`this`，系统会默认你想引用的是一个全局变量，可是这里我们需要引用的是这个vue实例里的局部变量

现在整体代码和之前有些变化，看起来是这样的：

```html
<!DOCTYPE html>
<html>
<head>
	<title>Vue Demo</title>
	<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
</head>
<body>
	<div id="firstVue">
		<button v-on:click="clickButton">Click Me</button>
		<p>{\{my_data\}}</p>
	</div>
</body>
<script type="text/javascript">
var myVue = new Vue({
	el:'#firstVue',
	data:{
		my_data: "test",
		my_hidden: "hidden"
	},
	methods:{
		clickButton:function(){
			this.my_data = "Wow! I'm changed!"
		}
	}
})
</script>
</html>
```

好了，运行一下，点击`Click Me`的时候"test"就会变成"Wow! I'm changed!"，这是因为变量my_data的改变。

另外，`v-on:`语法同样有一个缩写`@`，比如`v-on:click="clickButton"`就等价于`@click="clickButton"`

# 表单控件绑定

之前有说Vue这个框架是单向数据传输的，就是从vue实例传送数据到DOM ，那么我们如何从DOM中实时获取用户输入的数据赋值给vue实例呢

这用到了Vue.js提供给用户的一个[语法糖](https://baike.baidu.com/item/%E8%AF%AD%E6%B3%95%E7%B3%96/5247005?fr=aladdin) `v-model` ,这个语法糖通过两步实现了数据的反向传递，也就是从DOM传送给vue实例数据。

*下面说的原理看不懂可以跳过，不会影响后续阅读*
>`v-model` 通过两步实现了数据反向传递
>
>第一步，绑定了DOM标签的`input`事件(比如叫tapInput())
>
>第二步，当用户进行输入时候，触发`tapInput()`函数，`tapInput()`函数内部读取此DOM标签的Value值，赋值给vue实例
>
>通过以上两步，v-model语法糖实现了vue数据的反向传输

好，直接写一个例子吧

```html
<!DOCTYPE html>
<html>
<head>
	<title>Vue Demo</title>
	<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
</head>
<body>
	<div id="firstVue">
		<input type="text" v-model="my_data" />
		<button v-on:click="clickButton">Click Me</button>
		<p>{{my_data}}</p>
	</div>
</body>
<script type="text/javascript">
	var myVue = new Vue({
		el:'#firstVue',
		data:{
			my_data: "test",
			my_hidden: "hidden"
		},
		methods:{
			clickButton:function(){
				this.my_data = "Wow! I'm changed!"
			}
		}
	})
</script>
</html>
```

可以发现，我们增加了一个`<input>`标签，并且用v-model语法绑定了之前定义的变量my_data

这个时候，当我们在`<input>`输入框输入值的时候，v-model会实时将最新值(value)赋值给vue实例的`my_data`变量，而`my_data`变量又将实时展现在`<p>`标签中

这样我们看起来实现了这么一个数据流:

**DOM1(input标签)——>VUE——>DOM2(p标签)**

先从用户输入的DOM1获取输入保存在VUE实例，用从VUE实例里拿到数据输出到DOM2

运行这个页面，发现`<p>`标签中的内容会随着`<input>`标签实时的变化，说明我们利用v-model语法变相实现了数据的双向传递


# 总结
看看我们学到了什么
* 用 `new Vue({})`新建vue实例
* 使用 `v-bind:`和`{{}}`双大括号语法在html中绑定变量
* 使用 `v-on:` 和 `@` 语法绑定函数到标签的事件
* 使用 `v-model:` 语法使用户的页面输入反向传递回vue实例变量


现在简单完善一下上面的代码，做成一个计数器程序，用户可以在输入框输入任意数字，点击按钮进行总数累加

下面是对刚才代码进行了一些小的改动，相信你一定可以看明白的

```html
<!DOCTYPE html>
<html>
<head>
	<title>Vue Demo</title>
	<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
</head>
<body>
	<div id="firstVue">
		<input type="number" v-model.number="my_step" />
		<button v-on:click="clickButton">Add</button>
		<p>{{my_total}}</p>
	</div>
</body>
<script type="text/javascript">
	var myVue = new Vue({
		el:'#firstVue',
		data:{
			my_step: 1,
			my_total: 0
		},
		methods:{
			clickButton:function(){
				this.my_total = this.my_total + this.my_step
			}
		}
	})
</script>
</html>
```

上面`v-model.number`后面的这个`.number`的意思是说在返回`my_step`值的时候自动转换成integer类型，因为默认是string类型，所以要多这么一句。

---

本文算是vue的基础内容，下一篇将会是vue的核心内容，组件。

前端的东西多且杂，东拼西凑总也不是进步的办法，我认为最好就是将主干的一支学明白用在实际项目中，其他的一些枝枝叉叉遇见了现看文档也是来得及的，比如我每次FLEX布局都要现查语法，甚至写前端页面的时候总是开着VUE的官方文档随时查看 -囧-

我会写出一个用Vue.js构建SPA(Single Page Application)的系列文章, 希望可以让初学、转学VUE的人少走弯路

最后的最后，转载请注明出处，谢谢

附:[源代码](https://github.com/true1023/vue-tech/tree/master/vue01init)
{% endraw %}

下一篇：[VueJS简明教程(二)之组件](http://www.qinluo1023.com/2018/07/19/simple-vuejs-02.html)