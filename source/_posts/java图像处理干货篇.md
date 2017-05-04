---
title: java图像处理干货篇一
date: 2017-04-04 20:17:07
categories: java学习
tags: java图形与文本处理
---
# java图像处理干货篇
## 绘制图像
>绘制图像主要用到的是Graphics类中drawImage方法，当然Graphics2D中也有相应的方法
>>主要的用法：
>>* `public abstract boolean drawImage(Image img,x,y,ImageObserver observer)`:img是`Image`对象，x,y起始坐标,`observer`是观察对象
>>* `drawImage(Image img,int x,int y,int width,int height,Imageobersver observer)`:`width`和`height`是指定图像的宽度和高度，主要的作用是放大和缩小图像
>> * `drawImage(Image img,int dx1,int dy1,int dx2,int dx2,int sx1,int sy1,int sx2,int sy2,ImageObserver observer)`:主要用来翻转图形,通过互换源矩形的第一个和第二个角的x坐标可以实现水平翻转，通过互换源矩形的第一个和第二个角的y坐标可以实现垂直翻转

## 翻转图像
```java
package com.zzk;
import java.awt.BorderLayout;
import java.awt.Graphics;
import java.awt.Image;
import java.awt.Toolkit;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.net.URL;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
public class PartImageFrame extends JFrame {
    private Image img = null;  // 声明图像对象
    private PartImagePanel imagePanel = null;  // 声明图像面板对象
    private int dx1, dy1, dx2, dy2;   // 目标矩形第一个角与第二个角的X、Y坐标
    private int sx1, sy1, sx2, sy2;   // 源矩形第一个角与第二个角的X、Y坐标
    public static void main(String args[]) {
        PartImageFrame frame = new PartImageFrame();
        frame.setVisible(true);
    }
    public PartImageFrame() {
        super();
        URL imgUrl = PartImageFrame.class.getResource("/img/image.jpg");// 获取图片资源的路径
        img = Toolkit.getDefaultToolkit().getImage(imgUrl); // 获取图像资源
        dx2 = sx2 = 340; // 初始化图像大小
        dy2 = sy2 = 200; // 初始化图像大小
        imagePanel = new PartImagePanel();  // 创建图像面板对象
        this.setBounds(200, 160, 355, 276); // 设置窗体大小和位置
        this.add(imagePanel); // 在窗体中部位置添加图像面板对象
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 设置窗体关闭模式
        this.setTitle("翻转图像"); // 设置窗体标题
        final JPanel panel = new JPanel();
        getContentPane().add(panel, BorderLayout.SOUTH);
        final JButton btn_h = new JButton();
        btn_h.addActionListener(new ActionListener() {
            public void actionPerformed(final ActionEvent e) {
                // 下面3行代码用于交换sx1和sx2的值
                int x = sx1;
                sx1 = sx2;
                sx2 = x;
                imagePanel.repaint();  // 重新调用面板类的paint()方法
            }
        });
        btn_h.setText("水平翻转");
        panel.add(btn_h);
        final JButton btn_v = new JButton();
        btn_v.addActionListener(new ActionListener() {
            public void actionPerformed(final ActionEvent e) {
                // 下面3行代码用于交换sy1和sy2的值
                int y = sy1;
                sy1 = sy2;
                sy2 = y;
                imagePanel.repaint();// 重新调用面板类的paint()方法
            }
        });
        btn_v.setText("垂直翻转");
        panel.add(btn_v);
    }
    // 创建面板类
    class PartImagePanel extends JPanel {
        public void paint(Graphics g) {
            g.clearRect(0, 0, this.getWidth(), this.getHeight());// 清除绘图上下文的内容
            g.drawImage(img, dx1, dy1, dx2, dy2, sx1, sy1, sx2, sy2, this);// 绘制图像

        }
    }
}
```
## 旋转图像
>主要用到的是`Graphics2D`类中的`rotate`函数，定义如下:`public abstract void rotate(double theta)`: `theta`是角度，以弧度为单位
>**代码如下**

```javas
package com.zzk;
import java.awt.*;
import java.net.URL;
import javax.swing.*;
public class RotateImageFrame extends JFrame {
    private Image img = null;
    private RotatePanel rotatePanel = null;
    public RotateImageFrame() {
        URL imgUrl = RotateImageFrame.class.getResource("/img/image.jpg");// 获取图片资源的路径
        img = Toolkit.getDefaultToolkit().getImage(imgUrl);   // 获取图片资源
        rotatePanel = new RotatePanel();  // 创建旋转图像的面板对象
        this.setBounds(150, 120, 380, 310);                 // 设置窗体大小和位置
        add(rotatePanel);// 在窗体上放置图像面板
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);  // 设置窗体关闭模式
        this.setTitle("旋转图像");                     // 设置窗体标题
    }
    public static void main(String[] args) {
        new RotateImageFrame().setVisible(true);
    }
    class RotatePanel extends JPanel {
        public void paint(Graphics g) {
            Graphics2D g2 = (Graphics2D) g;         // 获得Graphics2D对象
            g2.drawImage(img, 80, 10, 260, 150, this);      // 绘制指定大小的图片
            g2.rotate(Math.toRadians(10));                 // 将图片旋转10度
            g2.drawImage(img, 80, 10, 260, 150, this);      // 绘制指定大小的图片
            g2.rotate(Math.toRadians(10));                // 将图片旋转10度
            g2.drawImage(img, 80, 10, 260, 150, this);      // 绘制指定大小的图片
        }
    }
}

```
 ## 倾斜图像
>主要用到的是`Graphics2D`中的`shear`函数定义如：`public abstract void shear(doubel shx,double shy)`:`shx`是在正x轴上移动坐标的乘数，它可以作为其纵坐标的值,shy是在正y轴方形移动坐标的乘数，它可以作为其x坐标的函数。
>**本人的理解：
>倾斜画布，如果shx>0就是向正方向平移，平移的长度为shx*height(图形纵坐标的值，如果是矩形就是乘以矩形的高)
>相同的对于shy是乘以矩形宽**
```java
package com.zzk;
import java.awt.*;
import java.net.URL;
import javax.swing.*;
public class ShearImageFrame extends JFrame {
	private Image img;
	private ShearImagePanel canvasPanel = null;
	public ShearImageFrame() {
        URL imgUrl = ShearImageFrame.class.getResource("/img/image.jpg");// 获取图片资源的路径
        img = Toolkit.getDefaultToolkit().getImage(imgUrl);  // 获取图片资源
        canvasPanel = new ShearImagePanel();     // 创建绘制倾斜图像的面板对象
        this.setBounds(100, 100, 360, 240);                // 设置窗体大小和位置
        add(canvasPanel);// 在窗体上添加面板对象
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 设置窗体关闭模式
        this.setTitle("倾斜图像");                    // 设置窗体标题
	}
	public static void main(String[] args) {
		new ShearImageFrame().setVisible(true);
	}
	class ShearImagePanel extends JPanel {// 绘制倾斜图像的面板类
		public void paint(Graphics g) {
			Graphics2D g2=(Graphics2D) g;// 获得Graphics2D对象
			g2.shear(0, -0.5);// 倾斜图像
			g2.drawImage(img, 10, 20, 220, 160, this);     // 绘制指定大小的图片
		}
	}
}
```

## 裁剪图片
>`public BufferedImage createScreenCapture(Rectangle screenRect)`:返回的是一个BufferedImage对象，参数是Rectangle对象，这个函数是Robot类中的，主要用于裁剪图形

```java
package com.zzk;
import java.awt.AWTException;
import java.awt.BasicStroke;
import java.awt.BorderLayout;
import java.awt.Color;
import java.awt.Graphics;
import java.awt.Graphics2D;
import java.awt.Image;
import java.awt.Rectangle;
import java.awt.Robot;
import java.awt.Toolkit;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;
import java.awt.event.MouseMotionAdapter;
import java.awt.image.BufferedImage;
import java.net.URL;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JSplitPane;

public class CutImageFrame extends JFrame {
    private Image img = null; // 声明图像对象
    private OldImagePanel oldImagePanel = null; // 声明图像面板对象
    private int pressPanelX = 0, pressPanelY = 0;// 鼠标按下点的X、Y坐标 
    private int pressX = 0, pressY = 0;// 鼠标按下点在屏幕上的X、Y坐标
    private int releaseX = 0, releaseY = 0;// 鼠标释放点在屏幕上的X、Y坐标
    private Robot robot = null;  // 声明Robot对象
    private BufferedImage buffImage = null; // 声明缓冲图像对象
    private CutImagePanel cutImagePanel = new CutImagePanel(); // 创建绘制裁剪结果的面板
    private boolean flag = false;  // 声明标记变量，为true时显示选择区域的矩形，否则不显示

    public static void main(String args[]) {
        CutImageFrame frame = new CutImageFrame();
        frame.setVisible(true);
    }

    public CutImageFrame() {
        super();
        URL imgUrl = CutImageFrame.class.getResource("/img/image.jpg");// 获取图片资源的路径
        img = Toolkit.getDefaultToolkit().getImage(imgUrl); // 获取图像资源
        oldImagePanel = new OldImagePanel(); // 创建图像面板对象
        this.setBounds(200, 160, 355, 276); // 设置窗体大小和位置
        final JSplitPane splitPane = new JSplitPane();
        splitPane.setDividerLocation((this.getWidth() / 2) - 10);
        getContentPane().add(splitPane, BorderLayout.CENTER);
        splitPane.setLeftComponent(oldImagePanel);
        splitPane.setRightComponent(cutImagePanel);
        oldImagePanel.addMouseListener(new MouseAdapter() {
            public void mousePressed(final MouseEvent e) {  // 鼠标键按下事件
                pressPanelX = e.getX(); // 获得鼠标按下点的X坐标 
                pressPanelY = e.getY();// 获得鼠标按下点的Y坐标 
                pressX = e.getXOnScreen() + 1;// 鼠标按下点在屏幕上的X坐标加1，即去除选择线
                pressY = e.getYOnScreen() + 1;// 鼠标按下点在屏幕上的Y坐标加1，即去除选择线
                flag = true;// 为标记变量赋值为true
            }

            public void mouseReleased(final MouseEvent e) { // 鼠标键释放事件
                releaseX = e.getXOnScreen() - 1;// 鼠标释放点在屏幕上的X坐标减1，即去除选择线
                    releaseY = e.getYOnScreen() - 1;// 鼠标释放点在屏幕上的Y坐标减1，即去除选择线
                    try {
                    robot = new Robot();// 创建Robot对象
                    if (releaseX - pressX > 0 && releaseY - pressY > 0) {
                        Rectangle rect = new Rectangle(pressX, pressY, releaseX
                                - pressX, releaseY - pressY);// 创建Rectangle对象
                        buffImage = robot.createScreenCapture(rect);// 获得缓冲图像对象
                        cutImagePanel.repaint(); // 调用CutImagePanel面板的paint()方法
                    }
                } catch (AWTException e1) {
                    e1.printStackTrace();
                }
                flag = false;// 为标记变量赋值为false
            }
        });
        oldImagePanel.addMouseMotionListener(new MouseMotionAdapter() {
            public void mouseDragged(final MouseEvent e) {// 鼠标拖动事件
                if (flag) {
                    releaseX = e.getXOnScreen();// 获得鼠标释放点在屏幕上的X坐标
                    releaseY = e.getYOnScreen();// 获得鼠标释放点在屏幕上的Y坐标
                    oldImagePanel.repaint();// 调用OldImagePanel面板的paint()方法
                }
            }
        });
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // 设置窗体关闭模式
        this.setTitle("裁剪图片"); // 设置窗体标题
    }




    class OldImagePanel extends JPanel {// 创建绘制原图像的面板类

        public void paint(Graphics g) {
            Graphics2D g2 = (Graphics2D) g;
            g2.drawImage(img, 0, 0, this.getWidth(), this.getHeight(), this);// 绘制图像
            g2.setColor(Color.WHITE);
            if (flag) {
                float[] arr = {5.0f}; // 创建虚线模式的数组
                BasicStroke stroke = new BasicStroke(1, BasicStroke.CAP_BUTT,
                        BasicStroke.JOIN_BEVEL, 1.0f, arr, 0); // 创建宽度是1的平头虚线笔画对象
                g2.setStroke(stroke);// 设置笔画对象
                g2.drawRect(pressPanelX, pressPanelY, releaseX - pressX,
                        releaseY - pressY);// 绘制矩形选区
            }
        }
    }

    class CutImagePanel extends JPanel {// 创建绘制裁剪结果的面板类

        public void paint(Graphics g) {
            g.clearRect(0, 0, this.getWidth(), this.getHeight());// 清除绘图上下文的内容
            g.drawImage(buffImage, 0, 0, releaseX - pressX, releaseY - pressY,
                    this);// 绘制图像
        }




    }
}
```
## 调整图片的亮度
>`RescaleOp`类中的`filter`方法原缓冲图像进行重缩放，定义如下
>`public abstract BufferedImage filter(BufferedImage src,BufferedImage dst)`:src是要过滤的源对象，dst是目标对象，或则为null

```java
package com.zzk;

import java.awt.BorderLayout;
import java.awt.Graphics;
import java.awt.Image;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.image.BufferedImage;
import java.awt.image.RescaleOp;
import java.io.File;
import java.io.IOException;
import javax.imageio.ImageIO;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
public class ImageBrightenFrame extends JFrame {
    private BufferedImage image;// 用于调整亮度的缓冲图像对象
    private BufferedImage oldImage;// 用于存放调整亮度之前的原缓冲图像对象
    private ImageBrightenPanel imageBrightenPanel = new ImageBrightenPanel();
    
    public static void main(String args[]) {
        ImageBrightenFrame frame = new ImageBrightenFrame();
        frame.setVisible(true);
    }
    
    public ImageBrightenFrame() {
        super();
        setBounds(100, 100, 357, 276);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setTitle("调整图片的亮度");
        Image img = null;
        try {
            img = ImageIO.read(new File("src/img/image.jpg"));  // 创建图像对象
        } catch (IOException e) {
            e.printStackTrace();
        }
        image = new BufferedImage(img.getWidth(this), img.getHeight(this),
        BufferedImage.TYPE_INT_RGB);// 创建缓冲图像对象
        image.getGraphics().drawImage(img, 0, 0, null);// 在缓冲图像对象上绘制图像
        oldImage = image;// 存储原来的图像对象，用于以后的恢复操作
        getContentPane().add(imageBrightenPanel, BorderLayout.CENTER);
        
        final JPanel panel = new JPanel();
        getContentPane().add(panel, BorderLayout.SOUTH);

        final JButton button = new JButton();
        button.addActionListener(new ActionListener() {
            public void actionPerformed(final ActionEvent e) {
                float a = 1.0f;// 定义缩放因子
                float b = 5.0f;// 定义偏移量
                RescaleOp op = new RescaleOp(a,b,null);// 创建具有指定缩放因子和偏移量的 RescaleOp对象
                image = op.filter(image, null);// 对源图像中的数据进行逐像素重缩放，达到变亮的效果
                repaint();// 重新绘制图像
            }
        });
        button.setText("变    亮");
        panel.add(button);

        final JButton button_3 = new JButton();
        button_3.addActionListener(new ActionListener() {
            public void actionPerformed(final ActionEvent e) {
                float a = 1.0f;// 定义缩放因子
                float b = -5.0f;// 定义偏移量
                RescaleOp op = new RescaleOp(a,b,null);// 创建具有指定缩放因子和偏移量的 RescaleOp对象
                image = op.filter(image, null);// 对源图像中的数据进行逐像素重缩放，达到变暗的效果
                repaint();// 重新绘制图像
            }
        });
        button_3.setText("变    暗");
        panel.add(button_3);

        final JButton button_2 = new JButton();
        button_2.addActionListener(new ActionListener() {
            public void actionPerformed(final ActionEvent e) {
                image = oldImage;  // 获得变亮前的图像
                imageBrightenPanel.repaint();// 重新绘制原图像，即恢复为变亮前的图像
            }
        });
        button_2.setText("恢    复");
        panel.add(button_2);

        final JButton button_1 = new JButton();
        button_1.addActionListener(new ActionListener() {
            public void actionPerformed(final ActionEvent e) {
                System.exit(0);
            }
        });
        button_1.setText("退    出");
        panel.add(button_1);
  }
    
    class ImageBrightenPanel extends JPanel {
        public void paint(Graphics g) {
            if (image != null) {
                g.drawImage(image, 0, 0, null);  // 将缓冲图像对象绘制到面板上
            }
        }
    }
}

```
>>**补充说明：这里的`RescaleOp`类可以调整色数，其原理是每一个样本值乘以一个缩放因子然后加上偏移量就是缩放的数，如果要变亮的话就将偏移量为正，反之为负，这里将缩放因子设置为1.0f是因为不想那么快速的变亮，如果你设置的大一点，就会很快变得很亮，反之亦然**

## 转换彩色图片为灰色图片
>主要使用`ColorConvertOp`类，其构造函数如下
>`public ColorConvertOp(ColorSpace src,ColorSpace dst,RenderingHints hints)`:src是原颜色空间对象，dst是目标颜色空间对象，hints是用于控制颜色转换的RenderingHints对象，可以为null
>使用`ColorConvertOp`类中的`filter`方法将彩色图像转换成灰色图像，定义如下：
>`public final BufferedImage filter(BufferedImage src,BufferedImage dst)`:scr要过滤的对象，dst目标空间对象

```java
package com.zzk;

import java.awt.BorderLayout;
import java.awt.Graphics;
import java.awt.Image;
import java.awt.color.ColorSpace;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.image.BufferedImage;
import java.awt.image.ColorConvertOp;
import java.io.File;
import java.io.IOException;
import javax.imageio.ImageIO;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
public class MultiColorToGrayFrame extends JFrame {
    private BufferedImage image;
    private ColorToGrayPanel colorToGrayPanel = new ColorToGrayPanel();
    
    public static void main(String args[]) {
        MultiColorToGrayFrame frame = new MultiColorToGrayFrame();
        frame.setVisible(true);
    }
    
    public MultiColorToGrayFrame() {
        super();
        setBounds(100, 100, 357, 276);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setTitle("彩色图像转换为灰度");
        Image img = null;
        try {
            img = ImageIO.read(new File("src/img/image.jpg"));  // 创建图像对象
        } catch (IOException e) {
            e.printStackTrace();
        }
        image = new BufferedImage(img.getWidth(this), img.getHeight(this),
                BufferedImage.TYPE_INT_RGB);// 创建缓冲图像对象
        image.getGraphics().drawImage(img, 0, 0, null);// 在缓冲图像对象上绘制图像
        
        getContentPane().add(colorToGrayPanel, BorderLayout.CENTER);
        
        final JPanel panel = new JPanel();
        getContentPane().add(panel, BorderLayout.SOUTH);
        
        final JButton button = new JButton();
        button.addActionListener(new ActionListener() {
            public void actionPerformed(final ActionEvent e) {
                ColorSpace colorSpace1 = ColorSpace.getInstance(ColorSpace.CS_GRAY);// 创建内置线性为灰度的颜色空间
                ColorSpace colorSpace2 = ColorSpace.getInstance(ColorSpace.CS_LINEAR_RGB);// 创建内置线性为 RGB的颜色空间
                ColorConvertOp op = new ColorConvertOp(colorSpace1,colorSpace2,
                        null);// 创建进行颜色转换的对象
                image = op.filter(image, null);// 对缓冲图像进行颜色转换
            repaint();// 重新绘制图像
        }
        });
        button.setText("转换为灰度");
        panel.add(button);

        final JButton button_1 = new JButton();
        button_1.addActionListener(new ActionListener() {
            public void actionPerformed(final ActionEvent e) {
                System.exit(0);
            }
        });
        button_1.setText("退    出");
        panel.add(button_1);
    }
    
    class ColorToGrayPanel extends JPanel {
        public void paint(Graphics g) {
            if (image != null) {
                g.drawImage(image, 0, 0, null);  // 将缓冲图像对象绘制到面板上
            }
        }
    }
}
```
>**补充说明：这里的`image.getGraphics().drawImage(img, 0, 0, null)`可以删除的，因为这里Graphics类中的drawImage可以直接绘制BufferedImage类型的缓冲图像，下面会给出一段代码做个示范**

```java
import javax.imageio.ImageIO;
import javax.swing.*;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.IOException;

/**
 * Created by Chenjiabing on 2017/4/5.
 */
public class demo  extends JFrame{

    private BufferedImage image=null;
    private Graphics2D graphics2D=null;
    private draw my_draw=new draw();
    public static void main(String args[])
    {
        demo my=new demo();
        my.setVisible(true);
    }

    public demo()
    {
        this.setBounds(100,100,1000,1000);
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
            try{
                image= ImageIO.read(new File("src/img/image.jpg"));
                //graphics2D=image.createGraphics();
                //graphics2D.drawImage(image,0,0,null);
                // image.getGraphics().drawImage(image,0,0,null);

        }
        catch (IOException e)
        {
            e.printStackTrace();
            System.out.println("error");
        }
        this.getContentPane().add(my_draw);




    }

    class draw extends JPanel
    {
        public void paint(Graphics g)
        {
            g.drawImage(image,0,0,image.getWidth(),image.getHeight(),this);
        }
    }



}

```
## 总结：
>**从文件中读取图像的方法**
>>* URL imgUrl = CutImageFrame.class.getResource("/img/image.jpg");//得到的是URL
    img = Toolkit.getDefaultToolkit().getImage(imgUrl);  //得到的是Image对象，同样的想要得到BufferedImage对象可以进行转     化
>>* `Image img=ImageIo(new File("path"));`这里得到的是Image对象，如果想要得到BufferedImage对象，可以用BufferedImage的构造方法BufferedImage(int width,int height,)




















>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*