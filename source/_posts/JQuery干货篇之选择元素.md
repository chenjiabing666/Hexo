---
title: JQuery干货篇之选择元素
date: 2017-04-20 14:55:07
categories: JQuery学习
tags: JQuery
---
# JQuery 干货篇之选择元素


## 实验的HTML+CSS的代码

>**html**
```html
<!DOCTYPE html>
<html>
<head>
    <title>Example</title>
    <script src="jquery-3.2.1.min.js" type="text/javascript"></script>
    <link rel="stylesheet" type="text/css" href="main.css"/>
    <script src="main.js" type="text/javascript"></script>
</head>
<body>
<h1>Jacqui's Flower Shop</h1>
<form method="post">
    <div id="oblock">
        <div class="dtable">
            <div id="row1" class="drow">
                <div class="dcell">
                    <img src="astor.png"/><label for="astor">Astor:</label>
                    <input name="astor" value="0" required>
                </div>
                <div class="dcell">
                    <img src="daffodil.png"/><label for="daffodil">Daffodil:</label>
                    <input name="daffodil" value="0" required>
                </div>
                <div class="dcell">
                    <img src="rose.png"/><label for="rose">Rose:</label>
                    <input name="rose" value="0" required>
                </div>
            </div>
            <div id="row2" class="drow">
                <div class="dcell">
                    <img src="peony.png"/><label for="peony">Peony:</label>
                    <input name="peony" value="0" required>
                </div>
                <div class="dcell">
                    <img src="primula.png"/><label for="primula">Primula:</label>
                    <input name="primula" value="0" required>
                </div>
                <div class="dcell">
                    <img src="snowdrop.png"/><label for="snowdrop">Snowdrop:</label>
                    <input name="snowdrop" value="0" required>
                </div>
            </div>
        </div>
    </div>
    <div id="buttonDiv">
        <button type="submit">Place Order</button>
    </div>
</form>
</body>
</html>

```

>**css**

```css
h1 {
    min-width: 70px;
    border: thick double black;
    margin-left: auto;
    margin-right: auto;
    text-align: center;
    font-size: x-large;
    padding: .5em;
    color: darkgreen;
    background-image: url("border.png");
    background-size: contain;
    margin-top: 0;
}

.dtable {
    display: table;
}

.drow {
    display: table-row;
}

.dcell {
    display: table-cell;
    padding: 10px;
}

.dcell > * {
    vertical-align: middle
}

input {
    width: 2em;
    text-align: right;
    border: thin solid black;
    padding: 2px;
}

label {
    width: 5em;
    padding-left: .5em;
    display: inline-block;
}

#buttonDiv {
    text-align: center;
}

#oblock {
    display: block;
    margin-left: auto;
    margin-right: auto;
    min-width: 700px;
}

.hover{
    background: blue;
    color: white;
    height:300px;
    width:300px;
}
```

## 选择器
>* `:animated` :选择正在处理动画的元素
>* `:first`    :选择第一个元素
>* `:last`     :选择最后一个元素
>* `:eq(n)`    :选择第n个元素(从0开始)
>* `:even `    :选择序号为偶数的元素
>* `:odd`      :选择序号为奇数的元素
>* `:gt(n)`    :选择序号**大于**n的元素
>* `:lt(n)`    :选择序号小于n的元素
>* `:text`     :选择所有的文本输入框
>* `:contains(text)`   :选择包含指定文本的元素
>* `file`     :选择所有文件上传输入框
>* `:button`   :选择所有的按钮
>* `:checkbox`  :选择所有的复选框
>* `:hidden`     :选择隐藏的元素


>**实例**
>>`$("img:odd").css("border","thick double red");`选择序号为奇数的`img`元素
>> `$("img:first").css("border","thick double red")`  选择第一个`img`元素

## JQuery对象的方法

>* `context` 选择元素时使用的上下文对象

>> `$("img:odd").context.TagName;`

>* `each(function())` 在每个选中的元素上运行给定的函数

```javascript
	$("img").each(function(index,elem){
	console.log(ele.TagName+"   "+elem.id);//这里的index表示每一个元素的索引，elem表示每一个元素的htmlElement对象，并不是jquery对象
})
```

>* index(jquery) || index(selector)  返回给定jquery对象在住对象中的序号，或者返回给定选择器参数的索引

>>`$("img").index("img[src=*astor]")` 


>* length || size()  返回的时jquery对象个数

>>`$("img:odd").length`

>* toArray()  返回一个有jquery对象中包含的htmlEelments数组

>>`var content=$("img:odd").toArray()`  这里content返回的htmlElements数组




## 把jquery当成数组

```javascript
var content=$("img:odd");
for(var i=0;i<content.length;i++)
{
    console.log(content[i].TagName+"    "+content[i].src);    //这里的content[i]就是htmlElement数组了，$(content[i])就变成了Jquery对象了
}
```

## add
>**`add`函数允许我们添加更多的项，常用的有`add(htmlElement[])`,`add(selector)`,`add(jquery)`**

>**实例：**

```javascript
$("img:odd").add("img:even").css("border",'thick double red');

var jq=$("img[src*=astor]");
$("img:even").add(jq).add("img:even").css("border",'thick double red');

var label=document.getElementsByTagName("label");
$("img:odd").add(label).css("border","thick double red");
```


## slice()
>**用来获取特定的一组子元素**

>**实例：**

```javascript
 $("img").slice(0,3).css("border","thick double red");   //获取0-2的元素
 
  $("img").slice(3).css("border","thick double red");   //获取3-结束
 
 
```


## filter
>**filter可以将不满足指定条件的元素剔除，常用的方法有`filter(jquery)`,`filter(htmlElement)`,`filter(function(index))`,`filter(selector)`**

>**实例**

```javascript
   //这里填入的参数selector
 $("label").filter("[for*=p]").css("background-color",'blue').css("font-size",'20px').css("border","2px solid red");
     
      $("img").filter(function (index) {     //index是每一个元素的索引，如果返回的是true就会选定，false就会剔除这个元素
        if(index==4)
        {
            return true;
        }
        else return false;
    }).css("border",'thick double red');
    
    
    var elem=document.getElementsByTagName("label")[1];    //只选择第二个label
    $("label").filter(elem).css("font-size",'30px')     //这里填入的参数是htmlElement对象
 
 
```

## not
>**`not`方法是`filter`方法的补充，主要是删除匹配条件的元素，而`filter`则是保留满足匹配条件的元素，常用的方法有`not(selector)`,`not(htmlElement)`,`not(jquery)`,`not(function(index))`**

>**实例：**

```javascript
 $("label").not("[for*=p]").css("background-color",'red');    //选择for不带p的label元素

    $("label").not(function (index) {   //哪个元素返回true就删除，false保留
        if(index==0)
            return true;      //这里就会删除第一个label元素，保留后面的元素
        else
            return false;

    }).css("background-color","yellow");
```

## has
>**选择拥有指定后代的选择器**

>**实例：**
```javascript
    $("div.dcell").has("img[src*=astor]").css("border","thick double red");  //选择子代拥有img属性src带有astor的div.dcell元素
    
    var s=$("[for*=astor]");
    $("div.dcell").has(s).css("border","thick double red");   //参数为jquery对象

```

## map
>**以一个函数为参数，map方法能够帮助我们灵活的处理一个`jquery`对象，从而得到满足需要的一个`jquery`对象。针对源`jquery`对象中的每一个元素都调用一次这个函数，而函数返回的`HtmlElement`对象将会变成一个`jquery`对象，参数是`function(index,elem)`,其中`index是序号，elem是jquery对象中的每一个HTMLElelments对象，这里必须要有返回值，不然没有意义**

>**实例：**

```javascript
$("div.dcell").map(function(index,elem){
    return elem.getElementsByTagName("img")[0];   //这里的elem是$(div.dcell)中的每一个HtmlElement对象，返回的是img元素
}).css("border",'thick double red');      //可以很清楚的看到这里返回的htmlElement对象变成了Jquery对象，因为调用了函数css


$("img").map(function(index,elem){
    if(index==1)
    return elem;   //返回的是第二个img的HtmlElement对象，但是经过map的包装就会变成jquery对象

}).css("border",'thick double red');      //可以很清楚的看到这里返回的htmlElement对象变成了Jquery对象，因为调用了函数css

```

## is
>**`is`方法确定`jquery`对象中的某个或者某些元素是否满足测试条件，其中的形式有`is(selector)`,`is(HtmlElement)`,`is(jquery)`,`is(function(index))`如果结果集中至少有一个元素匹配指定的条件，那么就返回`true`,否则`false`**

>**实例：**
```javascript
console.log($("img").is("[src*=astor]"));//这里是判断img中的src属性有没有astor字段的，如果存在返回true

$("img").is(function(index){

})


var c=$("img").is(function (index) {    //函数中如果至少有一个返回true，那么就会返回true，index是索引
        return this.getAttribute('src')=='rose.png';   //判断属性
    });
    console.log(c);

```

## end

>**当我们调用方法链来修改结果集的时候，`jquery`维护者一个历史结果集的查找，我们可以利用`end`回退到历史的结果集中,`end`用来扔掉当前的结果集，返回到上一层结果集**

>**实例：**

```javascript
$("img").filter("[src*=astor]").end().css("border",'thick double red');   //这里回退到$("img")这个结果集中


$("div.dcell").find("img").filter(":odd").filter(":eq(0)").end().end().css("border",'thick double red'); //这里调用了两个end将结果集回退到$("div.dcell").find("img")中
```

## addBack
>**得到当前结果集和上一个结果集的合集**

>>**实例**

```javascript
$("div.dcell").children("img").addBack().css("border",'thick double red');//这里得到的是$("div.dcell")和$("div.dcell").children("img")的合集，并且应用css

$("img").slice(0,3).filter("[src*=astor]").addBack().css("border",'thick double red');//$("img").slice(0,3)和$("img").slice(0,3).filter("[src*=astor]")的合集

//这里的选择器参数过滤的是原结果集，相当于$("img").slice(0,3).filter("[src*=daff]")，
$("img").slice(0,3).filter("[src*=astor]").addBack("[src*=daff]").css("border",'thick double red');

```


## children
>**`children`是用来访问子元素的，形式有childern(),children(selector),其中第一个是用来得到结果集中所有的子元素，第二个是用来过滤得到的子元素，保留满足`selector`的子元素**

>**实例：**

```javascript
$("div.dcell").children().css("border",'thick double red');//得到所有div.dcell的子元素，包括其中的img和input元素

$("div.dcell").children("img").css("border",'thick double red');//得到所有子元素中的img元素
```

## find
>**`find`是用来得到结果集中的所有的后代元素，这里是后代元素，并不是只有子元素，还包括孙子。。。，形式有`find()`,`find(selector)`,`find(htmlElement)`,`find(jquery)`,`find(htmlElment[])`，这里会自动去掉含有重复的元素，因此可以用来过滤元素**


>**实例**

```javascript
$("div.dcell").find("img");   //找到div.dcell的后代元素img

var content=document.getElementsByTagName("input");
$("div.dcell").find(content).filter(":first").css("font-size",'1.5em');//找到div.dcell后代元素中的input元素

```


## parent
>**选取结果集中的父元素，这里表示一层关系就是父元素，并不是祖先元素，形式有`parent()`,`parent(selector)`**

>**实例：**
```javascript
$("img").parent();   //选取img的父元素

$("img").parent(":first");   //选取img父元素中的第一个元素
```

## parents
>**选取祖先元素，包括父元素，形式有`parents()`,`parents(selector)`**

>**实例：**
```javascript
$("img").parents().each(function(index,elem){    //选取所有的祖先元素
    console.log(elem.TagName+"   "+elem.id);
})


$("img").parents("div.dcell").css("border",'thick double red');   //选择所有的div.dcell元素

```


## parentsUntil
>**选择祖先元素，知道找到这个当前祖先元素匹配参数选择器为止,`parentsUntil(selector)`,`parentsUntil(selector,selector)`，其中带有两个参数选择器中的第二个参数是用来筛选所得到的结果集，第一个是用来定位直到这个元素为止**

>**实例：**

```javascript
    $("img").parentsUntil("div.drow");//找img的祖先元素，直到div.drow为止，不包括div.drow
    
     $("img").parentsUntil("div.drow",":first").css("border",'thick double red');  //这里选择了结果集中的第一个元素应用了样式

```


## closest
>**得到结果集中元素的祖先元素中匹配`selector`选择器最接近的那个祖先元素，形式为`closest(selector)`,`closest(selctor,context)`,`closest(htmlElemtent)`,`closest(jquery)`**

>**实例：**

```javascript

$("img").closest("div.drow").each(function (index,elem) {   //选择满足div.drow的祖先元素，这里的最接近就是辈分最接近，这里的两个class=drow的div都是最接近的，因为这俩个是同级的关系
        console.log(elem.tagName+"    "+elem.id);
    });
    
    
    var jq=$("#row1,#row2,form");   //传入jquery对象
    $("img").filter("[src*=astor]").closest(jq).each(function (index,elem) {   //这里选取的是最接近第一张图的祖先元素，当然是<div id="row1">
        console.log(elem.tagName+"   "+elem.id);
    })
    
```

## offestParent
>**得到距离最近的祖先定位元素，使用`fixed`,`absolute`,`relative`定位的元素，形式为`offestParent()`**


## siblings
>**得到所有的兄弟元素，可选的`selector`用来过滤结果，形式为`siblings()`,`siblings(selector)`**

>**实例：**

```javascript
    $("img").siblings().css("font-size",'1.4em');// 得到img的所有兄弟元素，这里是input
    
    $("img").siblings(":last");     //得到img所有兄弟元素中的最后一个元素
```

## prev
>**得到上一个兄弟元素，形式为`prev()`,`prev(selector)`，其中的`selector`是用来过滤结果的**

>**实例：**

```javascript
    $("input").prev().css("border",'thick double red');   //这里得到input的上一个元素Label元素

```

## prevAll
>**得到当前元素的所有的上面的兄弟元素，形式为`prevALl()`,`prevAll(selector)`**

>**实例：**

```javascript
$("input").prevAll().css("border",'thick double red');   //得到input上面的所有的兄弟元素

$("input").prev("img").css("border",'thick double red');   //得到input上面的所有的img元素
```

### prevUntil
>**这个和parentsUntil一样，直到匹配`selector`就结束了，不包括**

>**实例：**

```javascript
$("input").prevUntil("i").css("border",'thick double red');
```

## next
>**选择当前元素下面的一个兄弟元素，和`prev`一样**

## nextAll
>**选择当前元素下面的所有兄弟元素，和`prevAll`一样**

## nextUntil
>**和`prevUntil`一样**







































































>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*