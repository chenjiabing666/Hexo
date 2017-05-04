---
title: scrapy设置请求池
date: 2017-03-26 14:20:10
categories: Scrapy学习
tags: scrapy
---

# scrapy设置"请求池"
## 引言
>相信大家有时候爬虫发出请求的时候会被ban，返回的是403错误，这个就是请求头的问题，其实在python发出请求时，使用的是默认的自己的请求头，网站管理者肯定会不允许机器访问的，但是有些比较low的网站还是可以访问的，有时候网站管理者看到同一个请求头在一秒内请求多次，傻子都知道这是机器在访问，因此会被ban掉，这时就需要设置请求池了，这个和ip代理池是一个概念
## 爬虫请求常见的错误
>>200：请求成功      处理方式：获得响应的内容，进行处理   
201：请求完成，结果是创建了新资源。新创建资源的 URI 可在响应的实体中得到    处理方式：爬虫中不会遇到   
202：请求被接受，但处理尚未完成    处理方式：阻塞等待   
204：服务器端已经实现了请求，但是没有返回新的信 息。如果客户是用户代理，则无须为此更新自身的文档视图。    处理方式：丢弃 
300：该状态码不被 HTTP/1.0 的应用程序直接使用， 只是作为 3XX 类型回应的默认解释。存在多个可用的被请求资源。    处理方式：若程序中能够处理，则进行进一步处理，如果程序中不能处理，则丢弃 
301：请求到的资源都会分配一个永久的 URL，这样就可以在将来通过该 URL 来访问此资源    处理方式：重定向到分配的 URL   
302：请求到的资源在一个不同的 URL 处临时保存     处理方式：重定向到临时的 URL   
304 请求的资源未更新     处理方式：丢弃   
400 非法请求     处理方式：丢弃   
401 未授权     处理方式：丢弃   
403 禁止     处理方式：丢弃   
404 没有找到     处理方式：丢弃   
5XX 回应代码以“5”开头的状态码表示服务器端发现自己出现错误，不能继续执行请求    处理方式：丢弃

## 话不多说直接撸代码
```python
    from scrapy import log
    import random
    from scrapy.downloadermiddlewares.useragent import UserAgentMiddleware
    class RotateUserAgentMiddleware(UserAgentMiddleware):
    # for more user agent strings,you can find it in http://www.useragentstring.com/pages/useragentstring.php
    user_agent_list = [
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.1 "
        "(KHTML, like Gecko) Chrome/22.0.1207.1 Safari/537.1",
        "Mozilla/5.0 (X11; CrOS i686 2268.111.0) AppleWebKit/536.11 "
        "(KHTML, like Gecko) Chrome/20.0.1132.57 Safari/536.11",
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.6 "
        "(KHTML, like Gecko) Chrome/20.0.1092.0 Safari/536.6",
        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.6 "
        "(KHTML, like Gecko) Chrome/20.0.1090.0 Safari/536.6",
        "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.1 "
        "(KHTML, like Gecko) Chrome/19.77.34.5 Safari/537.1",
        "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/536.5 "
        "(KHTML, like Gecko) Chrome/19.0.1084.9 Safari/536.5",
        "Mozilla/5.0 (Windows NT 6.0) AppleWebKit/536.5 "
        "(KHTML, like Gecko) Chrome/19.0.1084.36 Safari/536.5",
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1063.0 Safari/536.3",
        "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1063.0 Safari/536.3",
        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_0) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1063.0 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1062.0 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1062.0 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1061.1 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1061.1 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1061.1 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1061.0 Safari/536.3",
        "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/535.24 "
        "(KHTML, like Gecko) Chrome/19.0.1055.1 Safari/535.24",
        "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/535.24 "
        "(KHTML, like Gecko) Chrome/19.0.1055.1 Safari/535.24"
    ]
    def process_request(self, request, spider):
        ua = random.choice(self.user_agent_list)
        if ua:
            # 显示当前使用的useragent
            print "********Current UserAgent:%s************" % ua
            # 记录
            log.msg('Current UserAgent: ' + ua)
            request.headers.setdefault('User-Agent', ua)
```
           
## 说明
>>这里的思路就是在下载器中间件中对request设置请求，这里是使用`request.headers.setdefault("User-Agent",user_agent)`这个函数设置请求头，对于下载器中间件在我博客前面的文章已经有说明，想要了解的请[点击](https://chenjiabing666.github.io/2017/03/25/scrapy%E7%9A%84%E4%B8%8B%E8%BD%BD%E5%99%A8%E4%B8%AD%E9%97%B4%E4%BB%B6/)

## 注意
>> 这里还要说明的是设置了请求池还要在配置文件settins中设置一下，具体设置方法和设置代理ip一样，详情请看[scrapy代理ip的设置](https://chenjiabing666.github.io/2017/03/26/scrapy%E8%AE%BE%E7%BD%AE%E4%BB%A3%E7%90%86ip/)












>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*