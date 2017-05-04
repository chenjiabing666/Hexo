---
title: JQuery干货篇之操控DOM
date: 2017-04-21 17:53:16
categories: JQuery学习
tags: JQuery
---
# JQuery干货篇之插入元素

**本次使用的html,css还是我上一篇的源代码，详情请看[上一篇文章](https://chenjiabing666.github.io/2017/04/20/JQuery%E5%B9%B2%E8%B4%A7%E7%AF%87%E4%B9%8B%E9%80%89%E6%8B%A9%E5%85%83%E7%B4%A0/)**

## 分类

1. **插入子元素：`append`,`prepend` ,`appendTo`,`prependTo`**
2. **封装包裹元素：`wrap`,`wrapAll`,`wrapInner`**
3. **插入兄弟元素：`after`,`before`,`insertAfter`,`insertBefore`**
4. **替换元素：`replaceWith`,`replaceAll`**
5. **删除元素：`remove`,`deatch`,`unwrap`,`empty`**

## 创建新元素
**通常在把新元素插入到`DOM`中的目标位置之前，要先创建一个新元素才能将它插入到指定位置**

>**使用`$`创建元素**
>>`$(<div><img src='rose.png' alt='玫瑰'></div>)`

## clone
>**克隆元素，使用`clone`方法以已有的元素为模子生成新的元素，这个在后面的插入元素起到关键作用，如果在要引用html中的一个标签内容的话，不使用`clone`方法，那么就会将这段内容移动，因此这里使用`clone`会很方便，详细请看`append`的用法实例**

>**实例：**

```javascript
    $("div.dcell").clone();    //这里的clone方法必须是JQuery对象调用
```

## 使用DOM API创建新元素
>**`DOM API`是用`js`操作的，其实`jquery`在幕后悄悄的调用`DOM API`**

>**实例：**

```javascript
     var divElem=document.createElement("div");    //创建一个div元素
     divElem.classList.add("dcell");       //为div添加class=dcell

    var imgElem=document.createElement("img");
    imgElem.src="lily.png";

    divElem.appendChild(imgElem);   //在新创建的元素后面插入img

    var newElem=$(divElem);

    newElem.each(function (index,elem) {
        console.log(elem.tagName+"    "+elem.className);

    });

```


## append
>**把参数指定的元素插入到所有的`JQuery`内含元素内容末尾成为他们的最后一个子元素，形式有`append(html)`,`append(Jquery)`,`append(HTMLElements[])`，`append(function())`**

>**实例：**

```javascript
//这里使用append元素创建了一个div元素，在末尾插入元素成为div的子元素
//
    var orchildElems = $("<div class='dcell'></div>").append("<img src='orchid.png'/>")
        .append("<label for='orchild'>Orchild:</label>")
        .append("<input name='orchild' value='0' required>");

    var newElems = $("<div class='dcell'></div>").append("<img src='lily.png'/>")
        .append("<label for='lily'>Lily:</label>")
        .append("<input name='lily' value='0' required>")
        .css("border", 'thick double red');
        
    $("div.drow").append(orchildElems);   //在末尾插入数据，这里的参数是jquery对象
    
    
    $("div.drow").append(function(index,elem){
    
    if(elem.id=='row1')
        return orchildElems;
    
    else if(this.id='row2')
        return newElems;
    })
    
    
    $("div.drow").last().append(orchildElem,newElems);   //在其中添加两个参数，插入的先后按照参数的先后位置，当然其中的参数个数没有限制
    
    
    
        
```

## prepend
>**和`append`完全相反,向当前元素的前面插入`html`节点作为当前元素的子元素,形式有`prepen d(Jquery)`,`prepend(html)`,`prepend(htmlElemnts[])`,`prepend(function())`**

>**实例：**

```javascript
      var orchildElems = $("<div class='dcell'></div>").append("<img src='orchid.png'/>")
        .append("<label for='orchild'>Orchild:</label>")
        .append("<input name='orchild' value='0' required>");
    $("div.dcell").prepend(orchildElems);    //将orchildElems插入到div.dcell的最前面，作为他的子元素
    
    
    $("div.dcell").prepend("<img src='lily.png'>"); //将参数html的内容插入到前面，作为子元素
    
    
    
    $("div.drow").append(function (index) {     //参数是函数，index是索引，返回的内容就是要插入到前面的内容

         if (this.id == 'row1')
             return orchildElem;                //返回的对象可以是jquery对象，也可以是html标签，如：return "<img src='lily.png'>

         else if (this.id = 'row2')
             return newElems;
     });
    

```


## appendTo
>**`appendTo`是和`append`一样的函数，都是将指定的元素插入到指定元素的前面作为子元素，但是他们的参数就不同了，`append`是将指定的参数插入到当前调用它的的结果集中，而`appendTo`是将当前调用它的结果集插入到指定的参数中，主要的形式有`appendTo(jquery)`,`append(HTMLELments[])`**

>**实例：**

```javascript

$("<img src='lily.png'>").appendTo($("img").last().parent());   //将图片插入到最后一个dcell中，这里参数是目标位置，开头调用的时想要插入的内容

$("img:first").clone().appendTo($("img").last().parent()); //选择第一个图片插入到最后一个dcell中，这里必须用clone，否则就会将这张图片移到目标位置

 $($("div.dcell").html()).appendTo($("img").last().parent());   //这里的.html()是获取html文本内容

```

## prependTo
>**`.prepend()`和`.prependTo()`实现同样的功能，主要的不同是语法，插入的内容和目标的位置不同。 对于 `.prepend()` 而言，选择器表达式写在方法的前面，作为待插入内容的容器，将要被插入的内容作为方法的参数。而 `.prependTo()` 正好相反，将要被插入的内容写在方法的前面，可以是选择器表达式或动态创建的标记，待插入内容的容器作为参数。**


## after
>**在匹配元素集合中的每个元素后面插入参数所指定的内容，作为其兄弟节点。形式有`after(content[content,])`,`after(function())`,这里的`content`内容有HTML字符串，`DOM` 元素，文本节点，元素和文本节点的数组，或者`jQuery`对象，用来插入到集合中每个匹配元素的后面**

>**实例：**

```javascript
      var orchildElems = $("<div class='dcell'></div>").append("<img src='orchid.png'/>")
        .append("<label for='orchild'>Orchild:</label>")
        .append("<input name='orchild' value='0' required>");     //创建一个dcell内容
        
    
        $("div.dcell").after(orchildElems);   //插入元素作为兄弟元素，在当前元素的后面
        
        
        $("#row1 div.dcell").after(function (index, html) {    //index表示索引，html表示原来的html文本，指的是没有插入之前的html
        console.log(html);
        if (index == 0)return orchildElem;        //返回的可以是jquery对象，html文本
        else if (index == 1)
            return newElems;
    });
});
        
```

## before
>**根据参数设定，在匹配元素的前面插入内容,形式和`after`一样，内容也差不多**


## insertBefore
>**和`prependTo`的用法差不多，只是参数是要插入的目标位置，作为兄弟元素插入**

>**实例：**

```javascript
orchildElems.clone().insertBefore("#row2 div.dcell");
```

## insertAfter
>**和`append`用法差不多，只是参数是要插入的目标位置，这里的也是作为兄弟元素插入的**

>**实例：**

```javascript
orchildElems.insertAfter("#row1 div.dcell");

```

## wrap
>**在集合中匹配的每个元素周围包裹一个`HTML`结构，将会作为父元素存在。形式为`wrap(html)`,`wrap(jquery)`,`wrap(HtmlElements[])`,`wrap(function())`**

>**实例：**

```javascript
    div=$("<div></div>").css("border",'thick double red');
    $("div.drow").wrap(div);     //在drow外层添加了一个div将作为父元素，可以看到现在的源代码变成了<div style...><div class='drow'>...</div></div>
    
    
    $(".drow").wrap(function (index) {   //index是索引
    //if($(this).has("img[src*=astor]").length>0)
    if(index==0)
        return div;      //只在第一个drow中添加父元素div
    else 
        return $("<div></div>").css("border",'thick double blue');
})

```

## unwrap
>**将匹配元素集合的父级元素删除，保留自身（和兄弟元素，如果存在）在原来的位置。形式为`unwrap()`,`unwrap(selector)`**

>**实例：**

```javascript
  $("div.dcell").css("border",'thick double red');
    $("div.dcell").children("img").first().unwrap();   //这里将第一个img元素的父级元素删除，并且保留了其中的子元素
    
    $("div.dcell").children("img").unwrap(":first");   //这里使用参数来筛选要删除父级元素的当前元素，这里选择第一个元素

```

## wrapAll
>**在集合中所有匹配元素的外面包裹一个HTML结构,也就是为结果集中的所有元素都设置了一个相同的父级元素来包裹所有的元素，形式为`wrapAll(html)`,`wrapAll(jquery)`,`wrapAll(htmlElements[])`,`wrapAll(function())`**

>**实例：**

```javascript
var div = $("<div></div>").css("border", 'thick double red');
$("div.drow").wrapAll(div);    //这里的div成为了他共有的父级元素，原来的父级元素变成了祖先元素了
$("img").wrapAll(div);  //这里的img没有共同的父元素，那么就会强制的将所有的元素拉在一起为他们设置一个父级元素



```

## wrapInner
>**在匹配元素里的内容外包一层结构,也就是为匹配元素的后代元素添加一个父级元素，但是这个父级元素是匹配元素的子代元素，也就是原来的匹配元素变成了祖先元素，形式为`wrapInner(html)`,`wrapInner(jquery)`,`wrapInner(htmlElements)`,`wrapInner(function())`**

>**实例：**

```javascript
    var div = $("<div></div>").css("border", 'thick double red');
    $(".dcell").wrapInner(div);    //这里的dcell元素将会变成祖先元素，而div将会变成内部后代元素新的父级元素

```


## replaceWith
>**用提供的内容替换集合中所有匹配的元素并且返回被删除元素的集合,形式为`replace(html)`,`replaceWith(jquery)`,`replaceWith(function())`**

>**实例：**

```javascript
 var newElems = $("<div class='dcell'></div>").append("<img src='lily.png'>")
 .append("<label for='lily'>Lily</label>").append("<input name='lily' value='0' required>").css("border", 'thick   double blue');
$(".dcell:first").replaceWith(newElems);  //用newElems替换第一个dcell


$("div.drow img").replaceWith(function () {
    if (this.src.indexOf("rose") > -1)
        return $("<img src='lily.png'>").css("border",'thick double red'); //返回的时替换的内容，可以是jquery或者html
    else if (this.src.indexOf("peony") > -1)
        return newElems;
    else return $(this.clone()).css("border",'thick double blue');

})
```

## replaceAll
>**用集合的匹配元素替换每个目标元素。`.replaceAll()`和`.replaceWith()`功能类似，但是目标和源相反**

>**实例：**

```javascript
 $("<img src='lily.png'>").replaceAll("#row1 img");   //这里使用<img src='lily.png'>替换所有的img元素
```

## remove
>**将匹配元素集合从`DOM`中删除,并且同时移除元素上的事件及 `jQuery` 数据**

>**实例：**

```javascript
$("div.dcell").remove(":has(img[src*=rose])");  //删除img

$("div.dcell:first()").remove();    //不带参数


```

## detach
>**从`DOM`中去掉所有匹配的元素,`.detach()` 方法和`.remove()`一样, 除了 `.detach()`保存所有`jQuery`数据和被移走的元素相关联。当需要移走一个元素，不久又将该元素插入DOM时，这种方法很有用。**

>**实例：**

```javascript
$("div.dcell").detach();

$("div.dcell").detach(":has(img[src*=rose])"); 

```

## empty
>**从`DOM`中移除集合中匹配元素的所有子节点。**

```javascript=
$("div.dcell:first").empty();   //删除所有的子节点
```
    
## 参考文章
>* [JQuery中文文档](http://www.css88.com/jqapi-1.9/)




























>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*