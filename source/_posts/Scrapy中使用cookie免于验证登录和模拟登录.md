---
title: Scrapy中使用cookie免于验证登录和模拟登录
date: 2017-03-26 13:30:53
categories: Scrapy学习
tags: scrapy
---
# Scrapy中使用cookie免于验证登录和模拟登录
## 引言
>`python`爬虫我认为最困难的问题一个是ip代理，另外一个就是模拟登录了，更操蛋的就是模拟登录了之后还有验证码，真的是不让人省心，不过既然有了反爬虫，那么就有反反爬虫的策略，这里就先介绍一个cookie模拟登陆，后续还有`seleminum+phantomjs`模拟浏览器登录的文章。还不知道cookie是什么朋友们，可以[点击这里](http://bubkoo.com/2014/04/21/http-cookies-explained/)
>## cookie提取方法：
>>打开谷歌浏览器或者火狐浏览器，如果是谷歌浏览器的按`F12`这个键就会跳出来浏览器控制台，然后点击`Network`，之后就是刷新网页开始抓包了，之后在抓到的页面中随便打开一个，就能看到cokie了，但是这里的cookie并不符合python中的格式，因此需要转换格式，下面提供了转换的代码

```python  
    # -*- coding: utf-8 -*-

    class transCookie:
    def __init__(self, cookie):
        self.cookie = cookie

    def stringToDict(self):
        '''
        将从浏览器上Copy来的cookie字符串转化为Scrapy能使用的Dict
        :return:
        '''
        itemDict = {}
        items = self.cookie.split(';')
        for item in items:
            key = item.split('=')[0].replace(' ', '')
            value = item.split('=')[1]
            itemDict[key] = value
        return itemDict

    if __name__ == "__main__":
    cookie = "你复制的cookie"
    trans = transCookie(cookie)
    print trans.stringToDict()
```
    
>## 补充说明：
>>只需要将你网页上的cookie复制到上述代码中直接运行就可以了

>## 使用cookie操作scrapy
>>### 直接撸代码

```python
    # -*- coding: utf-8 -*-
    import scrapy
    from scrapy.conf import settings #从settings文件中导入Cookie，这里也可以室友from scrapy.conf import settings.COOKIE
    
    class DemoSpider(scrapy.Spider):
    name = "demo"
    #allowed_domains = ["csdn.com"]
    start_urls = ["http://write.blog.csdn.net/postlist"]
    cookie = settings['COOKIE']  # 带着Cookie向网页发请求\
    headers = {
        'Connection': 'keep - alive',  # 保持链接状态
        'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.82 Safari/537.36'
    }
    def start_requests(self):
        yield scrapy.Request(url=self.start_urls[0],headers=self.headers,cookies=self.cookie)# 这里带着cookie发出请求

    def parse(self, response):
        print response.body
```

>>### 说明
>>> 这里是scrapy工程目录下spiders目录下的主要的解析网页的py文件相信学过scrapy的应该不会陌生，上述代码中的cookie值是放在Settings文件中的，因此使用的时候需要导入，当然你也可以直接将cookie粘贴到这个文件中

>## 注意
>>虽说这里使用直接使用cookie可以省去很多麻烦，但是cookie的生命周期特别的短，不过小型的项目足够使用了，向那些需要爬两三天甚至几个月的项目就不适用了，因此在隔一段时间就要重新换cookie的值，虽说有很多麻烦，但是我还是比较喜欢这种方法的，因为可以省去不少脑筋

>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持。

>### 最后欢迎大家看看我的其他scrapy文章
>> * [scrapy设置代理ip](https://chenjiabing666.github.io/2017/03/26/scrapy%E8%AE%BE%E7%BD%AE%E4%BB%A3%E7%90%86ip/)
>> * [scrapy架构初探](https://chenjiabing666.github.io/2017/03/25/scrapy%E6%9E%B6%E6%9E%84%E5%88%9D%E6%8E%A2/)
>> * [scrapy初试](https://chenjiabing666.github.io/2017/03/25/scrapy%E5%88%9D%E8%AF%95/)
>> * [scrapy下载器中间件](https://chenjiabing666.github.io/2017/03/25/scrapy%E7%9A%84%E4%B8%8B%E8%BD%BD%E5%99%A8%E4%B8%AD%E9%97%B4%E4%BB%B6/)
>> 







































*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*