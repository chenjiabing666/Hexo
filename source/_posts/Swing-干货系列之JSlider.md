---
title: Swing干货系列之JSlider(滑块)
date: 2017-03-27 21:43:22
categories: java学习
tags: swing 
---

# Swing干货系列之JSlider(滑块)
## 引言
>一个让用户以图形方式在有界区间内通过移动滑块来选择值的组件。

>滑块可以显示主刻度标记以及主刻度之间的次刻度标记。刻度标记之间的值的个数由 `setMajorTickSpacing `和 `setMinorTickSpacing` 来控制。刻度标记的绘制由 setPaintTicks 控制。

>滑块也可以在固定时间间隔（或在任意位置）沿滑块刻度打印文本标签。标签的绘制由 `setLabelTable` 和 `setPaintLabels` 控制。

### 构造函数
>* `JSlider()`:创建一个空值的滑块组件，但是默认的刻度是100，其中如果获得其值的话可以很清楚的看见
>* `JSlider(BoundedRangeModel brm)`:使用指定的 `BoundedRangeModel` 创建一个水平滑块
>* `JSlider(int min,int max)`:创建一个带有最小值和最大值得滑块
>* `JSlider(int min,int max,int value)`:创建一个带有最小值，最大值和当前值的滑块

### 常用的方法
>1. `getValue(int x)`/`setValue(int x)`:得到和设置当前值
>1. `getPaintsLabels()`:return `boolean` 告知是否绘制了签
>1. `SetFont(Font font)`:设置组件的字体，其中Font类的font对象是参数
>1. `setInverted(boolean b)`:反转滑块的刻度
>1. `setMaximum(int maximum)` ：设置最大值
>1. `setMinimum(int min)`:设置最小值
>1. `setMinorTickSpacing(int n)` :设置次刻度，就是主刻度中间不用标记数值的刻度
>1. `setMajorTickSpacing(int n)`:设置主刻度
>1. `setPaintTicks(boolean b)`:确定是否在滑块下面显示刻度线，如果为false表示不显示
>1. `setPaintLabels(boolean b)`:确定是否在刻度线下绘制数值，默认不绘制
**以上只是列了几个常用的函数，详情见[官方文档](http://tool.oschina.net/apidocs/apidoc?api=jdk-zh)**

### 下面撸个代码试试身手
```java
package com;
import javax.swing.*;
import javax.swing.event.ChangeEvent;
import javax.swing.event.ChangeListener;
import java.awt.*;
/**
 * Created by chenjiabing on 2017/3/27.
 */
public class Java_swing extends JFrame {
    public JSlider points = null;

    public Java_swing() {
        super();
        setTitle("记事本");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setSize(300, 400);
//        points=new JSlider();
        points = new JSlider(0, 50, 5);
        points.setMinorTickSpacing(5);//设置次要的间隔，每个一个间隔，这个显示时中间不标记数值
        points.setMajorTickSpacing(10);//显示主要的刻度线，每个两个间隔，这个设置了，如果setPaintLabels为true就会显示数值
        points.setPaintTicks(true);  //确定是否显示刻度线
        points.setPaintLabels(true); //确定是否显示刻度的值
        //points.setInverted(true);//指定为true反转刻 度
        points.setSnapToTicks(true);
        points.addChangeListener(new ChangeListener() {
            @Override
            public void stateChanged(ChangeEvent e) {
                int value = points.getValue();
                System.out.println(value);

            }
        });
        getContentPane().add(points, BorderLayout.CENTER);
    }

    public static void main(String args[]) {
        Java_swing my = new Java_swing();
        my.setVisible(true);
    }
}

```

### Change Listener(一个监听机制)
```java
import java.awt.Dimension;
/*from  w  ww  .  ja  v a 2 s  .c o  m*/
import javax.swing.JFrame;
import javax.swing.JSlider;
import javax.swing.event.ChangeEvent;
import javax.swing.event.ChangeListener;

public class Main {
  public static void main(String[] args) {
    JFrame f = new JFrame();
    final JSlider slider = new JSlider(0, 150, 0);
    f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    slider.setPreferredSize(new Dimension(150, 30));
    
    //添加change Listener,当然这里的和JButton的也是一样，可以在一个类中实现
    slider.addChangeListener(new ChangeListener() {
      public void stateChanged(ChangeEvent event) {
        int value = slider.getValue();
        if (value == 0) {
          System.out.println("0");
        } else if (value > 0 && value <= 30) {
          System.out.println("value > 0 && value <= 30");
        } else if (value > 30 && value < 80) {
          System.out.println("value > 30 && value < 80");
        } else {
          System.out.println("max");
        }
      }
    });
    f.add(slider);
    f.pack();
    f.setLocationRelativeTo(null);
    f.setVisible(true);
  }
}

```


**当然以上只是JSlider的一部分内容，还有的后面会陆续更新**
**本文参考的文章：**
>> * [中文文档](http://tool.oschina.net/apidocs/apidoc?api=jdk-zh)
>> * [英文文档](http://www.java2s.com/Tutorials/Java/Java_Swing/0970__Java_Swing_JSlider.htm)

**福利时间，博主写了一个小例子，想要的朋友可以参见[github](https://github.com/chenjiabing666/Java_demo/tree/master/031),不要忘了随手点个赞哦！！！**




















>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*