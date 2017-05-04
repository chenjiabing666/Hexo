---
title: BootStrap干货篇之表单
date: 2017-04-26 23:11:37
categories: BootStrap学习
tags: BootStrap
---
# BootStrap干货篇之表单

## 基本介绍
>**单独的表单控件会被自动赋予一些全局样式。所有设置了 `.form-control` 类的 `<input>`、`<textarea>` 和 `<select>` 元素都将被默认设置宽度属性为 `width: 100%`;。 将 `label `元素和前面提到的控件包裹在 `.form-group` 中可以获得最好的排列。**

>**基本实例：**

```htmlmixed
    <div class='container'>
    <form>
  <div class="form-group">
    <label for="exampleInputEmail1">Email address</label>
    <input type="email" class="form-control" id="exampleInputEmail1" placeholder="Email">
  </div>
  <div class="form-group">
    <label for="exampleInputPassword1">Password</label>
    <input type="password" class="form-control" id="exampleInputPassword1" placeholder="Password">
  </div>
  <div class="form-group">
    <label for="exampleInputFile">File input</label>
    <input type="file" id="exampleInputFile">
    <p class="help-block">Example block-level help text here.</p>
  </div>
  <div class="checkbox">
    <label>
      <input type="checkbox"> Check me out
    </label>
  </div>
  <button type="submit" class="btn btn-default">Submit</button>
</form>
    </div>
```
>**说明：这里的`form-control`是对所有的输入控件而言的,源码中将width设置为`100%`，表示会将这个输入控件占满一整行，`form-group`是用来对`label`和`input`更好的排版的，其中还有`form-group-sm`,`form-group-lg`，源码中分别利用这个对带有`form-control`的控件设置了不同的高度，具体看源码，不过正常情况下还是使用`form-group`**


## 内联表单
>**为 `<form>` 元素添加 `.form-inline` 类可使其内容左对齐并且表现为` inline-block `级别的控件。只适用于视口（`viewport`）至少在 `768px` 宽度时（视口宽度再小的话就会使表单折叠）从源码中可以看到对`form-inline`下的`form-group`,`form-control`,`form-control-static`,`input-group`,`radio`,`checkbox`都是用了`display:inline-block`**

>**注意：**

>* 在 `Bootstrap` 中，输入框和单选/多选框控件默认被设置为 `width`: `100%`; 宽度。在内联表单，我们将这些元素的宽度设置为` width: auto`;，因此，多个控件可以排列在同一行。根据你的布局需求，可能需要一些额外的定制化组件。
>* **一定要有`label`标签，如果不想要`label`标签可以设置`.sr-only`将其隐藏**如果你没有为每个输入控件设置 `label` 标签，屏幕阅读器将无法正确识别。对于这些内联表单，你可以通过为 label 设置 .sr-only 类将其隐藏。还有一些辅助技术提供label标签的替代方案，比如 `aria-label`、`aria-labelledby `或 `title` 属性。如果这些都不存在，屏幕阅读器可能会采取使用 `placeholder` 属性，如果存在的话，使用占位符来替代其他的标记，但要注意，这种方法是不妥当的。


>**实例:**

```htmlmixed
<form class="form-inline">    <!--指定了form-inline类-->
  <div class="form-group">
  <!--label中的for标签是用于绑定组件的，如果指定了for标签，input中的id也和for标签的内容相同，那么就会当鼠标点击<label>内容时会自动聚焦在input上-->
    <label class="sr-only" for="exampleInputEmail3">Email address</label>
    <input type="email" class="form-control" id="exampleInputEmail3" placeholder="Email">
  </div>
  <div class="form-group">
    <label class="sr-only" for="exampleInputPassword3">Password</label>
    <input type="password" class="form-control" id="exampleInputPassword3" placeholder="Password">
  </div>
  <div class="checkbox">
    <label>
      <input type="checkbox"> Remember me
    </label>
  </div>
  <button type="submit" class="btn btn-default">Sign in</button>
</form>
```

## 水平表单

>**水平表单通过指定为form指定`form-horizontal`类来设定，其中可以使用`BootStrap`的栅栏系统设置水平间距，其中的`form-group`的`div`就表示一行了，相当于`<div class='row'></div>`,因此只需要在`label`和`input`中指定列就行了，但是`input`标签不能直接使用，要在外面加上`div`**

>**实例：**

```htmlmixed
<form class="form-horizontal">
  <div class="form-group">   
    <label for="inputEmail3" class="col-sm-2 control-label">Email</label>
    <div class="col-sm-10">
      <input type="email" class="form-control" id="inputEmail3" placeholder="Email">
    </div>
  </div>
  <div class="form-group">   <!--相当与<div class='row'></div>-->
    <label for="inputPassword3" class="col-sm-2 control-label">Password</label>
    <div class="col-sm-10">
      <input type="password" class="form-control" id="inputPassword3" placeholder="Password">
    </div>
  </div>
  <div class="form-group">
    <div class="col-sm-offset-2 col-sm-10">
      <div class="checkbox">
        <label>
          <input type="checkbox"> Remember me
        </label>
      </div>
    </div>
  </div>
  <div class="form-group">
    <div class="col-sm-offset-2 col-sm-10">
      <button type="submit" class="btn btn-default">Sign in</button>
    </div>
  </div>
</form>
```
>**说明上面的`label`标签中的`control-label`主要的作用是设置文字的对齐方式为左对齐，如果不加这个将会在右边出现很大的空白**


## 多选和单选框
>**多选框`（checkbox）`用于选择列表中的一个或多个选项，而单选框（`radio`）用于从多个选项中只选择一个。其中提供的类有`checkbox`,`checkbox-inline`,`radio`,`radio-inline`**

#### 内联单选和多选框
>**通过将 .checkbox-inline 或 .radio-inline 类应用到一系列的多选框（`checkbox`）或单选框（`radio`）控件上，可以使这些控件排列在一行。**

>**实例：**

```htmlmixed
<label class="checkbox-inline">
  <input type="checkbox" id="inlineCheckbox1" value="option1"> 1
</label>
<label class="checkbox-inline">
  <input type="checkbox" id="inlineCheckbox2" value="option2"> 2
</label>
<label class="checkbox-inline">
  <input type="checkbox" id="inlineCheckbox3" value="option3"> 3
</label>

<label class="radio-inline">
  <input type="radio" name="inlineRadioOptions" id="inlineRadio1" value="option1"> 1
</label>
<label class="radio-inline">
  <input type="radio" name="inlineRadioOptions" id="inlineRadio2" value="option2"> 2
</label>
<label class="radio-inline">
  <input type="radio" name="inlineRadioOptions" id="inlineRadio3" value="option3"> 3
</label>



<div class="checkbox-inline">
            <label for="sex"><input type="checkbox">男</label>
    </div>
    <div class="checkbox-inline">
        <label for="sex"><input type="checkbox">男</label>
    </div> 
```

#### 不带label文本的Checkbox 和 radio
>**如果需要 `<label>` 内没有文字，输入框（`input`）正是你所期望的。 目前只适用于**非内联**的 `checkbox `和 `radio`。 请记住，仍然需要为使用辅助技术的用户提供某种形式的 `label`（例如，使用 `aria-label`）。**

>**实例：**

```htmlmixed
<div class="checkbox">
  <label>
    <input type="checkbox" id="blankCheckbox" value="option1" aria-label="...">
  </label>
</div>
<div class="radio">
  <label>
    <input type="radio" name="blankRadio" id="blankRadio1" value="option1" aria-label="...">
  </label>
</div>
```

#### 下拉列表（select）
>**实例：**

```htmlmixed
<select class="form-control">
  <option>1</option>
  <option>2</option>
  <option>3</option>
  <option>4</option>
  <option>5</option>
</select>
```

## 静态控件
>**如果需要在表单中将一行纯文本和 `label` 元素放置于同一行，为`<p>`标签设置为`form-control-static`**

>**实例：**

```htmlmixed
<form class="form-horizontal">
  <div class="form-group">
    <label class="col-sm-2 control-label">Email</label>
    <div class="col-sm-10">
      <p class="form-control-static">email@example.com</p>
    </div>
  </div>
  <div class="form-group">
    <label for="inputPassword" class="col-sm-2 control-label">Password</label>
    <div class="col-sm-10">
      <input type="password" class="form-control" id="inputPassword" placeholder="Password">
    </div>
  </div>
</form>
```

## 参考文章
>* [中文官网](http://v3.bootcss.com/css/#forms-controls-static)
>* [文档手册](http://www.shouce.ren/api/view/a/779)













>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*