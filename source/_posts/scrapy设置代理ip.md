---
title: scrapy设置代理ip
date: 2017-03-26 00:49:29
categories: Scrapy学习
tags: scrapy
---

# scrapy代理的设置
>在我的上一篇文章介绍了[scrapy下载器中间件的使用](https://chenjiabing666.github.io/2017/03/25/scrapy%E7%9A%84%E4%B8%8B%E8%BD%BD%E5%99%A8%E4%B8%AD%E9%97%B4%E4%BB%B6/),这里的scrapy`IP`的代理就是用这个原理实现的，重写了下载器中间件的`process_request(self,request,spider)`这个函数,这个函数的主要作用就是对request进行处理。
>>### 话不多说直接撸代码

```python
    import random  
    import scrapy
    import logging
    class proxMiddleware(object):
    #proxy_list=[{'http': 'http://123.157.146.116:8123'}, {'http': 'http://116.55.16.233:8998'}, {'http': 'http://115.85.233.94:80'}, {'http': 'http://180.76.154.5:8888'}, {'http': 'http://139.213.135.81:80'}, {'http': 'http://124.88.67.14:80'}, {'http': 'http://106.46.136.90:808'}, {'http': 'http://106.46.136.226:808'}, {'http': 'http://124.88.67.21:843'}, {'http': 'http://113.245.84.253:8118'}, {'http': 'http://124.88.67.10:80'}, {'http': 'http://171.38.141.12:8123'}, {'http': 'http://124.88.67.52:843'}, {'http': 'http://106.46.136.237:808'}, {'http': 'http://106.46.136.105:808'}, {'http': 'http://106.46.136.190:808'}, {'http': 'http://106.46.136.186:808'}, {'http': 'http://101.81.120.58:8118'}, {'http': 'http://106.46.136.250:808'}, {'http': 'http://106.46.136.8:808'}, {'http': 'http://111.78.188.157:8998'}, {'http': 'http://106.46.136.139:808'}, {'http': 'http://101.53.101.172:9999'}, {'http': 'http://27.159.125.68:8118'}, {'http': 'http://183.32.88.133:808'}, {'http': 'http://171.38.37.193:8123'}]
    proxy_list=[
        "http://180.76.154.5:8888",
        "http://14.109.107.1:8998",
        "http://106.46.136.159:808",
        "http://175.155.24.107:808",
        "http://124.88.67.10:80",
        "http://124.88.67.14:80",
        "http://58.23.122.79:8118",
        "http://123.157.146.116:8123",
        "http://124.88.67.21:843",
        "http://106.46.136.226:808",
        "http://101.81.120.58:8118",
        "http://180.175.145.148:808"]
    def process_request(self,request,spider):
        # if not request.meta['proxies']:
        ip = random.choice(self.proxy_list)
        print ip
        #print 'ip=' %ip
        request.meta['proxy'] = ip
```
        
>>## 主要的原理：
>>>给出一个代理列表，然后在这个列表中随机取出一个代理，设置在request中，其中`request.meta['proxy']`就是设置代理的格式

>**但是现在主要的问题就是没有代理ip可用，如果去买的话又太贵了，自己玩玩买代理不值当，所以只好自己写爬虫去爬取免费的代理了，但是免费的代理存活的时间是有限的，这是个非常麻烦的事情，我提供的方法就是实现自己的一个ip代理池，每天定时更新自己的代理池，具体的实现方法会在下一篇文章中介绍，现在提供一段代码用来爬
取西刺网站的代理**
>## 直接撸代码，接招吧

```python
    #coding:utf-8
    import requests
    from bs4 import BeautifulSoup
    import threading
    import Queue
    class Get_ips():
    def __init__(self,page):
        self.ips=[]
        self.urls=[]
        for i in range(page):
            self.urls.append("http://www.xicidaili.com/nn/" + str(i))
        self.header = {"User-Agent": 'Mozilla/5.0 (Windows NT 6.3; WOW64; rv:43.0) Gecko/20100101 Firefox/43.0'}
        #self.file=open("ips",'w')
        self.q=Queue.Queue()
        self.Lock=threading.Lock()
    def get_ips(self):
        for url in self.urls:
            res = requests.get(url, headers=self.header)
            soup = BeautifulSoup(res.text, 'lxml')
            ips = soup.find_all('tr')
            for i in range(1, len(ips)):
                ip = ips[i]
                tds = ip.find_all("td")
                ip_temp = "http://" + tds[1].contents[0] + ":" + tds[2].contents[0]
                # print str(ip_temp)
                self.q.put(str(ip_temp))

    def review_ips(self):
        while not self.q.empty():
            ip=self.q.get()
            try:
                proxy={"http": ip}
                #print proxy
                res = requests.get("http://www.baidu.com", proxies=proxy,timeout=5)
                self.Lock.acquire()
                if res.status_code == 200:
                    self.ips.append(ip)
                    print ip
                    self.Lock.release()
            except Exception:
                pass
                #print 'error'
    def main(self):
        self.get_ips()
        threads=[]
        for i in range(40):
            threads.append(threading.Thread(target=self.review_ips,args=[]))
        for t in threads:
            t.start()
        for t in threads:
            t.join()
        return self.ips
    def get_ip():
    my=Get_ips(4)
    return my.main()
    get_ip()
```

>>### 实现的原理
>>>这里用到了BeautifulSoup解析页面，然后将提取到的代理交给队列，然后再通过共享队列分配给线程，这里主要开启线程通过设置代理ip访问一个网站，因为访问网站的时间比较长，因此要开起多个线程，相信大家能够学习设置代理ip了应该都是比较上手的了，这里具体的代码就不一一解释了，如果代码有什么问题可以及时联系我，我的联系方式在**关于我**的一栏中有提到

>### 补充
>>想要ip应用起来，还要在配置文件`settings`中添加`DOWNLOADER_MIDDLEWARES = {
'demo.proxy.proxMiddleware':400
}`这里的demo是工程的名字，proxy是py文件的名,proxMiddleware是类的名字

>>>当然这里可能你觉得proxy_list写在这里有点冗余，你可以在配置文件中定义，然后将配置文件的内容`import`到py文件中

>**以上全是博主慢慢摸索出来的，可以说自学一门技术真的很难，学习python爬虫已经有两三个月了，可以说全是自己通过看项目，网上查资料才有了今天的成功，不过现在还有几个问题没有解决，就是分布式爬虫、移动端爬取，博主接下来就要主攻这两个方面，学好之后会在自己的博客上分享学习心得的，因为网上没有系统的学习教程，对于自学的人来说实在是太痛苦了**






















































*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*