---
layout: post
title: Windows下NodeJS环境搭建
tags: [nodejs, javascript, js, 前端]
excerpt_separator: <!--more-->
---

# 1. NodeJS
Javascript运行环境，可以解析JavaScript语言，采用Chrome V8引擎，可以理解为你安装了一个Chrome浏览器，然后把浏览器的图形化界面都扒光，把没必要的一些功能都扒光，只留下一个核心V8引擎，再把这个引擎外面封装一些接口，就成了NodeJS。
<!--more-->

# 2. NPM（CNPM）

一个包管理器。就像我们装新家需要有现成的实木门，现成的组合厨房，现成的彩电电冰箱，然后把这些都拿过来以后像拼积木一样把他们拼到屋子里就完成了一个新家的装修，总比给你一堆元器件现发明冰箱来的快。包管理器，就是把这些前人写好的代码（包）下下来自己用。

可以类比于C#的NuGet，Ubuntu的APT，PHP的Compose，这些都是对应环境的包管理工具。

因为国内的开发者访问NPM官方资源会很慢，可以说基本不能用，所以我们一般把NPM的包地址指向国内的淘宝镜像

# 3. 搭建环境

去下面的地址，下载下来对应版本的NodeJs并且安装
[NodeJS官网下载地址](http://nodejs.cn/download/)
NodeJS和NPM是绑定安装的，装好NodeJS以后默认NPM就已经装好了，然后启动任意一个终端界面输入下面命令,如果出现版本号，则安装成功

```cmd
node -v
npm -v
```
`node -v`查询nodejs版本，`npm -v`查询npm版本号

如果是在中国大陆地区的用户最好安装CNPM，一个自动绑定淘宝镜像的NPM

详细的CNPM信息点[这里](http://npm.taobao.org)

## 3.1 安装cnpm

```cmd
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

解释下上面的参数

`install` 安装

`-g` 全局安装参数

`cnpm` 安装cnpm这个软件包

`--registry=https://registry.npm.taobao.org` 重定向包地址到https://registry.npm.taobao.org，也就是说我们是从taobao.org这个地址下载安装的cnpm这个包

这个过程需要一段时间，慢慢等一会


## 3.2 用CNPM安装Webpack包

装完cnpm以后，你需要建立一个单独的文件夹用来保存工程所需要的各种包、文件，我创建了一个叫做`NodeDemo`的新文件夹

然后在Powershell（或者别的终端比如cmd）里面切换到NodeDemo这个文件夹目录，运行下面这个命令安装一个webpack包（一个打包工具）
```
cnpm install webpack --save-dev
```
参数解释：
`--save` 将webpack这个包的信息保存到`package.json`配置文件
`-dev` 安装开发版本而非发布版


# 4. NodeDemo目录结构介绍

我们来看一下安装好Webpack后的NodeDemo文件夹里面有什么


![NodeDemo目录结构](/assets/img/post/2018-06-22/1.png)

一个文件`package.json`，一个文件夹`node-modules`。

## 4.1 node-modules

`node-modules`文件夹保存着你通过CNPM安装的所有的本地包代码，一般情况下我们是不需要动这个文件夹的。

那么为什么是本地包代码呢，是因为我们刚才安装包（webpack包）的时候并没有加`-g`这个参数，而这个参数的意义就是把这个包的代码全局安装，这样的话在任意项目中（比如NodeDemo这种文件夹）都可以使用这个包，而像是安装在`NodeDemo/node-modules/`这种文件夹里的包，就只有这一个项目可以用。

全局包安装地址是
`C:\Users\{username}\AppData\Roaming\npm\node_modules`
![全局包地址](/assets/img/post/2018-06-22/2.jpg)

这里面就会有刚刚我们全局安装的CNPM包

> 顺便一提为什么全局包哪都可以用，这个跟NodeJS的寻址顺序有关系，它会先找全局地址里的包，再找当前项目里面的包。

## 4.2 package.json

打开`package.json`，里面只有几行配置

```json
{
  "devDependencies": {
    "webpack": "^3.10.0"
  }
}

```
这个文件就是一个配置文件，用来告诉CNPM这个项目里都安装了哪些包，记得之前`--save`选项吗，如果安装包的时候加上这个选项，就会把那个包的信息加到这个文件里（package.json），如果不加`--save`，就不会加到这个文件里了,但是还是会安装到node-modules文件夹里

加到这个文件里的意义有两个，第一是可以手工打开查看当前项目里安装了哪些包以及版本信息，第二就是你可以通过这个文件随时重建这个项目结构，举个例子，我们现在把node-modules这个文件夹删除。

然后在Powershell（或者其他终端，必须在NodeDemo这个文件夹目录下）里面输入

```
cnpm install
```

运行`cnpm install`后，cnpm会读取`package.json`文件夹的内容，并把里面配置的包重新安装一遍。

当然，`package.json`还可以配置项目名称，作者名称或者其他一些参数，不过一般在创建项目的时候就已经设置好了

[package.json官网说明](https://docs.npmjs.com/files/package.json)

---

# 5. 总结

NodeJS是环境，一个执行JS代码的解释器

NPM是一个运行在NodeJS上的包管理器，可以方便的通过它管理下载各种可以在NodeJS环境下运行的包

这也是现代前端的开发基础




