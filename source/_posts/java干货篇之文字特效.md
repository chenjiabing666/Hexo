---
title: java干货篇之文字特效
date: 2017-04-09 16:15:16
categories: java学习
tags: java图形与文本处理
---

# java干货篇文字特效
## 立体效果的文字
**主要使用了Graphics类中的setFont和setColor的方法，绘制多层字然后加上平移一个坐标即可实现多重叠加的效果,让人看起来就像是立体一样,详情请见[源码](https://github.com/chenjiabing666/java_provide/tree/master/039)**

## 阴影效果的文字
**和面一样，只是平移的方式有些不同，详情请见[源码](https://github.com/chenjiabing666/java_provide/tree/master/040)**

## 倾斜效果的文字
**主要使用的时Graphics2D类的shear方法，使绘图上下文倾斜，详情见[源码](https://github.com/chenjiabing666/java_provide/tree/master/041)**
**`public abstract void shear(double shx,double shy)`其中shx表示在正x轴方向移动坐标的乘数，可以作为其y坐标的函数**

## 渐变效果的文字
**主要使用了Graphics2D中的setPaint的方法,详情请见[源码](https://github.com/chenjiabing666/java_provide/tree/master/042)**
**`public abstract void setPaint(Paint paint)`paint封装了渐变颜色的Paint对象**
**其中Paint对象的创建是由[GradientPaint](http://tool.oschina.net/uploads/apidocs/jdk-zh/java/awt/GradientPaint.html)初始化的,其中的构造函数如下：`GradientPaint(float x1, float y1, Color color1, float x2, float y2, Color color2) `**

## 会变色的文字
**这个主要使用了多线程的方式实现的，用多线程改变Color方法中的RGB的值,用Random在指定范围内任意取值然后组成了不同的颜色，详情请见[源码](https://github.com/chenjiabing666/java_provide/tree/master/043)**

## 水印文字特效([源码](https://github.com/chenjiabing666/java_provide/tree/master/044))
**水印文字主要通过改变了文字的透明度实现的，将文字绘制在图片上，然后改变图片的透明度，主要使用了Graphaics2D中的setComposite方法，定义如下：**
**`public abstract void setComposite(Composite comp)`，其中Comp是[AlphaComposite](http://tool.oschina.net/uploads/apidocs/jdk-zh/java/awt/AlphaComposite.html)对象，可以使用以下两种方式创建**
>* `AlphaComposite alpha=AlphaComposite.getInstance(AlphaComposite.SRC_OVER,0.3f)`获得一个SRC_OVER规则的对象
>* `AlphaComposite alpha=AlphaComposite.SC_OVER.driver(0.3f)`同上

## 动态绘制文本([源码](https://github.com/chenjiabing666/java_provide/tree/master/046))
**主要使用BufferedReader缓冲流从指定文件中读取一个字符，然后使用线程一个一个的绘制在画板上，中间sleep了400ms，这样就能展示出动态的效果，还使用了System类的getProperty方法获得项目的路径,以下提供了两种方法读取文件，更多的读取方法请看我的[博客文章](https://chenjiabing666.github.io/2017/03/25/java%E4%B8%AD%E7%9A%84IO%E6%93%8D%E4%BD%9C/)**
>* `BufferedReader read=new BufferedReader(new FileReader(pathname))`
>* `BufferedReader read=new BufferedReader(new InputStreamReader(in))`

**由于都是比较简单的代码，这里不再贴出来le，有想要看的朋友，请点击上面的源码**







>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*