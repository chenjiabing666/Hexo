---
title: python爬虫大战京东商城
date: 2017-04-23 18:34:48
categories: python
tags: python爬虫
---
# python大规模爬取京东
## 主要工具
>* **`scrapy`**
>* **`BeautifulSoup`**
>* **`requests`**

## 分析步骤

>* **打开京东首页，输入**裤子**将会看到页面跳转到了[这里](https://search.jd.com/Search?keyword=%E8%A3%A4%E5%AD%90&enc=utf-8&wq=%E8%A3%A4%E5%AD%90&pvid=a424f5c84d7844aaa56d4d62286878be)，这就是我们要分析的起点**

>* **我们可以看到这个页面并不是完全的，当我们往下拉的时候将会看到图片在不停的加载，这就是`ajax`,但是当我们下拉到底的时候就会看到整个页面加载了60条裤子的信息，我们打开chrome的调试工具，查找页面元素时可以看到每条裤子的信息都在`<li class='gl-item'></li>`这个标签中，如下图：**

>![生成图](http://ono60m7tl.bkt.clouddn.com/jd1.bmp)

>* **接着我们打开网页源码就会发现其实网页源码只有前30条的数据，后面30条的数据找不到，因此这里就会想到ajax，一种异步加载的方式，于是我们就要开始抓包了，我们打开chrome按F12，点击上面的NetWork,然后点击XHR,这个比较容易好找,下面开始抓包，如下图：**

>![抓包图](http://ono60m7tl.bkt.clouddn.com/jd2.bmp)

>* **从上面可以找到请求的`url`，发现有很长的一大段，我们试着去掉一些看看可不可以打开，简化之后的`url`=https://search.jd.com/s_new.php?keyword=%E8%A3%A4%E5%AD%90&enc=utf-8&qrst=1&rt=1&stop=1&vt=2&offset=3&wq=%E8%A3%A4%E5%AD%90&page={0}&s=26&scrolling=y&pos=30&show_items={1}**
>**这里的`showitems`是裤子的`id`,`page`是翻页的，可以看出来我们只需要改动两处就可以打开不同的网页了，这里的`page`很好找，你会发现一个很好玩的事情，就是主网页的`page`是奇数，但是异步加载的网页中的`page`是偶数，因此这里只要填上偶数就可以了，但是填奇数也是可以访问的。这里的`show_items`就是`id`了，我们可以在页面的源码中找到，通过查找可以看到`id`在`li`标签的`data-pid`中，详情请看下图**

>![id](http://ono60m7tl.bkt.clouddn.com/jd3.bmp)

>* **上面我们知道怎样找参数了，现在就可以撸代码了**

## 代码讲解

>* **首先我们要获取网页的源码，这里我用的requests库，安装方法为`pip install requests`，代码如下:**
```python
    def get_html(self):
        res = requests.get(self.url, headers=self.headers)
        html = res.text     
        return html    #返回的源代码
```

>* **根据上面的分析可以知道，第二步就是得到异步加载的url中的参数`show_items`,就是`li`标签中的`data-pid`,代码如下：**

```python
    def get_pids(self):
        html = self.get_html()
        soup = BeautifulSoup(html, 'lxml')    #创建BeautifulSoup对象
        lis = soup.find_all("li", class_='gl-item')   #查找li标签
        for li in lis:
            data_pid = li.get("data-pid")      #得到li标签下的data-pid
            if (data_pid):
                self.pids.add(data_pid)    #这里的self.pids是一个集合，用于过滤重复的
```

>* **下面就是获取前30张图片的url了，也就是主网页上的图片，其中一个问题是img标签的属性并不是一样的，也就是源码中的`img`中不都是`src`属性，一开始已经加载出来的图片就是src属性，但是没有加载出来的图片是`data-lazy-img`，因此在解析页面的时候要加上讨论。代码如下：**

```python
    def get_src_imgs_data(self):
        html = self.get_html()
        soup = BeautifulSoup(html, 'lxml')
        divs = soup.find_all("div", class_='p-img')  # 图片
        # divs_prices = soup.find_all("div", class_='p-price')   #价格
        for div in divs:
            img_1 = div.find("img").get('data-lazy-img')  # 得到没有加载出来的url
            img_2 = div.find("img").get("src")  # 得到已经加载出来的url
            if img_1:
                print img_1
                self.sql.save_img(img_1)
                self.img_urls.add(img_1)
            if img_2:
                print img_2
                self.sql.save_img(img_2)
                self.img_urls.add(img_2)
```

>**前三十张图片找到了，现在开始找后三十张图片了，当然是要请求那个异步加载的`url`，前面已经把需要的参数给找到了，下面就好办了，直接贴代码：**

```python
    def get_extend_imgs_data(self):
        # self.search_urls=self.search_urls+','.join(self.pids)
        self.search_urls = self.search_urls.format(str(self.search_page), ','.join(self.pids))  #拼凑url,将获得的单数拼成url,其中show_items中的id是用','隔开的，因此要对集合中的每一个id分割，page就是偶数，这里直接用主网页的page加一就可以了
        print self.search_urls
        html = requests.get(self.search_urls, headers=self.headers).text   #请求
        soup = BeautifulSoup(html, 'lxml')   
        div_search = soup.find_all("div", class_='p-img')   #解析
        for div in div_search:  
            img_3 = div.find("img").get('data-lazy-img')    #这里可以看到分开查找img属性了
            img_4 = div.find("img").get("src")

            if img_3:    #如果是data-lazy-img
                print img_3
                self.sql.save_img(img_3)    #存储到数据库
                self.img_urls.add(img_3)      #用集合去重
            if img_4:    #如果是src属性
                print img_4
                self.sql.save_img(img_4)     
                self.img_urls.add(img_4)
```


>* **通过上面就可以爬取了，但是还是要考虑速度的问题，这里我用了多线程，直接每一页面开启一个线程，速度还是可以的，感觉这个速度还是可以的，几分钟解决问题，总共爬取了`100`个网页,这里的存储方式是`mysql`数据库存储的，要用发哦`MySQLdb`这个库，详情自己百度，当然也可以用mogodb但是还没有学呢，想要的源码的朋友请看[GitHub源码](https://github.com/chenjiabing666/JD_Spider_python/tree/master)**


## 拓展
**写到这里可以看到搜索首页的网址中`keyword`和`wq`都是你输入的词，如果你想要爬取更多的信息，可以将这两个词改成你想要搜索的词即可，直接将汉字写上，在请求的时候会自动帮你编码的，我也试过了，可以抓取源码的，如果你想要不断的抓取，可以将要搜索的词写上文件里，然后从文件中读取就可以了。以上只是一个普通的爬虫，并没有用到什么框架，接下来将会写`scrapy`框架爬取的，请继续关注我的博客哦！！！**














































>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*