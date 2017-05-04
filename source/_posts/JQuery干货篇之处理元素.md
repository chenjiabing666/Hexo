---
title: JQuery干货篇之处理元素
date: 2017-04-22 21:45:23
categories: JQuery学习
tags: JQuery
---
# JQuery干货篇之处理元素
>**注意这里用的还是我前两篇用的例子，详情请看[我的博客](https://chenjiabing666.github.io/2017/04/20/JQuery%E5%B9%B2%E8%B4%A7%E7%AF%87%E4%B9%8B%E9%80%89%E6%8B%A9%E5%85%83%E7%B4%A0/)**
## attr
>**`attr()` 方法设置或返回被选元素的属性值。**

>**语法：**
>>* `$(selector).attr(attribute)` 返回被选元素的属性值。
>>* `$(selector).attr(attribute,value)` 设置被选元素的属性和值
>>* `$(selector).attr(attribute,function(index,oldvalue))` 设置被选元素的属性和值。

| 参数 |  描述   |
|:-----:|:-----:|
|  `attribute `    |    规定属性的名称。   |
|   `function(index,oldvalue)`    |  规定返回属性值的数。该函数可接收并使用选择器的 index 值和当前属性值。     |

>**实例：**
```javascript
    $("img").filter(":first").attr('src');   //得到属性

$("img").each(function (index,elem) {    
        if(index%2==0)
            $(elem).attr("src",'lily.png');      //设置属性
        console.log($(elem).attr("src"));
        })
        
        
        $("img").attr('src',function (index,oldValue) {  //这里的oldValue表示原来属性的值，index是索引
        if(oldValue=="rose.png")
            return 'lily.png';
        else
            return 'astor.png';
    })
    
    
    attrs={       //使用映射对象一次设置多个值
        src:'lily.png',
        style: 'border: thick double red'
    };
    $("img:eq(1)").attr(attrs);
```


## removeAttr
>**`removeAttr()` 方法从被选元素中移除属性。**

>**语法：**

>* `$(selector).removeAttr(attribute)`  这里的attribute是属性的名字

>**实例：**

```javascript
$("img:first").removeAttr("src");  //删除属性src
```

## addClass
>**`addClass()` 方法向被选元素添加一个或多个类**

>**语法：**

>* `$(selector).addClass(class)` 这里的class是类名如果需要添加多个类，中间用**空格**隔开

>* `$(selector).addClass(function(index,oldclass))`  这里的index是索引，oldClass是原来就有的类名，都是**可选参数**。这个函数的返回的就是要添加的类名

>**实例：**

```javascript
$("img:even").addClass("redBar");  //向偶数的img添加类redBar

$("img").addClass(function (index,currentClass) {    //这里的currentClass就是原来有的类名，可选
        if(index==1)
            return 'blueBar';   //第二个img应用blueBar这个类
        else
            return 'redBar';      //这里需要注意的是，对同一个img应用类的时候，因为这个类的定义有优先级，上面定义会被后面定义的覆盖，所以要注意类定义的位置
    })
    
    
    $("img").filter(":odd").addClass("redBar").end().filter(":even").addClass("blueBar");  //链式调用
    
    $("img").addClass("blueBar redBar");   //添加两个类
    
```

## hasClass
>**`hasClass()` 方法检查被选元素是否包含指定的`class`**

>**语法：**

>* `$(selector).hasClass(class)`  //返回值是false和true

>**实例：**

```javascript
console.log($("img:odd").hasClass("redBar"));     

```

## toggleClass
>**toggleClass() 对设置或移除被选元素的一个或多个类进行切换。该方法检查每个元素中指定的类。如果不存在则添加类，如果已设置则删除之。这就是所谓的切换效果**

>**语法：**

>* `$(selector).toggleClass(class,switch)`  `class`必需的，用来规定添加或移除`class`的指定元素，如需规定若干 `class`，请使用空格来分隔类名。`switch`是`boolean`可选参数，规定是否添加或移除`class`

>* `$(selector).toggleClass(function(index,class),switch)`   `index`表示索引，`class`表示选择器当前拥有的类

>**实例：**

```javascript
$("img").toggleClass("redBar");   //这里对所有的img在redBar这个类之间切换

$("img").toggleClass("redBar blueBar");  //在两个类之间来回的切换



$("<button>ToggleClass</button>").appendTo("#buttonDiv").click(function (e) {
        $("img").toggleClass('redBar blueBar');   //在两种class之间切换，如果有就删除，没有的就添加
        e.preventDefault();    
        })
        
        
        //下面添加一个按钮，完成同时添加多个图片的效果
    $("<button>ToggleClass</button>").appendTo("#buttonDiv").click(function (e) {
        $("img").toggleClass(function (index,currentClass) {
            if(index%2==0)
                return 'blueBar';   //动态的切换，这里是偶数就切换blue
            else
                return 'redBar blueBar';  //这里是奇数的图片在两种颜色来回的切换

        });
        e.preventDefault();

    })
```


## css
>**`css()` 方法返回或设置匹配的元素的一个或多个样式属性，这里只说`css`，还有其他的设置`css`样式请看[w3School](http://www.w3school.com.cn/jquery/jquery_ref_css.asp)**

>**语法：**

>* `$(selector).css(name)`  返回第一个匹配元素的 `CSS `属性值。`name`是`css`属性的名称

>* `$(selector).css(name,value)` 设置所有匹配元素的指定 `CSS` 属性。`name`表示属性名称，`value`表示属性的值
 
>* `$(selector).css(name,function(index,value))`   此函数返回要设置的属性值。接受两个参数，`index `为元素在对象集合中的索引位置，`value` 是原先的属性值。`name`表示要设置的属性名称，返回值就是要设置的属性值

>**实例：**

```javascript
$("label").css('font-size','30px');  //设置字体大小

$("label").css('font-size','+=10');  //使用相对值设置属性值，在原有的基础上加上10

console.log($("h1").css('font-family'));   //获取h1标签的字体

var cssValues={
    'border':'thick double red',
    'font-size':'1.5em'
};
$("label").css(cssValues);   //同时设置多个属性

```

## text
>**`text()` 方法方法设置或返回被选元素的文本内容。当该方法用于返回一个值时，它会返回所有匹配元素的组合的文本内容(会删除 `HTML` 标记)**

>**语法：**

>* `$(selector).text()`   当该方法用于返回一个值时，它会返回所有匹配元素的组合的文本内容（会删除 `HTML` 标记）。
>* `$(selector).text(content)`  当该方法用于设置值时，它会覆盖被选元素的所有内容。
>* `$(selector).text(function(index,oldcontent))`  `index`表示索引,`oldcontent`表示选择器当前的文本内容


## html
>**`html()` 方法返回或设置被选元素的内容 `(inner HTML)`。如果该方法未设置参数，则返回被选元素的当前内容。**

>**语法：**

>* `$(selector).html()`    当使用该方法返回一个值时，它会返回**第一个**匹配元素的内容。

>* `$(selector).html(content)`   当使用该方法设置一个值时，它会覆盖所有匹配元素的内容。

>* `$(selector).html(function(index,oldcontent)) `  使用函数来设置所有匹配元素的内容。`index` - 可选。接收选择器的`index` 位置,`oldcontent` - 可选。接收选择器的当前内容


## val
>**`val()` 方法返回或设置被选元素的值,元素的值是通过 `value` 属性设置的。该方法大多用于 `input` 元素,如果该方法未设置参数，则返回被选元素的当前值**

>**语法：**

>* `$(selector).val(value)`    设置文本域的值为value
>* `$(selector).val()`       得到文本域的值
>* `$(selector).val(function(index,oldvalue))`  设置文本域的值，这里函数的返回值将会用来设置文本域的值，`index`表示元素索引，`oldvalue`表示选择器当前文本域的值











 











>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*