---
title: java图形与文本处理一
date: 2017-03-25 12:38:51
categories: java学习
tags: java图形与文本处理 
---

# java绘制图形和文本<一>

## 开篇介绍([官方文档](http://tool.oschina.net/apidocs/apidoc?api=jdk-zh))
> java.awt 
类 Graphics
java.lang.Object
继承者 java.awt.Graphics
直接已知子类：
DebugGraphics, Graphics2D
public abstract class Graphics extends Object
     
>Graphics 类是所有图形上下文的抽象基类，允许应用程序在组件（已经在各种设备上实现）以及闭屏图像上进行绘制。
Graphics 对象封装了 Java 支持的基本呈现操作所需的状态信息。此状态信息包括以下属性：
要在其上绘制的 Component 对象。
呈现和剪贴坐标的转换原点。
当前剪贴区。
当前颜色。
当前字体。
当前逻辑像素操作函数（XOR 或 Paint）。
当前 XOR 交替颜色（参见 setXORMode(java.awt.Color)）。
坐标是无限细分的，并且位于输出设备的像素之间。绘制图形轮廓的操作是通过使用像素大小的画笔遍历像素间无限细分路径的操作，画笔从路径上的锚点向下和向右绘制。填充图形的操作是填充图形内部区域无限细分路径操作。呈现水平文本的操作是呈现字符字形完全位于基线坐标之上的上升部分。
图形画笔从要遍历的路径向下和向右绘制。其含义如下：
如果绘制一个覆盖给定矩形的图形，那么该图形与填充被相同矩形所限定的图形相比，在右侧和底边多占用一行像素。
如果沿着与一行文本基线相同的 y 坐标绘制一条水平线，那么除了文字的所有下降部分外，该线完全画在文本的下面。
所有作为此 Graphics 对象方法的参数而出现的坐标，都是相对于调用该方法前的此 Graphics 对象转换原点的。
所有呈现操作仅修改当前剪贴区所限定区域内的像素，此剪贴区是由用户空间中的 Shape 指定的，并通过使用 Graphics 对象的程序来控制。此用户剪贴区 被转换到设备空间中，并与设备剪贴区 组合，后者是通过窗口可见性和设备范围定义的。用户剪贴区和设备剪贴区的组合定义复合剪贴区，复合剪贴区确定最终的剪贴区域。用户剪贴区不能由呈现系统修改，以反映得到的复合剪贴区。用户剪贴区只能通过 setClip 或 clipRect 方法更改。所有的绘制或写入都以当前的颜色、当前绘图模式和当前字体完成。


>> ## 绘制直线
> >主要用到的内容是Graphics类中的**drawLine**函数
> > 定义：
> > > `public abstract void drawLine(int x1,int y1,int x2,int y2)`
> > > *x1,y1是起始点的坐标，x2,y2是尾点的坐标*

> > #### 拓展
> > > `SetColor(Color color)`
> > > > setColor是Graphics类中的一个函数，主要是设置颜色作用，其中参数是Color类中的一个对象，用于定义自己的颜色，里面的变量的是RGB,定义的方法：`Color color=newe Color(R,G,B)`
> > #### 代码

```java   
    import java.awt.Graphics;
    import javax.swing.JFrame;
    import javax.swing.JPanel;

    public class DrawLineFrame extends JFrame {
    DrawLinePanel linePanel = new DrawLinePanel(); 

    public static void main(String args[]) { // 主函数
        DrawLineFrame frame = new DrawLineFrame(); // 创建一个继承JFrame的一个类对象
        frame.setVisible(true); // 设置窗体可见，true为可见，false为不可见
    }

    public DrawLineFrame() {
        super();
        setTitle("绘制直线"); // 设置窗体的标题
        setBounds(100, 100, 273, 167); // 设置窗体的显示位置和大小
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 设置窗体的关闭方式，具体见官方文档
        add(linePanel); // 将继承Jpanel类的容器对象添加在窗体中
    }

    class DrawLinePanel extends JPanel {   // 继承在JPanel类的一个内部类，用于定义直线
        public void paint(Graphics g) {    // 重写JCommponent类中的paint方法，用来绘制直线
            Color color=new Color(Color.Red);//这里用的是Color提供的颜色，当然读者也可以自己定义RGB颜色
            g.setColor(Color);//将颜色作用于绘图上下文
            g.drawLine(70, 50, 180, 50);   // 调用方法
            g.drawLine(70, 80, 180, 80);   // 第二条直线
            g.drawLine(110, 10, 140, 120); // 第三条
        }
    }
}
```

> >## 绘制矩形
> >主要用到的函数是：`public  abstract void drawRect(int x,int y,int width,int height)`这里的x,y是矩形左上角的坐标，width，height是矩形的长和宽
> > > ### 拓展
> > > `fillRect(int x,int y,int width,int height)`:绘制实心矩形
> > > ### 代码

```java
    import java.awt.Graphics;
    import javax.swing.JFrame;
    import javax.swing.JPanel;
    public class DrawRectangleFrame extends JFrame     {
    DrawRectanglePanel rectPanel = new     DrawRectanglePanel(); // 创建面板类的实例
    
    public static void main(String args[]) { // 主方法
        DrawRectangleFrame frame = new DrawRectangleFrame(); // 创建窗体类的实例
        frame.setVisible(true); // 显示窗体
    }
    
    public DrawRectangleFrame() {
        super(); // 调用超类的构造方法
        setTitle("绘制矩形"); // 窗体标题
        setBounds(100, 100, 269, 184); // 窗体的显示位置和大小
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 窗体关闭方式
        add(rectPanel); // 将面板类的实例添加到窗体容器中
    }
    
    class DrawRectanglePanel extends JPanel { // 创建内部面板类
        public void paint(Graphics g) {       // 重写paint()方法
            g.drawRect(30, 40, 80, 60);       // 绘制空心矩形
            g.fillRect(140, 40, 80, 60);      // 绘制实心矩形
        }
    }
    }
```
    
>>### 绘制椭圆
>>>函数：`public abstract void drawOval(int x,int y,int width,int height)`,其中x,y是外切矩形的左上角的坐标，width，height是长宽
>>>>## 拓展
>>>>>其中将令width=height，即是一个圆了，`fillOval(int x,int y,int width,int height)`用来绘制实心的椭圆
>>>>>### 代码

```java

    package com.zzk;
    import java.awt.Graphics;
    import javax.swing.JFrame;
    import javax.swing.JPanel;

    public class DrawEllipseFrame extends JFrame {
    DrawEllipsePanel ellipsePanel = new DrawEllipsePanel(); // 创建面板类的实例
    
    public static void main(String args[]) { // 主方法
        DrawEllipseFrame frame = new DrawEllipseFrame(); // 创建窗体类的实例
        frame.setVisible(true); // 显示窗体
    }
    
    public DrawEllipseFrame() {
        super(); // 调用超类的构造方法
        setTitle("绘制椭圆"); // 窗体标题
        setBounds(100, 100, 269, 222); // 窗体的显示位置和大小
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 窗体关闭方式
        add(ellipsePanel); // 将面板类的实例添加到窗体容器中
    }
    
    class DrawEllipsePanel extends JPanel { // 创建内部面板类
        public void paint(Graphics g) {     // 重写paint()方法
            g.drawOval(30, 20, 80, 50);     // 绘制空心椭圆
            g.drawOval(150, 10, 50, 80);    // 绘制空心椭圆
            g.fillOval(40, 90, 50, 80);     // 绘制实心椭圆
            g.fillOval(140, 110, 80, 50);   // 绘制实心椭圆
        }
    }
    }
    
```    

>>## 绘制圆弧
>>>主要用到的函数`public astract void drawArc(int x,int y,int width,int height,int startAngle,int arcAngle)`，其中x,y是要绘制圆弧的左上角的坐标，width，height是要绘制的长宽，startAngle是开始角度，arcAngle是相对于开始角度而言的，弧跨越的角度，
>>>>### 拓展:
>>>>>fillArc(int x,int y,int width,int height,int startAngle,int arcAngle)用来绘制实心圆弧
>>>>>当然你也可以用这个来绘制扇形，用drawLine方法将圆弧的两端连起来就可以了，不过这个对坐标的精确度就要求很高了，暂时不想费那个脑筋来搞了
>>>>### 代码

```java

    package com.zzk;
    import java.awt.Graphics;
    import javax.swing.JFrame;
    import javax.swing.JPanel;
    public class DrawArcFrame extends JFrame {
    DrawArcPanel arcPanel = new DrawArcPanel(); // 创建面板类的实例
    public static void main(String args[]) { // 主方法
        DrawArcFrame frame = new DrawArcFrame(); // 创建窗体类的实例
        frame.setVisible(true); // 显示窗体
    }
    public DrawArcFrame() {
        super(); // 调用超类的构造方法
        setTitle("绘制圆弧"); // 窗体标题
        setBounds(100, 100, 269, 184); // 窗体的显示位置和大小
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 窗体关闭方式
        add(arcPanel); // 将面板类的实例添加到窗体容器中
    }
    class DrawArcPanel extends JPanel { // 创建内部面板类
        public void paint(Graphics g) { // 重写paint()方法
            g.drawArc(20, 20, 80, 80, 0, 120);    // 绘制圆弧
            g.drawArc(20, 40, 80, 80, 0, -120);   // 绘制圆弧
            g.drawArc(150, 20, 80, 80, 180, -120);// 绘制圆弧
            g.drawArc(150, 40, 80, 80, 180, 120); // 绘制圆弧
        }
    }
    }
```
   
>>## 绘制多边形
>>>主要用到的函数是：`public abstract void drawPolygon(int[] xpoints,int[] ypoints,int npoints)`，其中xpoints：要绘制多边形的x坐标组，ypoints是要绘制多边形的y坐标组，npoints是多边形的n条边
>>>### 拓展
>>>>`fillPolygon(...)`是绘制实心多边形的函数
>>>### 代码

```java
    package com.zzk;
    import java.awt.Graphics;
    import javax.swing.JFrame;
    import javax.swing.JPanel;
    public class DrawSectorFrame extends JFrame {
    DrawSectorPanel sectorPanel = new DrawSectorPanel(); // 创建面板类的实例
    public static void main(String args[]) { // 主方法
        DrawSectorFrame frame = new DrawSectorFrame(); // 创建窗体类的实例
        frame.setVisible(true); // 显示窗体
    }
    public DrawSectorFrame() {
        super(); // 调用超类的构造方法
        setTitle("绘制填充扇形"); // 窗体标题
        setBounds(100, 100, 278, 184); // 窗体的显示位置和大小
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 窗体关闭方式
        add(sectorPanel); // 将面板类的实例添加到窗体容器中
    }
    class DrawSectorPanel extends JPanel { // 创建内部面板类
        public void paint(Graphics g) { // 重写paint()方法
            g.fillArc(40, 20, 80, 80, 0, 150);    // 绘制填充扇形
            g.fillArc(140, 20, 80, 80, 180, -150);// 绘制填充扇形
            g.fillArc(40, 40, 80, 80, 0, -110);   // 绘制填充扇形
            g.fillArc(140, 40, 80, 80, 180, 110); // 绘制填充扇形
        }
    }
    }
```


>>## 绘制文本
>>>主要用到的函数是：`public abstract void drawString(String value,int x,int y)`,其中value是要绘制的文本，x,y是第一个字的坐标
>>>### 拓展
>>>>SetFont(Font font):这个函数是用来设置文本的字体大小，颜色的，其中参数font是Font类中的
>>>### 代码

```java
    package com.zzk;

    import java.awt.Font;
    import java.awt.Graphics;
    import javax.swing.JFrame;
    import javax.swing.JPanel;
    public class TextFontFrame extends JFrame {
    ChangeTextFontPanel changeTextFontPanel = new ChangeTextFontPanel(); // 创建面板类的实例
    
    public static void main(String args[]) { // 主方法
        TextFontFrame frame = new TextFontFrame(); // 创建窗体类的实例
        frame.setVisible(true); // 显示窗体
    }
    public TextFontFrame() {
        super(); // 调用超类的构造方法
        setTitle("设置文本的字体"); // 窗体标题
        setBounds(100, 100, 333, 199); // 窗体的显示位置和大小
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 窗体关闭方式
        add(changeTextFontPanel); // 将面板类的实例添加到窗体容器中
    }
    class ChangeTextFontPanel extends JPanel { // 创建内部面板类
        public void paint(Graphics g) { // 重写paint()方法
            String value = "明日编程词典社区";
            int x = 40; // 文本位置的横坐标
            int y = 50; // 文本位置的纵坐标
            Font font = new Font("华文行楷", Font.BOLD + Font.ITALIC, 26); // 创建字体对象
            g.setFont(font); // 设置字体
            g.drawString(value, x, y); // 绘制文本
            value = "http://community.mrbccd.com";
            x = 10; // 文本位置的横坐标
            y = 100; // 文本位置的纵坐标
            font = new Font("宋体", Font.BOLD, 20); // 创建字体对象
            g.setFont(font); // 设置字体
            g.drawString(value, x, y); // 绘制文本
        }
    }
    }

```
    
>>>>#### 补充
>>>>>字体样式包括Font.BLOD(粗体)，Font.ITALIC(斜体)，Font.PLAIN(普通字体)，其中如果要设置两种样式，可以用"+"连接，如：`Font.BLOD+Font.ITALIC`，这样就会同时设置了斜体和粗体样式

**以上是本人的学习成果，通过不断的学习和探索，发现网上没有什么系统的学习java图形处理的文章，就下定决心准备好好写，于是前几天就花了一晚上的时间搭建了博客，以前都是在CSDN上写的，发现在那上面写，没有逼格，为了提高逼格，自己撸了一个博客，让我来自由发挥，另外喜欢编程的朋友可以加我的联系方式，我们可以一起探讨，在下面留言也是可以的哦,联系方式可以在我的*关于我*可以找到**


*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*


























































    


