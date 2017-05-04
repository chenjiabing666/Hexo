---
title: swing布局管理器
date: 2017-04-05 23:06:08
categories: java学习
tags: swing
---

# Swing系列之布局管理器
## 流布局(`FlowLayout`)默认的`JApplet`,`JPanel`,`JScrollPane`
>流布局是相对比较简单的一种布局管理器，也是最常用的布局管理器。在流布局中放置控件时，将按照控件的添加顺序，依次将控件从左到右进行摆放，并且在一行的最后会进行自动换行放置 。在一行中，控件是默认**居中**放置的。

>布局管理器也是通过构造器来创建的。流布局是通过FlowLayout 类来创建，FlowLayout类具有三种构造器。首先是无参构造器， 使用无参构造器能够创建一个默认的以居中对齐方式，控件间水 平和垂直间距为5个像素的流布局。

>FlowLayout类还具有一个需要整型参数的构造器，使用该构造器能够创建一个指定对齐方式的流布局管理器，它的控件间水平和垂直间距仍然是默认的5个像素。流布局管理器的对齐方式如下所示。
>* `LEFT`	左对齐方式
>* `CENTER`	居中对齐方式
>* `RIGHT`	右对齐方式
>* `LEADING`	控件与容器开始边对齐
>* `TRAILING`	
>
>**构造函数：**
>1. `FlowLayout()`,生成一个默认的FlowLayout布局。默认情况下，组件居中，间隙为5个像素。
>2. `FlowLayout(int aligment)`,设定每珩的组件的对齐方式。`alignment`取值可以为`FlowLayout.LEFT`,`FlowLayout.CENTER`,`FlowLayout.RIGHT`。
>1. `FlowLayout(int aligment,int horz, int vert)`,设定对齐方式，并设定组件的水平间距horz和垂直间距vert，用超类Container的方法`setLayout()`为容器设定布局。例如，代码`setLayout(new FlowLayout())`为容器设定 FlowLayout布局。将组件加入容器的方法是add(组件名)。
>
>**常用的函数：**
>**`getAlignment`方法和`setAlignment`方法分别获取和设置流布局管理器的对齐方式。 `getHgap`方法和`setHgap`方法分别获取和设置流布局管理器中控件和控件之间的水平间距。getVgap方法和`setVgap`方法分别获取和 设置流布局管理器中控件和控件之间的垂直间距。**
```java
import javax.swing.*;
import java.awt.*;

/**
 * Created by Chenjiabing on 2017/4/5.
 */
public class BuJu {
    public static void main(String args[])
    {
        JFrame frame=new JFrame();
        FlowLayout flowLayout=new FlowLayout(FlowLayout.LEFT);
        JPanel panel=new JPanel(flowLayout);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setBounds(100,100,500,400);
        flowLayout.setHgap(20);  //设置水平间距
        flowLayout.setVgap(20); //控件之间的垂直间距

        for(int i=0;i<6;i++)
        {
            JButton button=new JButton("按钮");
            panel.add(button);
        }
        frame.getContentPane().add(panel);



        frame.setVisible(true);

    }

}

```

## 网格布局(`GridLayout`)
>* 网络布局也是一种比较常见的布局管理器。使用网格布局管理器后，会将所有的控件尽量按照给出的行数和列数来排列，同时网格布局管理器也会对控件进行尺寸的调整，使所有的控件具有相同的尺寸。在网格布局中，也会尽量使使用的空间成矩形的形式来显示。当窗体发生大小变化时，所有的空间也将自动改变大小来填充窗体。

>* 网格布局是通过`GridLayout`类来创建的。GridLayout类具有三个构造器，使用无参构造器将创建具有默认行和默认列的网格布局。在创建网格布局管理器时最常用的就是具有两个整型参数的构造器，第一个参数表示网格布局管理器的行数，第二个参数表示网格布局管理器的列数。还有一个具有四个参数的构造器，除了可以定义行数和列数外，还可以定义控件间水平间距和垂直间距。

>* `GridLayout`类中还定义了一些方法来对创建的网格布局进行操作 。`getRows`方法和`setRows`方法分别是获取和设置网格布局的行数。`getColumns`方法和`setColumns`方法分别是获取和设置网格布局 的列数。`getHgap`方法和`setHgap`方法分别是获取和设置网格布局 中控件间水平间距。`getVgap`方法和`setVgap`方法分别是获取和设 置网络布局中的控件间垂直间距。

>**构造函数：**
>1. `GridLayout()`,生成一个单列的GridLayout布局。默认情况下，无间隙。
>1.` GridLayout(int row,int col)`,设定一个有行`row`和列`col的GridLayout布局。
>1. GridLayout(int row,int col,int horz,int vert),设定布局的行数和列数、组件的水平间距和垂直间距

>**代码大概和上面的设置一样，这里注意的是，网格布局是以行为基准的，如果定义的控件多了或者少了，不会改变行的数量，会根据情况改变列的数量**

## 边框布局(BorderLayout)默认的是`JWindow`、`JFrame`,`JDialog`

>* 上面学习的流布局和网格布局具有很多相似的地方，但是边框布局就和他们存在很大的不同。在使用边框布局时，通常都会由程序员来为控件指定在容器中的位置。边框布局将容器分为五个部分，包括东南西北中五部分。在每一个部分中只能放置一个控件 ，所以如果控件超过五个将不能完全显示。在使用边框布局时需 要注意的是，当容器的大小发生变化时，四周的控件是不会发生变化的，只有中间的控件将发生变化。

>* 边框布局是通过BorderLayout类创建的。BorderLayout类具有两个构造器，一个是无参构造器，另一个是指定控件间间距的构造器，通常使用无参构造器来创建边框布局管理器。

>* 在前面将控件添加到容器中都是通过add方法，将控件作为add方法的参 数来进行添加的。但是在向边框布局容器中添加控件时，这样是不完全 的。在向边框布局容器中添加控件是使用具有两个参数的add方法。其中 第一个参数表示要添加的控件，第二个参数表示要添加到边框布局中的 哪一个位置。边框布局的位置表示是通过常量来表示的，具体常量如下所示

>>* `NORTH`	容器顶部
>>* `SOUTH`	容器底部
>>* `WEST`	容器左边
>>* `EAST`	容器右边
>>* `CENTER`	容器的中央

>**构造函数：**
>1. `BorderLayout()`,生成一个默认的`BorderLayout`布局。默认情况下，没有间隙。
>1.  `BorderLayout(int horz,int vert)`,设定组件之间的水平间距和垂直间距。

>**注意这里还有一些常用的方法，就是设置水平和垂直的间距，上面已经赘述过了，这里就不再详说了**

## 空布局(`null`)
>空布局就是没有使用布局管理器，在空布局的情况下将根据控件的自身信息来为控件指定位置。这就使得控件的布局更加灵活，与此同时给开发人员带来了更大的工作量。

空布局是不需要使用类来创建的，只需要在程序指定布局管理器 为null。将控件添加到空布局容器中时，仍然是使用`add`方法。因 为这里使用的是空布局管理器，所以在添加控件之前，要对控件 进行设置操作。设置操作是通过setBounds方法来完成的， setBounds方法的基本语法格式如下所示。
>`public void setBounds(int x,int y,int width,int height);`

> 其中x和y表示的是控件最左上侧的坐标，从而也固定了该控件的 位置。`width`和`height`表示的是空间的宽度和高度，从而也指定了
控件的大小。

>**示例代码：**
```java
>frame.setLayout(null);//布局管理器设置为null
    JLabel label = new JLabel("First Name:");
    label.setBounds(20, 20, 100, 20);//四个参数分别是x,y坐标和label的宽和高
    JTextField textField = new JTextField();
    textField.setBounds(124, 25, 100, 20);
    frame.add(label);
    frame.add(textField);
```

## BoxLayout
>`BoxLayout`是一种能够实现所有的控件水平放置和垂直放置，因为用到的不多，这里就简单的说一下
>**构造函数**：`public BoxLayout(Container target,int axis)`:其中`axis`表示放置的样式，主要有两种常用到的:

>* `X_AXIS`:指定组件应该从左到右放置。
>* `Y_AXIS`：指定组件从上到下放置

>**代码**

```java
import oracle.jrockit.jfr.JFR;

import javax.swing.*;
import java.awt.*;

public class BuJu {
    public static void main(String args[]) {
        JFrame frame = new JFrame();
        // frame.setLayout(new BorderLayout(frame.getComponentCount(),BoxLayout.Y_AXIS));
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        JPanel panel = new JPanel();
        BoxLayout boxLayout = new BoxLayout(panel, BoxLayout.X_AXIS);
        panel.setLayout(boxLayout);
        for (int i = 0; i < 10; i++) {
            JButton button = new JButton("cma");
            panel.add(button);
        }
        frame.getContentPane().add(panel, BorderLayout.CENTER);
        // System.out.println(boxLayout.getTarget());
        frame.pack();
        //frame.setSize(300,200);
        frame.setVisible(true);


    }


}
```
















>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*