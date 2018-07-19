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

# 一 基础

以上是VUE组件的官方定义，让我说的直白一点，比如`<button>Click</button>`这个html的按钮就可以理解为一个组件，因为这么写就会展示出一个按钮在界面。

那么如果我写`<button-demo>Click</button-demo>`界面就不会有任何显示，因为浏览器不认识这个`<button-demo>`这个组件，html语法没有定义。

那么VUE的组件使我们有能通过现有的浏览器认可的组件自定义一个像是`<button-demo>`这样的组件的显示方式和内部运行逻辑。

{% raw %}
举个栗子：

```js
Vue.component('button-demo',{
    template:'<button>Hello here!</button>'
})

```

通过Vue注册一个叫做'button-demo'的组件，这个自定义组件是由`<button>Hello here!</button>`组成的。

可以这么理解，注册了'button-demo'以后，`<button-demo></button-demo>`就等价于`<button>Hello here!</button>`

看一下[演示效果](https://jsfiddle.net/true1023/asjnbhtr/1/)

这里需要注意下，通过Vue注册的组件只能在Vue的注册范围内用，就像演示里面一样，必须要`<div id="app"></div>`里面用。

# 二 进阶

VUE组件当然不可能就只能组合组合html标签，每一个VUE组件对相当于一个VUE实例，在组件内可以定义方法，数据，甚至可以引用其他组件

```js
//定义button-counter组件
Vue.component('button-counter',{
    //定义数据
    data:function(){
        return {
            count: 0
        }
    },
    //定义方法
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

# 三 全局注册与局部注册

上面我们注册的是全局组件，也就是在整个VUE根实例中注册的，也可以理解为调用了VUE的静态方法注册的，这样注册的组件是可以在任何VUE实例中使用的。

不过这样也会带来作用域冲突的麻烦，比如我分别注册了一个前端`HOME`组件和一个后端`ADMIN`组件，又想注册两个`HEAD`组件分别用在`HOME`和`ADMIN`组件里面，代表前台后台不同的页头，这时候如果是全局注册为了区分不同的`HEAD`组件，我可能就需要为他们起两个名字，分别叫`HOME_HEAD`和`ADMIN_HEAD`。

但是局部组件注册就允许用户把自定义组件的作用域限制在父级VUE实例中：

```js
//component-a是一个全局组件
Vue.component('component-a',{
    template:'<div><p>这里是全局组件</p><component-b></component-b></div>',
    //使用components在父组件中注册局部组件
    components:{
        'component-b': {
            template:'<button>这是一个局部组件</button>'
        },
    }
})
```

可以看到，我们把在`component-a`组件里使用了关键字`components`注册了一个局部组件`component-b`并且嵌入了`component-a`的模板语法中。

[完整代码](https://jsfiddle.net/true1023/trpxg92e/)

# 四 注意问题

## 单个根元素

注意上面局部组件的那个例子，全局组件`component-a`的模板字符串是被一个`<div>`标签完全包裹起来的，是因为VUE规定**每个组件必须只有一个根元素**，所以不管你的组件由多少子元素组成，最外层都只有有且只有一个元素。

## 合理组织代码结构

同样是局部组件的那个例子，如果向下面这样写会不会更清楚明白一些：

```js
//定义component-b组件变量
component-b = {
    template: '<button>这是组件B</button>'
}
//定义component-a组间变量，并引用component-a变量
component-a = {
    template : '<div><p>这是组件A</p><component-b></component-b></div>'
    components: {
        'component-b':component-b
    }
}
//在Vue实例中注册component-a局部组件
//虽然component-a实在Vue实例中注册，但是如果此Vue实例
//是整个程序的跟实例，那也component-a也相当于进行了全局注册
new Vue({
     el: '#components-demo',
     components: {
         'component-b': component-a
     }
})
```

上面的代码将每个组件的代码赋值给了不同的变量，这样如果某个组件有变化，只用修改相应的变量，而不需要修改所有引用此变量的方法，实现了松耦合的编程思想。

## 特殊元素特殊对待

有些 HTML 元素，诸如 `<ul>`、`<ol>`、`<table>` 和 `<select>`，对于哪些元素可以出现在其内部是有严格限制的。而有些元素，诸如 `<li>`、`<tr>` 和 `<option>`，只能出现在其它某些特定的元素内部。

这会导致我们使用这些有约束条件的元素时遇到一些问题。例如：

```html
<table>
  <blog-post-row></blog-post-row>
  <!--table元素中只允许包含<th>,<tr>等元素-->
</table>
```

这个自定义组件 `<blog-post-row>` 会被作为无效的内容提升到外部，并导致最终渲染结果出错。幸好这个特殊的 is 特性给了我们一个变通的办法：

```html
<table>
  <tr is="blog-post-row"></tr>
</table>
```

这样`<blog-post-row>`这个自定义组件就会正常渲染。

如果你使用单文件组件的话(就是`.vue`后缀的文件)，这一条可以忽略

# 总结

作为一个轻量级的JS前端框架，

我认为这两章的内容就已经涵盖使用VUE最核心的语法和思想了。

还是那句话，学习一个框架，最有效的方法就是实践，不需要把所有文档看一个遍再去写程序，因为真正写程序的时候，你还会翻看无数遍框架文档。

直到现在，我在写VUE程序的时候，左边的屏幕是编辑器，右边的屏幕就是VUE的官方文档。

1. 数据绑定
2. 事件绑定
3. 用户输入获取
4. 组件定义和使用

上面四大知识点已经足够支撑我们写出一个差不多的VUE程序了。

后面会分别介绍一下:

1. Node.js与NPM，现代前端开发必需品
2. Webpack， 前端代码打包工具
3. vue-router，单文件前端程序必需品

有了上面这几样东西的基础，就可以着手开发一个单页程序了^_^

**如果你都看到这里了，能不能点个赞或者评价一下呢(* ￣3)(ε￣ *)**

{% endraw %}