---
title: scrapy爬取豆瓣全站
date: 2017-04-08 14:24:26
categories: Scrapy学习
tags: scrapy
---
# Scrapy爬取豆瓣读书全站
## 分析网页
>首先打开[豆瓣读书中的分类浏览](https://book.douban.com/tag/?icn=index-nav)，可以看到其中有很多的分类

>![分类](http://ono60m7tl.bkt.clouddn.com/2.bmp)

>豆瓣应该是一个比较好爬的网站，所有的数据都不是`ajax`加载的，我们打开谷歌的`F12`或者是火狐的`FireBug`可以很轻松的找到每一个分类的链接

>![url所在地](http://ono60m7tl.bkt.clouddn.com/NonName.bmp)

>这里我们使用scrapy中的一个[linkextractors库](http://scrapy-chs.readthedocs.io/zh_CN/0.24/topics/link-extractors.html),这个库的作用是会根据提供的限制，自动爬取和深入每一个页面并且提取需要的链接，如果想要找到每一个分类的url,只需`Rule(LinkExtractor(allow='/tag/',restrict_xpaths="//div[@class='article']"),follow=True),`这里的allow是一个`正则表达式`，用来筛选分类url,`restrict_xpaths`是限制在哪个结构中筛选url,这里限制的是在`<div class='article'>`这个盒模型中，`follow`表示是否深入，这里当然是要深入,这里就能得到每一个分类url了，自己可以在`回调函数`中测试下，输入所得的url,可以使用`respose.url`

>得到所有的分类url，就可以继续深入到每一步作品所在的页面了，如下图!


>![作品网页](http://ono60m7tl.bkt.clouddn.com/3.bmp)
>但是我们需要不止是这一页，我们要爬的时全站，因此这里必须实现翻页，我们可以看到页面底部清楚的写着下一页，我们通过解析页面同样可以得到url,如下图所示

>![翻页url](http://ono60m7tl.bkt.clouddn.com/6.bmp)
>可以看到所有的url的规则，我们就可以用正则表达式限制，以获取我们的需要，我们可以写出翻页的代码

```python
Rule(LinkExtractor(allow="\?start=\d+\&type=",restrict_xpaths="//div[@class='pa>ginator']"),follow=True),
```


>最后一步就是打开每一部书的网页得到所需的信息了，我们就可以通过这里通过解析网页还是可以很清楚的知道url,这里就不再详细的说怎么解析了，这里可以看到所有的url都在`li`标签中，如下图


>>![url](http://ono60m7tl.bkt.clouddn.com/4.bmp)

>我们打开`li`标签可以很清楚的看大url的规律，因此这里还是用到上面说的库解析深入，连同上面的代码如下

```python
Rule(LinkExtractor(allow='/tag/',restrict_xpaths="/ /div[@class='article']"),follow=True),#第一步
Rule(LinkExtractor(allow="\?start=\d+\&type=",restrict_xpaths="//div[@class='pa>ginator']"),follow=True),  #第二步翻翻页
Rule(LinkExtractor(allow="/subject/\d+/$",restrict_>xpaths="//ul[@class='subject-list']"),callback='parse_item')#得到所需网页的url
```

>到了这里总算是大功告成了，下面就需要解析自己的所需要的信息了,这里附上网页

>![图片](http://ono60m7tl.bkt.clouddn.com/5.bmp)
>下面就是写自己解析代码了，这里就不需要详细的说了，详细内容请看[源码](https://github.com/chenjiabing666/douban_book_spider),值得注意的是爬取的网页速度不要太快，豆瓣会禁IP的，这里可以采用一些反爬虫措施,如请求头的更换，ip地址的更换，下一篇会详细解说。

## 参考文档：
> **[scrapy中文文档](http://scrapy-chs.readthedocs.io/zh_CN/0.24/index.html#)**

>**最后附上本人的[github地址](https://github.com/chenjiabing666),不要忘了给个star哦**





















>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*