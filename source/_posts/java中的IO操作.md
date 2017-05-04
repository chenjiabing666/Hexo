---
title: java中的IO操作
date: 2017-03-25 13:52:53
categories: java学习
tags: java基础
---

# java中IO操作
## 读取文件中的内容
>#### 使用`Scanner`读取文本中的内容
>> 相信大家都知道`Scanner console=new Scanner(System.in)`是用来读取控制台上输入的内容，但是这里是用来读取文件的内容，原理是一样的，只是对象不同罢了，这里用到的是`File`对象，用来创建一个文件对象

```java
    Scanner input=new Scanner(new                         File("hello.txt"));//创建一个对象input
    while(input.hasNextLine()) //这里用来判断是否还有内容，    以免读到最后发生错误
    {
    String content=input.nextLine();
    System.out.println(content);
    }
```

>>这里顺便补充一下`Scannner`中的几个函数：
>>>1. `nextLine()`:读取一行的内容，包括空格，换行
>>>2. `nextInt()`:读取一个整型内容
>>>3. `nexDouble()`:读取一个双精度的浮点数
>>>4. `next()`:读取下一个内容，无论什么类型，其中遇到空格和换行默认是一个标记（即是跳过）和`nextLine()`类似
>>>5. `hasNext()`:用来判断文件中的还有下一个内容，无论什么类型的
>>>6. `hasNextInt()`
>>>7. `hasNextDouble()`://相似，不在赘述

>### 使用`FileReader`读取
>>用来读取字符文件的便捷类。此类的构造方法假定默认字符编码和默认字节缓冲区大小都是适当的。要自己指定这些值，可以先在 `FileInputStream `上构造一个 `InputStreamReader`。
FileReader 用于读取字符流。要读取原始字节流，请考虑使用 FileInputStream。
    
    //这里使用new File创建一个对象，同样的也可以直接将文件的绝对路径传入
    FileReader file=new FileReader(new File("hello.txt"));
    while(file.ready())   //用来判断是否还有字符可读
            {
            int content=file.read();   //这里的read是读取将单个字符 返回的是int，即是ascii码,这里官方文档说返回的是读取的字符数，但是我实验了一下返回的ascii码
            System.out.println((char)content);  //所以要将ascii码转换成字符
            }
            file.close();
            
>>>常用的几个方法：
>>>>1. `read()`: return int 上面介绍过
>>>>2. `read(char[] cbuf,int int length)`:将内容读入到一个`char`类型的数组，`length`是读取的字符数，`offest`是偏移量

>### 使用`BufferedReader`的类实现高效的读取文件

```java
    //传入一个reader创建一个对象
            BufferedReader file= new BufferedReader(new FileReader("hello.txt"));  
            System.out.println(file.skip(3));//实现将指针跳过3个字符
            System.out.println((char)file.read()); //read的方法，和FileReader中的read一样
            String line=file.readLine();   //读取一行
            System.out.println(line);
```
            
>>常用的方法：
>>>1. `readLine()`
>>>1. `read()`：如果到了末尾返回-1
>>>1. `read(char [],int off,int length)`:和FileReader中的一样
>>>1. `ready()`:判断是否还可以读取，一般和read配对使用
>>>1. `skip(long n)`:跳过的字符数
>>>1. `close()`

## 文件的写入
>### 用`FileWriter`写入文件

```java
    /*创建将对象f传入FileWriter,其中Filewriter有两个参数，第一个是File对象后者是一个String(即是文件的路径），第二个参数是boolean类型的，表示是否在文件的末尾追加内容，默认的是false表示不用在末尾追加，如果想要在末尾追加要写入另外一个参数true,当然这里可以用更加简洁的方式创建：FileWriter file=new FileWriter("hello.txt",false);
    */
    FileWriter file=new FileWriter(f,true);
    file.write("chenjiabing");//写入函数write
    file.close();  //最后必须关闭文件的输入流，否则写入将会失败，这里不想c和c++
```

>其中Filewriter中的方法还有
>>1. `flush`：刷新缓存流
>>1. `close`
>>1. `append()`:当前的领会的就是写入数组:`append(Arrays.toString(list))`;
>>1. `getEncoding()`:返回此流使用的字符编码

### 用`PrintStream`写入文件
>这里同样的是和`System.out.println()`一样的原理，`System.out.println`只是内部实现了`PrintStream`，这里是用来将指定的内容写入到文件中而已

```java  
    PrintStream output=new PrintStream(new     File("hello.txt"));
    //创建一个写入的对象output
    output.print("flan");
    output.println("vmlkfamla");
    output.println("vmslfkmadvmfs;dm");
```

>## 这里是用`BufferedWriter`类写入文件(一个高效的写入方式)
>>### 简单介绍
>>>将文本写入字符输出流，缓冲各个字符，从而提供单个字符、数组和字符串的高效写入。
可以指定缓冲区的大小，或者接受默认的大小。在大多数情况下，默认值就足够大了。
该类提供了 `newLine()` 方法，它使用平台自己的行分隔符概念，此概念由系统属性 line.separator 定义。并非所有平台都使用新行符 ('\n') 来终止各行。因此调用此方法来终止每个输出行要优于直接写入新行符。
通常 `Writer` 将其输出立即发送到底层字符或字节流。除非要求提示输出，否则建议用 `BufferedWriter` 包装所有其 `write()` 操作可能开销很高的 `Writer`（如 `FileWriters` 和 `OutputStreamWriters`）。例如，

    PrintWriter out= new PrintWriter(new BufferedWriter(new FileWriter("foo.out")));
将缓冲 `PrintWriter `对文件的输出。如果没有缓冲，则每次调用 `print()` 方法会导致将字符转换为字节，然后立即入到文件，而这是极其低效的。
>>### 例子

```java
    BufferedWriter input=new BufferedWriter(new FileWriter("hello.txt"));
            input.write("这是一个文件读入的方法");
            input.newLine();
            input.write("一个高效的方法");
            input.close();
```

>>### 其他的方法
>>>1. `close()`
>>> 1. `flush()`
>>> 1. `newLine()`:写入一个换行，因为每一个操作系统上的换行符可能不一样，不能系统的都用"\n"表示
>>>1. `write()`
>>>>详情参见`API`

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*




