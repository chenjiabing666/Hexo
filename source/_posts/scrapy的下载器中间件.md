---
title: scrapy的下载器中间件
date: 2017-03-25 15:04:08
categories: Scrapy学习
tags: scrapy
---

# scrapy中的下载器中间件
## 下载中间件
>下载器中间件是介于Scrapy的request/response处理的钩子框架。 是用于全局修改Scrapy request和response的一个轻量、底层的系统。
>>## 编写下载器中间件
>>>### 1. `process_request(request, spider)`

>>>当每个`request`通过下载中间件时，该方法被调用。
>>>`process_request()` 必须返回其中之一: 返回 `None` 、返回一个 `Response` 对象、返回一个 `Request `对象或`raise IgnoreRequest` 。

>>>如果其返回 `None` ，Scrapy将继续处理该`request`，执行其他的中间件的相应方法，直到合适的下载器处理函数(`download handler`)被调用， 该`request`被执行(其`response`被下载)。

>>>如果其返回 `Response` 对象，Scrapy将不会调用 任何 其他的 `process_request()` 或 `process_exception()` 方法，或相应地下载函数； 其将返回该`response`。 已安装的中间件的 `process_response()` 方法则会在每个`response`返回时被调用。

>>>如果其返回 `Request` 对象，Scrapy则停止调用 `process_request`方法并重新调度返回的`request`。当新返回的`request`被执行后， 相应地中间件链将会根据下载的`response`被调用。

>>>如果其`raise`一个 `IgnoreRequest` 异常，则安装的下载中间件的 `process_exception()` 方法会被调用。如果没有任何一个方法处理该异常， 则`request`的`errback(Request.errback)`方法会被调用。如果没有代码处理抛出的异常， 则该异常被忽略且不记录(不同于其他异常那样)。

>>>参数:	
>>>>* `request` (`Request` 对象) – 处理的`request`
>>>>* `spider` (`Spider` 对象) – 该`request`对应的`spider`

>>>### 2. `process_response(request, response, spider)`
>>>>`process_response()` 必须返回以下之一: 返回一个 `Response `对象、 返回一个` Request` 对象或`raise`一个 `IgnoreRequest` 异常。

>>>>如果其返回一个 `Response` (可以与传入的`response`相同，也可以是全新的对象)， 该`response`会被在链中的其他中间件的 `process_response()` 方法处理。

>>>>如果其返回一个 `Request` 对象，则中间件链停止， 返回的`request`会被重新调度下载。处理类似于 `process_request()` 返回`request`所做的那样。

>>>>如果其抛出一个 `IgnoreRequest` 异常，则调用`request的errback(Request.errback)。` 如果没有代码处理抛出的异常，则该异常被忽略且不记录(不同于其他异常那样)。

>>>>参数:	
>>>>>>* `request` (`Request `对象) – `response`所对应的request
>>>>>>* `response` (`Response` 对象) – 被处理的response
>>>>>>* `spider` (`Spider` 对象) – `response`所对应的`spider`

>>>### 3.`process_exception(request, exception, spider)`
>>>>当下载处理器(`download handler`)或 `process_request()` (下载中间件)抛出异常(包括 `IgnoreRequest` 异常)时， Scrapy调用 `process_exception()` 。

>>>>`process_exception()` 应该返回以下之一: 返回 `None` 、 一个 `Response` 对象、或者一个 `Request` 对象。

>>>>如果其返回 `None` ，Scrapy将会继续处理该异常，接着调用已安装的其他中间件的 `process_exception()` 方法，直到所有中间件都被调用完毕，则调用默认的异常处理。

>>>>如果其返回一个 `Response` 对象，则已安装的中间件链的 `process_response()` 方法被调用。Scrapy将不会调用任何其他中间件的 `process_exception()` 方法。

>>>>如果其返回一个 `Request` 对象， 则返回的`request`将会被重新调用下载。这将停止中间件的 `process_exception()` 方法执行，就如返回一个`response`的那样。

>>>>参数:	
>>>>>* `request` (是 `Request` 对象) – 产生异常的`request`
>>>>>* `exception` (`Exception` 对象) – 抛出的异常
>>>>>* `spider` (`Spider` 对象) – `request`对应的`spider`

>> ## 总结：
>>>总的来说下载器中间件就是起到处理request请求并且返回response的作用，一切从网页爬取的url发起的请求会组成一个请求队列，然后一个一个排队经过下载器中间件，之后下载器中间件会对request做出相应的处理，比如添加请求头，添加代理等等，然后通过process_response返回一个response，之后就是用得到的response做出相应的分析，当然这里的内容页可以不实现，但是如果要爬取大型的网站，会遇到被ban的可能就要在下载器中间件这里着手，设置一些相应的请求头，ip代理等等内容。
>>>**以上纯属个人逐渐摸索总结出来的内容，如果有什么错误欢迎指正**

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*