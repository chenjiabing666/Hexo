---
title: Scrapyd部署爬虫
date: 2017-04-24 10:58:07
categories: Scrapy学习
tags: scrapy
---
# Scrapyd部署爬虫

## 准备工作
>* **`安装scrapyd: pip install scrapyd`**
>* **安装`scrapyd-client : pip install scrapyd-client`**
>* **`安装curl:[安装地址](http://ono60m7tl.bkt.clouddn.com/curl.exe)`,安装完成以后将所在目录配置到环境变量中**

## 开始部署

>* **修改`scrapy`项目目录下的`scrapy.cfg`文件，修改如下**
```python
[deploy:JD_Spider]    #加上target   :name
url = http://localhost:6800/   #将前面的#删除
project = JD               #project的名字，可以使用默认的，当然也可以改变

```

>* **在任意目录下的打开终端，输入`scrapyd`,观察是否运行成功，运行成功的话，就可以打开`http://localhost:6800`看是否正常显示，如果正常显示则看到下面的这张图,这里的`JD`是部署之后才能看到的，现在是看不到的，所以没出现也不要担心：**

>![scrapyd](http://ono60m7tl.bkt.clouddn.com/scrayd.bmp)

>* **在项目的根目录下运行如下的命令：`python E:\python2.7\Scripts\scrapyd-deploy target -p project`,这里的E:\python2.7\Scripts\是你的python安装目录，Scripts是安装目录下的一个文件夹，注意前面一定要加上python,target是在前面scrapy.cfg中设置的deploy:JD_Spider，JD_Spider就是target,project 是JD,因此这个完整的命令是`python E:\python2.7\Scripts\scrapyd-deploy JD_Spider -p JD`,现在项目就部署到上面了，这下网页上就有`JD`了，详情请见上图**

>* **验证是否成功，你可以在网页上看有没有显示你的工程名字，另外在根目录下输入`python E:\python2.7\Scripts\scrapyd-deploy -l`就能列出你所有部署过的项目了**

>* **启动爬虫：`curl http://localhost:6800/schedule.json -d project=myproject -d spider=spider_name`,这里的`project`填入的是项目名，`spider_name`填入的是你的爬虫中定义的`name`,运行我的实例完整的代码为：`curl http://localhost:6800/schedule.json -d project=JD -d spider=spider`，这里将会显示如下信息：**

```python
#这里的jobid比较重要，下面会用到这个取消爬虫
{"status": "ok", "jobid": "3013f9d1283611e79a63acb57dec5d04", "node_name": "DESKTOP-L78TJQ7"}
```

>* **取消爬虫：`curl http://localhost:6800/cancel.json -d project=myproject -d job=jobid`,`jobid`就是上面的提到过的，如果取消我的这个实例代码如：`curl http://localhost:6800/cancel.json -d project=JD -d job=3013f9d1283611e79a63acb57dec5d04`,那么它的状态就会变成如下：**

```python
{"status": "ok", "prevstate": "running", "node_name": "DESKTOP-L78TJQ7"}
```
>* **列出项目：`curl http://localhost:6800/listprojects.json`,下面将会出现你已经部署的项目**

>* **删除项目：`curl http://localhost:6800/delproject.json -d project=myproject`**

>* **列出版本：`curl http://localhost:6800/listversions.json?project=myproject`,这里的`project`是项目的名字，是在scrapy.cfg设置的**

>* **列出爬虫：`curl http://localhost:6800/listspiders.json?project=myproject`这里的`project`是项目的名字，是在scrapy.cfg设置的**

>* **列出`job`:`curl http://localhost:6800/listjobs.json?project=myproject`这里的`project`是项目的名字，是在`scrapy.cfg`设置的**

>* **删除版本：`curl http://localhost:6800/delversion.json -d project=myproject -d version=r99`，这里的`version`是自己的项目版本号，在删除之前需要查看版本号**






>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*