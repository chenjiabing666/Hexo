---
title: Swing干货系列之JSplitPane(分割面板)
date: 2017-03-28 22:49:39
categories: java学习
tags: swing
---

# Swing中的JSplitPane(分割面板)
## 引言
>`JSplitPane` 用于分隔两个（只能两个）`Component`。两个 `Component` 图形化分隔以外观实现为基础，并且这两个 `Component` 可以由用户交互式调整大小。有关如何使用 `JSplitPane` 的信息，请参阅 [The Java Tutorial](http://tool.oschina.net/apidocs/apidoc?api=jdk-zh) 中的 How to Use Split Panes 一节。

>使用 `JSplitPane.HORIZONTAL_SPLIT` 可让分隔窗格中的两个 Component 从左到右排列，或者使用 `JSplitPane.VERTICAL_SPLIT` 使其从上到下排列。改变 Component 大小的首选方式是调用 `setDividerLocation`，其中 `location` 是新的 x 或 y 位置，具体取决于 JSplitPane 的方向。

>要将 Component 调整到其首选大小，可调用 `resetToPreferredSizes`。

>当用户调整 Component 的大小时，Component 的最小大小用于确定 Component 能够设置的最大/最小位置。如果两个组件的最小大小大于分隔窗格的大小，则分隔条将不允许您调整其大小。改变 JComponent 最小大小，请参阅 [JComponent.setMinimumSize(java.awt.Dimension)](http://tool.oschina.net/apidocs/apidoc?api=jdk-zh)。

>当用户调整分隔窗格大小时，新的空间以 resizeWeight 为基础在两个组件之间分配。默认情况下，值为 0 表示右边/底部的组件获得所有空间，而值为 1 表示左边/顶部的组件获得所有空间。
>**补充说明：**
>>这里的`JComponebt.SetMinimumSize(java.awt.Dimension)`:用于设置组件的最小值，这里的Dimension是一个封装组件的高度和宽度的一个类，其中的一个构造函数就是`Dimension(int width,int height)`,详情见[文档](http://tool.oschina.net/apidocs/apidoc?api=jdk-zh)，当然有设置最小的就有设置最大的啊，详情看文档吧

## 构造函数
>* `public JSplitPanel()`:创建一个配置为将其子组件水平排列、无连续布局、为组件使用两个按钮的新 JSplitPane
>* `public JSplitPanel(int newOrientation)`:创建一个指定方向的分割板，这里的`newOrientation`可以设置两个值， `VERTICAL_SPLIT`(设置分割板为上下布局),`HORIZONTAL_SPLIT`(设置分隔板左右布局)
>* `public JSplitPane(int newOrientation,Component newLeftComponent,Component newRightComponent)`:创建一个具有指定方向和不连续重绘的指定组件的新 JSplitPane。
>* `public JSplitPane(int newOrientation,boolean newContinuousLayout,Component newLeftComponent,Component newRightComponent)`:创建一个具有指定方向、重绘方式和指定组件的新 JSplitPane。

## 常用方法
>* `setContinuousLayout(boolean newContinuousLayout)`:设置是否连续重新显示组件，如果为false就会发现在调整面板的过程中会显示一道黑线，只有当停下的时候才能正常的显示，默认是`false`
>* `setDividerSize(int newSize)`:设置分割条的大小
>*　`setDividerLocation(double size)`:设置分隔条的位置,这里的size是小数，个人觉得官方文档好像这里有点对劲，相当于占整个面板的百分比
>* `setLeftComponent(Componentcomp)`/`setTopComponent(Component comp)`: 将组件设置到分隔条的上面或者左边。
>* `setRightComponent(Component comp)`/`setBottomComponent(Component comp)`:将组件设置到分隔条的下面或者右边。
>* `setOneTouchExpandable(boolean newValue)`:设置 oneTouchExpandable 属性的值，要使 JSplitPane 在分隔条上提供一个 UI 小部件来快速展开/折叠分隔条，此属性必须为 true。

>**补充说明：**
>>上面只是常用的几个函数，具体的请看官方文档，注意这里的setLeftComponent的四个设置组件的函数要根据分隔板的分布来确定

# 开始撸代码
>**初步实现(创建两个按钮实现分隔板的布局)**
```java

import java.awt.BorderLayout;
import javax.swing.JButton;
import javax.swing.JComponent;
import javax.swing.JFrame;
import javax.swing.JSplitPane;

public class Main {
  public static void main(String[] a) {
    JFrame horizontalFrame = new JFrame();
    horizontalFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

    JComponent topButton = new JButton("Left");
    JComponent bottomButton = new JButton("Right");
    final JSplitPane splitPane = new JSplitPane(JSplitPane.VERTICAL_SPLIT);

    splitPane.setTopComponent(topButton);
    splitPane.setBottomComponent(bottomButton);

    

    horizontalFrame.add(splitPane, BorderLayout.CENTER);
    horizontalFrame.setSize(150, 150);
    horizontalFrame.setVisible(true);

    splitPane.setDividerLocation(0.5);
  }
}
```

>**更进一步(两种布局的操作)**
```java
import java.awt.BorderLayout;
import javax.swing.JButton;
import javax.swing.JComponent;
import javax.swing.JFrame;
import javax.swing.JSplitPane;

public class Main {
  public static void main(String[] a) {
    JFrame horizontalFrame = new JFrame();
    horizontalFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    
    
    JComponent leftButton = new JButton("Left");
    JComponent rightButton = new JButton("Right");
    JSplitPane splitPane = new JSplitPane(JSplitPane.VERTICAL_SPLIT);
    splitPane.setLeftComponent(leftButton);
    splitPane.setRightComponent(rightButton);
    
    horizontalFrame.add(splitPane, BorderLayout.CENTER);
    horizontalFrame.setSize(150, 150);
    horizontalFrame.setVisible(true);
    
  }
}
```
>**嵌套分隔板**
```java
import javax.swing.JApplet;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JSplitPane;
public class Main{
  
  public static void main(String[] a) {
    int HORIZSPLIT = JSplitPane.HORIZONTAL_SPLIT;

    int VERTSPLIT = JSplitPane.VERTICAL_SPLIT;

    boolean continuousLayout = true;


    JLabel label1 = new JLabel("a");
    JLabel label2 = new JLabel("b");
    JLabel label3 = new JLabel("c");
    JSplitPane splitPane1 = new JSplitPane(VERTSPLIT, continuousLayout, label1, label2);
    splitPane1.setOneTouchExpandable(true);
    splitPane1.setDividerSize(2);
    splitPane1.setDividerLocation(0.5);

    JSplitPane splitPane2 = new JSplitPane(HORIZSPLIT, splitPane1, label3);//将分隔板和一个label放在第二个分割板中实现嵌套
    splitPane2.setOneTouchExpandable(true);
    splitPane2.setDividerLocation(0.4);
    splitPane2.setDividerSize(2);

    JFrame frame = new JFrame();
    frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    frame.add(splitPane2);
    frame.pack();
    frame.setVisible(true);
  }
}
```
>**事件监听**
```java
import java.awt.BorderLayout;
import java.beans.PropertyChangeEvent;
import java.beans.PropertyChangeListener;
// w  w  w . j a  va2s .  co m
import javax.swing.JButton;
import javax.swing.JComponent;
import javax.swing.JFrame;
import javax.swing.JSplitPane;

public class Main {
  public static void main(String args[]) {
    JFrame frame = new JFrame("Property Split");
    frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

    JSplitPane splitPane = new JSplitPane(JSplitPane.VERTICAL_SPLIT);
    splitPane.setContinuousLayout(true);
    splitPane.setOneTouchExpandable(true);

    JComponent topComponent = new JButton("A");
    splitPane.setTopComponent(topComponent);

    JComponent bottomComponent = new JButton("B");
    splitPane.setBottomComponent(bottomComponent);

    PropertyChangeListener propertyChangeListener = new PropertyChangeListener() {
      public void propertyChange(PropertyChangeEvent changeEvent) {
        JSplitPane sourceSplitPane = (JSplitPane) changeEvent.getSource();
        String propertyName = changeEvent.getPropertyName();
        if (propertyName.equals(JSplitPane.LAST_DIVIDER_LOCATION_PROPERTY)) {
          int current = sourceSplitPane.getDividerLocation();
          System.out.println("Current: " + current);
          Integer last = (Integer) changeEvent.getNewValue();
          System.out.println("Last: " + last);
          Integer priorLast = (Integer) changeEvent.getOldValue();
          System.out.println("Prior last: " + priorLast);
        }
      }
    };

    splitPane.addPropertyChangeListener(propertyChangeListener);

    frame.add(splitPane, BorderLayout.CENTER);
    frame.setSize(300, 150);
    frame.setVisible(true);
  }
}
```
>**说明**
>无论 `bean` 何时更改 `bound` 属性，都会激发一个 `PropertyChange` 事件。可以向源 `bean` 注册一个 `PropertyChangeListener`，以便获得所有绑定 (`bound`) 属性更改的通知。
>### 类 [PropertyChangeEvent](http://tool.oschina.net/apidocs/apidoc?api=jdk-zh)
>无论 bean 何时更改 "bound" 或 "constrained" 属性，都会提交一个 "PropertyChange" 事件。PropertyChangeEvent 对象被作为参数发送给 PropertyChangeListener 和 VetoableChangeListener 方法。
通常 PropertyChangeEvent 还附带名称和已更改属性的旧值和新值。如果新值是基本类型（比如 int 或 boolean），则必须将它包装为相应的 java.lang.* Object 类型（比如 Integer 或 Boolean）。
如果旧值和新值的真实值是未知的，则可能为它们提供 null 值。
事件源可能发送一个 null 对象作为名称，以指示其属性的任意事件集已更改。在这种情况下，旧值和新值应该仍然为 null。
>`getSource()`:返回最初未变化的对象，未Object类型的,因此这里需要强制转换成`JSplitPanel`


## 参考文章
>* [官方文档](http://tool.oschina.net/apidocs/apidoc?api=jdk-zh)
>* [英文Swing教程](http://www.java2s.com/Tutorials/Java/Java_Swing/1310__Java_Swing_JSplitPane.htm)

































>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*