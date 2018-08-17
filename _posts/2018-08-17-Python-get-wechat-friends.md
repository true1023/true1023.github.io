---
layout: post
title: 获取微信好友头像并拼成大图（Python, PIL, Itchat）
tags: [Python, PIL, 微信, 教程]
---

> 如题的一个小程序，做与学结合，只有动手，才能更快的掌握新的语言


最终会生成一个这样的图片(保护隐私，已做模糊处理)：

![sample](/assets/img/post/2018-08-17/1.png)

用到的Python包：

1. [Pillow](https://github.com/python-pillow/Pillow) Python的图像处理库，PIL的一个Fork版本
2. [Itchat](https://github.com/littlecodersh/ItChat) 一个封装好的微信接口库

[源代码](https://github.com/true1023/teach_code/tree/master/Python/Wechat_merge)

# 一 总体流程

1. 使用Itchat登录微信并获取所有好友的头像图片地址保存到`images/`文件夹下
2. 假设图片数量为n*n，使用Pillow创建一个n行，n列的正方形底图
3. 使用Pillow缩放每张头像图片到200px*200px的大小，那么第二步的底图大小为width=n\*200px,height=n\*200px
4. 将所有缩放后的头像图片按照顺序粘贴到第二步的底图上，完成

# 二 代码

未安装itchat和Pillow的需要先安装这两个包，我用的是Anaconda(一个python的包控制软件，可以在安装python包的时候把所有依赖补全),用pip的同学可能在安装pillow的时候需要解决一些依赖问题。

```bash
conda install itchat
conda install pillow
```

首先登陆微信并获取所有好友的头像图片地址并下载到`images/`文件夹下

```python
#导入itchat包
import itchat

#下载所有好友头像并存储
def download_images(frined_list):
    image_dir = "./images/"
    #计数器，保存每一个头像图片名为 num.jpg
    num = 1
    for friend in frined_list:
        #赋值当前需要保存的图片的名称
        image_name = str(num)+'.jpg'
        num+=1
        #使用itchat自带函数get_head_img获取好友头像图片的二进制流
        #friend["UserName"]为当前好友的唯一标识符
        img = itchat.get_head_img(userName=friend["UserName"])
        #将图片二进制流img变量写入到images/文件夹下对应jpg文件
        with open(image_dir+image_name, 'wb') as file:
            file.write(img)

if __name__ == '__main__':
    #自动登陆，会出现二维码，扫码确认后登陆微信
    itchat.auto_login()
    #get_friends获取所有好友信息函数，返回list存储到friends变量
    friends = itchat.get_friends(update=True)
    #把好友信息传入下载图片函数
    download_images(friends)
```

现在在当前目录下会有一个`images/`文件夹，里面保存了所有微信好友的头像图片。因为需要一张一张下载，所以这个过程可能时间会比较长需要几分钟。

接着，把所有头像图片合并:

```python
from PIL import Image
import os
import math

#合并所有好友头像
#path为存储头像图像的文件夹相对于当前路径的相对路径，这里应该为'images/'
def merge_images(path):
    print("Merging head images......")

    #设置每个图片需要缩放到的大小
    photo_width = 200
    photo_height = 200

    #保存所有本地图片的绝对地址
    photo_list = []
    #头像图片文件夹的绝对路径
    dirName = os.getcwd()+path

    #os.walk用来遍历某一个文件夹下的所有文件夹和文件，递归便利，os是python自带库
    #具体参数用法参考python手册
    for root, dirs, files in os.walk(dirName):
        for file in files:
            #遍历所有文件，如果文件名包含jpg则获取该文件绝对路径添加到photo_list
            #os.path.join(root, file)拼接为这个文件的绝对路径
            if "jpg" in file and os.path.getsize(os.path.join(root, file)) > 0:
                photo_list.append(os.path.join(root, file))

    pic_num = len(photo_list)
    #合并图片的列数
    line_max = int(math.sqrt(pic_num))
    #合并图片的行数
    row_max = int(math.sqrt(pic_num))
    print(line_max, row_max, pic_num)

    #如果好友太多行数大于20行则限制为20行
    if line_max > 20:
        line_max = 20
        row_max = 20
    
    num = 0
    #需要合并的图片总数
    pic_max=line_max*row_max

    #新建底图，长款为行数*200px,列数*200px
    toImage = Image.new('RGBA',(photo_width*line_max, photo_height*row_max))

    #循环粘贴每一个头像图片
    for i in range(0,row_max):
        for j in range(0,line_max):
            #读取对应的头像图片
            pic_fole_head = Image.open(photo_list[num])
            #把图片伸缩到设置的大小（200px*200px）
            tmppic = pic_fole_head.resize((photo_width,photo_height))
            #计算图片粘贴的位置
            loc = (int(j%row_max*photo_width),int(i%row_max*photo_height))
            #把头像图片粘贴到底图对应位置
            toImage.paste(tmppic,loc)
            num = num+1
            if num >= len(photo_list):
                break
        if num >= pic_max:
            break
    print(toImage.size)
    #保存图片
    toImage.save('merged.png')
```
