---
title: scrapy初试
date: 2017-03-25 14:21:21
categories: Scrapy学习
tags: scrapy
---

# scrapy初试
>## 创建项目
>>打开`cmd`，在终端输入`scrapy startproject tutorial`,这里将在指定的文件夹下创建一个`scrapy`工程

>## 其中将会创建以下的文件：
>>* `scrapy.cfg`: 项目的配置文件
>>* `tutorial/`: 该项目的python模块。之后您将在此加入代码。
>>* `tutorial/items.py`: 项目中的item文件.
>>* `tutorial/pipelines.py`: 项目中的pipelines文件.
>> * `tutorial/settings.py`: 项目的设置文件.
>> * `tutorial/spiders/`: 放置spider代码的目录.

>定义item
>>`Item `是保存爬取到的数据的容器；其使用方法和`python`字典类似， 并且提供了额外保护机制来避免拼写错误导致的未定义字段错误。

>>类似在`ORM`中做的一样，您可以通过创建一个 `scrapy.Item` 类， 并且定义类型为 `scrapy.Field `的类属性来定义一个`Item`。 (如果不了解`ORM`, 不用担心，您会发现这个步骤非常简单)

>>首先根据需要从`dmoz.org`获取到的数据对`item`进行建模。 我们需要从`dmoz`中获取名字，`url`，以及网站的描述。 对此，在`item`中定义相应的字段。编辑 `tutorial` 目录中的 `items.py` 文件:

```python  
    import scrapy
    class DmozItem(scrapy.Item):
    title = scrapy.Field()
    link = scrapy.Field()
    desc = scrapy.Field()
```

>>一开始这看起来可能有点复杂，但是通过定义item， 您可以很方便的使用Scrapy的其他方法。而这些方法需要知道您的item的定义.

>## 编写第一个爬虫
>>>在工程的根目录下打开终端输入`scrapy genspider demo douban.com`
>>>这里的`demo`是`spders`文件下的主要`py`文件
>>>`douban.com`是要爬取的域名，会在`demo.py`中的 `allowed_domains`中显示，主要的功能就是限制爬取的`url`
>>### spider代码中内容解析
>>>* `name`: 用于区别`Spider`。 该名字必须是唯一的，您不可以为不同的`Spider`设定相同的名字。
>>* `start_urls`: 包含了`Spider`在启动时进行爬取的`url`列表。 因此，第一个被获取到的页面将是其中之一。 后续的`URL`则从初始的`URL`获取到的数据中提取。
>>* `parse()` 是spider的一个方法。 被调用时，每个初始`URL`完成下载后生成的 Response 对象将会作为唯一的参数传递给该函数。 该方法负责解析返回的数据(`response data`)，提取数据(生成`item`)以及生成需要进一步处理的`URL`的 `Request `对象。

>>### 以下是spider目录下的demo.py的代码

```python
    import scrapy

    class DmozSpider(scrapy.Spider):
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = [
            "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
    ]

    def parse(self, response):
        filename = response.url.split("/")[-2]
        with open(filename, 'wb') as f:
            f.write(response.body)
```

            
>## spider的爬取
>>进入工程的根目录下打开终端输入：`scrapy crawl dmoz`

>## spider中的数据存取
>>在工程的根目录下打开终端输入`scrapy crawl dmoz -o items.json`
>>这里是将数据存储到`json`文件中


    
    
