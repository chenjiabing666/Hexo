---
title: swing干货系列之JTextField
date: 2017-04-08 20:32:04
categories: java学习
tags: swing
---
# Swing系列之JTextField(单行文本框)
## 介绍
* `JTextField`是一个轻量级组件，它允许编辑单行文本。
* `JTextField` 具有建立字符串的方法，此字符串用作针对被激发的操作事件的命令字符串。`java.awt.TextField` 把字段文本用作针对 `ActionEvent` 的命令字符串。如果通过 setActionCommand 方法设置的命令字符串不为 null，则 JTextField 将使用该字符串来保持与 java.awt.TextField 的兼容性，否则将使用字段文本来保持兼容性。

* `setEchoChar` 和 `getEchoChar` 方法不是直接提供的，以避免可插入的外观的新实现意外公开密码字符。为了提供类似密码的服务，单独的类 `JPasswordField` 扩展了 `JTextField`，从而通过可插入外观独立地提供此服务。
* `JTextField` 的水平对齐方式可以设置为左对齐、前端对齐、居中对齐、右对齐或尾部对齐。右对齐/尾部对齐在所需的字段文本尺寸小于为它分配的尺寸时使用。这是由 setHorizontalAlignment 和 `getHorizontalAlignment` 方法确定的。默认情况下为前端对齐。
* 文本字段如何使用 VK_ENTER 事件取决于文本字段是否具有任何操作侦听器。如果具有操作侦听器，则 VK_ENTER 导致侦听器获取一个 ActionEvent，并使用 VK_ENTER 事件。这与 AWT 文本字段处理 VK_ENTER 事件的方式是兼容的。如果文本字段没有操作侦听器，则从 1.3 版本开始不使用 VK_ENTER 事件。而是处理祖先组件的绑定，这将启用 JFC/Swing 的默认按钮特性。
* Swing 不是线程安全的

## 构造函数
* `JTextField()` 构造一个新的 TextField
* `JTextField(Document doc, String text, int columns)`  构造一个新的 JTextField，它使用给定文本存储模型和给定的列数。
* `JTextField(int columns)`  构造一个具有指定列数的新的空 TextField。
* `JTextField(String text) `构造一个用指定文本初始化的新 TextField。
* `JTextField(String text, int columns)`   构造一个用指定文本和列初始化的新 TextField。

## 常用的函数
* `get/setHorizontalAlignment(int alignment`) 设置/得到文本的水平对齐方式。其中水平的对齐方式有：JTextField.LEFT
>1. `JTextField.CENTER`
>1. `JTextField.RIGHT`
>1. `JTextField.LEADING` (the default)
>1. `JTextField.TRAILING`

* `setFont(Font font)`   设置字体
* `setScrollOffset(int scrollOffset)`  获取滚动偏移量（以像素为单位）。
* `setDocument(Document doc)`  将编辑器与一个文本文档关联，这里的意思就是将此文本框与一个文本文档关联，这将会保持内容一致，如果一个改变了，另外一个也会改变。
* `setInputVerifier(verifier)`    设置验证方式，如果此文本不能通过验证那么就不能将焦点聚焦到下一个组件上，就会一直聚焦到这个文本框上
* `setDragEnabled(boolean x)`   设置在文本框中是否能够拖放文本,为true则是能够，这里的意思就是能够将文本选中后能不能将文本拖走
* `addActionListener(ActionListener action)`   添加监听机制，输入文本按回车即可触发，和按钮的监听机制相同
* `write(InfileWriter writer)`  将文本框中的内容输入到文件中
* `addKeyListener(KeyListener event)`   添加键盘监听，在文本框中输入内容时会触发键盘，其中有按下，释放，键入的动作，详情见[官方文档](http://tool.oschina.net/uploads/apidocs/jdk-zh/java/awt/event/KeyListener.html)
* `addCaretListener(CareListener event)`  添加一个侦听文本组件插入符的位置更改的侦听器，只要鼠标指针的位置改变就会触发

## 一个简单的实例
```java
import javax.swing.*;
import java.awt.*;

class text extends JFrame {
    private JTextField textField1;
    private JTextField textField2;

    public static void main(String args[]) {
        text my = new text();
        my.setVisible(true);

    }

    public text() {
        //this.setBounds(100,100,300,200);
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        JPanel panel = new JPanel(new GridLayout(2, 1));
        textField1 = new JTextField(10);
        textField2 = new JTextField();
        panel.add(textField1);
        panel.add(textField2);
        this.getContentPane().add(panel, BorderLayout.CENTER);
        this.pack();
        InputVerifier verifier = new InputVerifier() {    //添加验证方式
            @Override
            public boolean verify(JComponent input) {     //重载函数
                boolean value;
                textField1 = (JTextField) input;    //将input组件强制转化为JTextField类型的单行文本框
                return textField1.getText().equals("pass");  //判断是否输入的时pass,如果不是就会验证错误

            }
        };
        textField1.setInputVerifier(verifier);   //设置验证方式
        textField1.setHorizontalAlignment(JTextField.CENTER);   //设置水平对齐方式
        Font font = new Font("楷体", Font.BOLD + Font.ITALIC, 20);
        textField1.setFont(font);   //设置字体
        textField1.setDragEnabled(true);  //设置在单行文本框中能够拖放文本，如果为false则不能够拖放文本


    }
}
```
## 关联文本文档
```java
import java.awt.Container;
import java.awt.GridLayout;
/*from   w  ww.jav  a  2s . co m*/
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JTextField;
import javax.swing.text.Document;

public class Main extends JFrame {
  JLabel nameLabel = new JLabel("Name:");
  JLabel mirroredNameLabel = new JLabel("Mirrored:");
  JTextField name = new JTextField(20);
  JTextField mirroredName = new JTextField(20);

  public Main() {
    this.setDefaultCloseOperation(EXIT_ON_CLOSE);
    this.setLayout(new GridLayout(2, 0));

    Container contentPane = this.getContentPane();
    contentPane.add(nameLabel);
    contentPane.add(name);
    contentPane.add(mirroredNameLabel);
    contentPane.add(mirroredName);

    Document nameModel = name.getDocument();    //得到文本框的文本文档，将之与第二个文本框关联
    mirroredName.setDocument(nameModel);           //两个文本框中的内容相互关联，这样只需要在一个里面输入文本，同时也会在另外一个文本框中显示
    
    pack();
    setVisible(true);    
  }

  public static void main(String[] args) {
    Main frame = new Main();

  }
}

```
>**说明：这里是将两个文本框相关联，这样就能达到一个文本框输入的同时，另外一个也会同时更新内容**

## Action Listener(动作监听机制)
**输入文本后按回车即可触发**
```java
import java.awt.event.ActionEvent;
//from  w  w  w. ja va2s  .c o m
import javax.swing.JFrame;
import javax.swing.JTextField;

public class Main {

  public static void main(String[] a) {
    JFrame frame = new JFrame();
    frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

    JTextField jTextField1 = new JTextField();

    jTextField1.setText("jTextField1");
    //添加监听机制
    jTextField1.addActionListener(new   java.awt.event.ActionListener() {
      public void actionPerformed(ActionEvent e) {
        System.out.println("action");
      }
    });
    frame.add(jTextField1);

    frame.setSize(300, 200);
    frame.setVisible(true);
  }

}
```

## 验证文本内容
**使用[InputVerifier](http://tool.oschina.net/uploads/apidocs/jdk-zh/javax/swing/InputVerifier.html#InputVerifier())验证**

```java
import java.awt.BorderLayout;
import javax.swing.InputVerifier;
import javax.swing.JComponent;
import javax.swing.JFrame;
import javax.swing.JTextField;
public class Main {
  public static void main(String args[]) {
    JFrame frame = new JFrame("Verifier Sample");
    frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    JTextField textField1 = new JTextField();
    JTextField textField2 = new JTextField();
    InputVerifier verifier = new InputVerifier() {     //创建一个验证
      public boolean verify(JComponent comp) {
        boolean returnValue;
        JTextField textField = (JTextField) comp;      //强制转换，将控件类型的comp转换成JTextFiled类型的
        try {
          Integer.parseInt(textField.getText());    //将输入的内容转化程int类型，如果输入的字符串不是十进制的话就会触发                                                          //NumberFormateException错误
          returnValue = true;
        } catch (NumberFormatException e) {   
          returnValue = false;
        }
        return returnValue;        //如果返回false的话，那么指针就会一直聚焦在此文本框中，不能移动到其他的组件上
      }
    };
    textField1.setInputVerifier(verifier);
    frame.add(textField1, BorderLayout.NORTH);
    frame.add(textField2, BorderLayout.CENTER);
    frame.setSize(300, 100);
    frame.setVisible(true);
  }
}
```
> **说明：如果返回false的话，那么指针就会一直聚焦在此文本框中，不能移动到其他的组件上**

## 将文本框中的内容保存到文件中
```java

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.File;
import java.io.FileWriter;
import java.io.IOException;

class Main extends JFrame {
    private JTextField textField;
    private FileWriter writer;

    public static void main(String args[]) {
        Main my = new Main();
        my.setVisible(true);
    }

    public Main() {
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        JPanel panel = new JPanel(new BorderLayout());
        JButton button = new JButton("运行");
        JLabel label = new JLabel("name");
        textField = new JTextField();
        panel.add(label, BorderLayout.WEST);
        panel.add(textField, BorderLayout.CENTER);
        String filename = "text.txt";
        button.addActionListener(new ActionListener() {    //添加一个按钮触发装置，这里只要点击一下anniu就会将文本框中的内容输入到文件中
            @Override
            public void actionPerformed(ActionEvent e) {
                try {
                    writer = new FileWriter(filename, false);   //创建一个写入文件的对象，这里的false表示不在文件的末尾添加
                    textField.write(writer);     //将单行文本中输入的内容写入到文件中
                    writer.close();
                } catch (IOException e1) {
                    e1.printStackTrace();
                    System.out.println("false");
                }
            }
        });
        panel.add(button, BorderLayout.SOUTH);
        this.getContentPane().add(panel, BorderLayout.CENTER);
        this.pack();
    }

}
```
>**说明：这里使用的是`FileWriter`类将内容写入到文件中，详情请看我的上一篇[文章](https://chenjiabing666.github.io/2017/03/25/java%E5%9B%BE%E5%BD%A2%E4%B8%8E%E6%96%87%E6%9C%AC%E5%A4%84%E7%90%86%E4%B8%80/)**

## 复制、粘贴、剪切文本
>**这里使用的时`copy()`、`paste()`、`cut()`函数**

```java
import java.awt.FlowLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JTextField;
import javax.swing.event.CaretEvent;
import javax.swing.event.CaretListener;

public class Main {
  public static void main(String args[]) {
    final JTextField textField = new JTextField(15);
    JButton buttonCut = new JButton("Cut");
    JButton buttonPaste = new JButton("Paste");
    JButton buttonCopy = new JButton("Copy");

    JFrame jfrm = new JFrame("Cut, Copy, and Paste");
    jfrm.setLayout(new FlowLayout());
    jfrm.setSize(230, 150);
    jfrm.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

    buttonCut.addActionListener(new ActionListener() {
      public void actionPerformed(ActionEvent le) {
        textField.cut();
      }
    });

    buttonPaste.addActionListener(new ActionListener() {
      public void actionPerformed(ActionEvent le) {
        textField.paste();
      }
    });

    buttonCopy.addActionListener(new ActionListener() {
      public void actionPerformed(ActionEvent le) {
        textField.copy();
      }
    });

    textField.addCaretListener(new CaretListener() {
      public void caretUpdate(CaretEvent ce) {
        System.out.println("All text: " + textField.getText());
        if (textField.getSelectedText() != null)
          System.out.println("Selected text: " + textField.getSelectedText());
        else
          System.out.println("Selected text: ");
      }
    });

    jfrm.add(textField);
    jfrm.add(buttonCut);
    jfrm.add(buttonPaste);
    jfrm.add(buttonCopy);
    jfrm.setVisible(true);
  }
}
```
>**说明：这里使用的时用三个按钮监听操作，只需要按住对应的按钮就会触发机制**

## 添加键盘监听机制

```java

import java.awt.Dimension;
import java.awt.FlowLayout;
import java.awt.HeadlessException;
import java.awt.event.KeyAdapter;
import java.awt.event.KeyEvent;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JTextField;

public class Main extends JFrame {
  public Main() throws HeadlessException {
    setSize(200, 200);
    setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    setLayout(new FlowLayout(FlowLayout.LEFT));

    JLabel usernameLabel = new JLabel("Username: ");
    JTextField usernameTextField = new JTextField();
    usernameTextField.setPreferredSize(new Dimension(100, 20));
    add(usernameLabel);
    add(usernameTextField);

    usernameTextField.addKeyListener(new KeyAdapter() {   //创建机制
      public void keyReleased(KeyEvent e) {        //重载函数，释放按键触发
        JTextField textField = (JTextField) e.getSource();  //得到最初发生event的组件对象,既是文本框对象
        String text = textField.getText();
        textField.setText(text.toUpperCase());      //将所有的小写字母转换成大写字母
      }
       public void keyTyped(KeyEvent e) {           //键入时触发
      }

      public void keyPressed(KeyEvent e) {       //释放按键时触发的函数
      }   
    });
  }

  public static void main(String[] args) {
    new Main().setVisible(true);
  }
}
```
## 添加插入符位置变化的监听机制
**使用的是[CareListener](http://tool.oschina.net/uploads/apidocs/jdk-zh/javax/swing/event/CaretListener.html)类来实现**

```java
package com.zzk;

import java.awt.BorderLayout;
import java.awt.Color;
import java.awt.Font;
import java.awt.Graphics;
import java.awt.Graphics2D;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JTextField;
import javax.swing.event.CaretEvent;
import javax.swing.event.CaretListener;

public class ClockwiseTextFrame extends JFrame {
    private JTextField textField;
    ClockwiseTextPanel clockwiseTextPanel = new ClockwiseTextPanel(); // 创建面板类的实例
    
    public static void main(String args[]) { // 主方法
        ClockwiseTextFrame frame = new ClockwiseTextFrame(); // 创建窗体类的实例
        frame.setVisible(true); // 显示窗体
    }
    
    public ClockwiseTextFrame() {
        super(); // 调用超类的构造方法
        setTitle("顺时针旋转文字"); // 窗体标题
        setBounds(100, 100, 340, 240); // 窗体的显示位置和大小
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 窗体关闭方式
        add(clockwiseTextPanel); // 将面板类的实例添加到窗体容器中
        textField = new JTextField();
        textField.addCaretListener(new CaretListener() {
            public void caretUpdate(CaretEvent arg0) {
                String text = textField.getText();// 获取文本框字符串
                clockwiseTextPanel.setText(text);// 为面板中的text变量赋值
            }
        });
        getContentPane().add(textField, BorderLayout.SOUTH);
    }
    
    class ClockwiseTextPanel extends JPanel { // 创建内部面板类
        private String text;
        public ClockwiseTextPanel() {
            setOpaque(false);// 设置面板为透明
            setLayout(null);// 设置为绝对布局
        }
        public String getText() {
            return text; // 获得成员变量的值
        }
        public void setText(String text) {
            this.text = text;// 为成员变量赋值
            repaint();// 调整paint()方法
        }
        public void paint(Graphics g) {// 重写paint()方法
            Graphics2D g2 = (Graphics2D) g;// 获得Graphics2D的实例
            int width = getWidth();// 获得面板的宽度
            int height = getHeight();// 获得面板的高度
            if (text != null) {
                char[] array = text.toCharArray();// 将文本转换为字符数组
                int len = array.length * 5;// 定义圆的半径，同时可以调整文字的距离
                Font font = new Font("宋体", Font.BOLD, 22);// 创建字体
                g2.setFont(font);// 设置字体
                double angle = 0;// 定义初始角度
                for (int i = 0; i < array.length; i++) {// 遍历字符串中的字符
                    if (i == 0) {
                        g2.setColor(Color.BLUE);// 第一个字符用蓝色
                    } else {
                        g2.setColor(Color.BLACK);// 其他字符用黑色
                    }
                    int x = (int) (len * Math.sin(Math.toRadians(angle + 270)));// 计算每个文字的横坐标位置
                    int y = (int) (len * Math.cos(Math.toRadians(angle + 270)));// 计算每个文字的纵坐标位置
                    g2.drawString(array[i] + "", width / 2 + x, height / 2 - y);// 绘制字符
                    angle = angle + 360d / array.length;// 改变角度
                }
            }
        }
    }
}
```




## 参考文档
>* [官方网站](http://tool.oschina.net/uploads/apidocs/jdk-zh/javax/swing/JTextField.html#setScrollOffset(int))
>* [英文文档](http://www.java2s.com/Tutorials/Java/Java_Swing/0820__Java_Swing_JTextField.htm)





          



          



>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*