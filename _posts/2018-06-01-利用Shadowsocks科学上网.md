---
layout: post
title: 利用Shadowsocks科学上网
tags: [翻墙, Shadowsocks]
excerpt_separator: <!--more-->
---

若不曾睁眼看世界...

---
<!--more-->

> 本文侧重应用，对于技术实现不做着重介绍， 否则过于冗长，感兴趣用户可以留言探讨。

<!-- To be placed at the beginning of the post, it is where the table of content will be generated -->
* TOC
{:toc}

# 1. 原理

> **如何已经有人帮你部署好了服务器，那就简单多了，请跳过前面直接看 *3下载客户端***

由于GFW的存在，部分用户是无法访问Google等网站，本教程的原理是墙外服务器作为跳转平台，将本来指向Google(或者其他被限制地址)的流量先转发到墙外服务器，由墙外服务器访问后转发给用户，整个过程是通过Shadowsocks这个软件进行的加密传输。

![sample](/assets/img/post/2018-06-01/1.png)

Shadowsocks(ss) 是由 [Clowwindy](https://github.com/Clowwindy) 开发的一款软件，其作用本来是加密传输资料。当然，也正因为它加密传输资料的特性，使得 GFW(墙) 没法将由它传输的资料和其他普通资料区分开来（上图），也就不能干扰我们访问那些"不存在"的网站了。



# 2. 选择VPS (服务器) 和部署

## 2.1 选择

VPS (Vitrual private server) 是一台虚拟服务器，可以理解为一台由专人维护供用户使用的电脑，你可以给他安装操作系统，软件，并且通过一些工具远程操控它。

有许多公司提供VPS的租用服务，国内的如阿里云，百度云，国外的如微软的Azure, AWS, Vultr,BandwagonHost(搬瓦工), 这些公司都可以提供部署在世界各地的服务器，我们只需要选择一家公司并选择一个部署在墙外的服务器作为跳转平台就可以了。

我用的是[Vultr](https://www.vultr.com/?ref=7430643)这家服务器提供商，有美国、亚洲、欧洲等多地的VPS。他家的服务器以性价比高文明，按时间计费，最低的资费为每月$2.5。
![sample](/assets/img/post/2018-06-01/2.png)

比 Vultr 价格更低的服务商也有许多，最知名的莫过于「搬瓦工」，和 V 家差不多的配置只要 $19.9 每年。不过后者过于出名，据说超售严重，实测夜间速度足以让人痛哭流涕。

## 2.2 充值

首先进入[Vultr](https://www.vultr.com/?ref=7430643)官网，可以点击[这里](https://www.vultr.com/?ref=7430643)进入 -> 点击右上角的`Create Account` -> 在新出现的页面用你的邮箱进行账户注册 -> 成功后登陆

![注册](/assets/img/post/2018-06-01/3.png "注册")
![登陆成功](/assets/img/post/2018-06-01/4.png "登陆成功")

注意左边侧栏，从上到下我们只用到前两个，第一个Servers用来选择VPS，第二个Billing用来给当前账户充值。

先充值，点击Billing, 选择 Aplipay(支付宝), 选最少的$10大概是人民币60多，支付宝会按照实时汇率换算成人民币，然后扫码付款后就会有10美元充值到你当前的账户

## 2.3 选择服务器

点击左侧 Servers, 在右侧的前三个选项里（1.Server Location 2.Server Type 3.Server Size）里分别选择 **Tokyo**, **Ubuntu16.04**和 **25GB SSD**, 然后点击 Deploy Now。
其他选项可以忽略，也可以按需选择，不过下面服务器的部署都是基于Ubuntu16.04系统。
等待系统安装完成。当显示“Running”时，就表示部署已完成。之后你可以随时停用、重启或销毁它。

![选择服务器](/assets/img/post/2018-06-01/5.png "选择服务器")

## 2.4 连接服务器

如果你用的是windows系统，远程连接刚刚部署的这台服务器需要下载[Putty](https://www.putty.org/)。请参考[百度经验](https://jingyan.baidu.com/article/8ebacdf0d9e86549f75cd57b.html)完成安装。
服务器的IP地址和root账户密码点击刚刚部署好的服务器就可以看到
。
![服务器信息](/assets/img/post/2018-06-01/6.png "服务器信息")

![连接服务器](/assets/img/post/2018-06-01/7.png "连接服务器")

## 2.5 安装 Shadowsocks server

Shadowsocks有一个Python的包，Ubuntu16.04默认已经安装了Python, 所以我们只需要利用pip(Python的包管理工具)安装Shadowsocks并且运行就可以了。官方教程在[这里](https://github.com/shadowsocks/shadowsocks/wiki/Shadowsocks-%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E)

首先安装pip,一定要加sudo获得权限

```
sudo apt-get install python-pip
```

接着用pip安装shadowsocks

```
sudo pip install git+https://github.com/shadowsocks/shadowsocks.git@maste
```

一切顺利的话，至此shadowsocks服务端安装完成, 恭喜！

## 2.6 启动 Shadowsocks server

在终端输入下面的命令，后台启动shadowsocks。

```
sudo ssserver -p 443 -k password -m aes-256-cfb --user nobody -d start
```

解释一下 
 - -p 制定监听端口，此处为443
 - -k 设置客户端连接密码，此处是password
 - -m 指定加密方式， 此处是 aes-256-cfb
 - --user 指定用哪个用户运行此程序，此处不指定特定用户
 - -d 后台运行程序， 有此参数程序后台运行
 我们需要记住的是端口号（443） 和刚才的密码（password）

## 2.7 开启 TCP Fast Open (可选)

实际上只要具备上述几部，你就可以在自己的任意设备上进行登录使用了。但是为了更好的连接速度，你还需要开启 [TCP Fast Open](https://en.wikipedia.org/wiki/TCP_Fast_Open)。

**首先**在终端输入 `sudo vim /etc/rc.local` 打开并编辑 rc.local 文件，在这个文件的最后加上下面这行

```
echo 3 > /proc/sys/net/ipv3/tcp_fastopen
```

然后保存退出

**然后**打开sysctl.conf

```
sudo vim /etc/sysctl.conf
```

在文末加上：

```
net.ipv4.tcp_fastopen = 3
```

**最后**重启shadowsocks程序

```
sudo ssserver -p 443 -k password -m aes-256-cfb --user nobody --fast-open -d restart
```

这个命令比上面的多了一个 `--fast-open` 选项，指示程序开启 TCP fast open, 最后的`open`也变成了`restart`，因为之前已经启动过程序了，所以这里是重启，如果没有启动过，那就还是`open`

# 3. 下载客户端
服务器端配置好了，接着就是下载windows客户端程序,

   [Win版客户端下载](https://github.com/shadowsocks/shadowsocks-windows/releases)

# 4. 连接服务器，客户端设置
下载下来是个解压缩文件，里面有个exe程序，将这个程序解压到一个单独的文件夹下，然后点击打开.

![客户端配置](/assets/img/post/2018-06-01/8.png "客户端配置")
如图所示，我们只需要填写
 - **Server Addr (服务器地址)**，这里我的是`198.14.49.236`，自己的地址请回头看2.4中有写
 - **Server Port (服务器端口)**，也是刚刚设置的 `443`
 - **Password (密码)**， 刚才我们设置的密码是 `password`，这里就输入这个
 - **Encryption (加密方式)**, 还是刚才我们启动服务器端程序的时候设置的，`aes-256-cfb`
剩下的就用默认的，代理端口为 1080.
点击OK, 注意屏幕右下角托盘里会出现一个灰色的小飞机，这个就是SS(Shadowsocks)的客户端，现在如果你刚才输入地址和密码什么的都没有问题，应该已经连接上了。
![ss客户端](/assets/img/post/2018-06-01/9.png "ss客户端")

接着介绍两种代理模式，PAC模式是比较推荐的模式，省流量：

 -  **全局代理**&nbsp;&nbsp;右键点击小飞机图片，确保 `Enable System Proxy` 和 `Global` 都被勾选中
![全局代理](/assets/img/post/2018-06-01/10.png "全局代理")

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;全局代理设置完成

 - **PAC**&nbsp;&nbsp;PAC是用来控制什么时候需要使用SS服务器的，比如访问百度的时候就不需要使用，访问谷歌就需要.

 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**首先**取消勾选 `Enable System Proxy` 

 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**接着**选PAC -> Online PAC, 会跳出一个弹出框让填写一个在线的PAC地址，下面是一些备选地址，随意填写一个就可以:

 ```
 Pagure: https://pagure.io/gfwlist/raw/master/f/gfwlist.txt

 Repo.or.cz: http://repo.or.cz/gfwlist.git/blob_plain/HEAD:/gfwlist.txt

 Bitbucket: https://bitbucket.org/gfwlist/gfwlist/raw/HEAD/gfwlist.txt

 Gitlab: https://gitlab.com/gfwlist/gfwlist/raw/master/gfwlist.txt

 TuxFamily: https://git.tuxfamily.org/gfwlist/gfwlist.git/plain/gfwlist.txt
 ```
![PAC](/assets/img/post/2018-06-01/11.png "PAC")

 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;PAC设置完成
 

# 5. 浏览器设置
这是最后一步了，相当简单，这里以Chrome浏览器为例，其他浏览器大同小异。
打开浏览器 -> 右上角点开找到设置 -> 在新出现的页面上找到打开代理设置
然后会出现下图

![代理设置](/assets/img/post/2018-06-01/12.png "代理设置")

点击 LAN settings (局域网设置)
在新出现的页面勾选 Use a proxy server for your LAN (启用代理服务器)，
将 `Address` 和 `Port` 如下图一样填好，端口号 1080是在上面设置ss客户端的时候设置的=

![代理设置](/assets/img/post/2018-06-01/13.png "代理设置")

到此，重启浏览器后就应该可以访问任何你想问的网站了。

![google](/assets/img/post/2018-06-01/14.png "google")
![twitter](/assets/img/post/2018-06-01/15.png "twitter")
![youtube](/assets/img/post/2018-06-01/16.png "youtube")


# 6. 相关技术
 1. **PAC** [wiki地址](https://en.wikipedia.org/wiki/Proxy_auto-config)
 2. [GFWList](https://github.com/gfwlist/gfwlist)
 3. [Shadowsocks github](https://github.com/shadowsocks/shadowsocks)

<!-- To be copied at the end of the post to render the table of content -->
<script>
$(document).ready(function() {
    $('#toc').toc();
});
</script>