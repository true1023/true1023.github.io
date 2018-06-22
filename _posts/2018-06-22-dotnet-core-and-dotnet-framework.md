---
layout: post
title: .Net Strandard .Net Core .Net Framework的区别
tags: [dotnet, c#, dotnet core]
excerpt_separator: <!--more-->
---
.NET Core 是 .NET Standard的一种实现形式，.NET Standard可以运行在多种操作系统上，可以理解为是一个基础库，所有其他的.NET框架都是在它的基础上扩展出来的，我称之为扩展库，比如.NET Framework，Xamarin，Tizen ，当然还有前面提到的.NET Core
<!--more-->

如果你的程序使用了一个 .NET Core的库，你有可能访问一些.NET Core特有但是不包含在.NET Standard库里的API，这时候（使用了.NET Core特有的API），你的程序如果换一个.NET Standard扩展库（比如.NET Framework ,Xamarin等等）就将不能运行。

拿下面这个图举例，两边的.NET Framework和.NET Core分别继承了中间.NET Standard的API并且实现了一些自己特有的API，这个时候如果程序本来是运行在.NET Core上，突然将底层框架变成了.NET Framework，就有可能会出现兼容性问题。

![Image](/assets/img/post/2018-06-22/3.png)

**总结一下：.Net standard更像是一个标准，就现在来说，微软.Net Core已经发布了2.1，配合VS Code已经完全可以跨平台开发和运行了**


