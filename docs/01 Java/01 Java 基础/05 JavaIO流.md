# JavaIO流

> 参考Bilibili尚硅谷课程：[尚硅谷_Java IO流(宋红康主讲)](https://www.bilibili.com/video/BV1Kb411W75N?p=577)

## 一、File类

`java.io.File类`：文件和文件目录路径的抽象表示形式，与平台无关

### 1  File类的理解

File类的一个对象，代表一个文件或一个文件目录(俗称：文件夹)。

File类中涉及到关于文件或文件目录的创建、删除、重命名、修改时间、文件大小等方法，并未涉及到写入或读取文件内容的操作。如果需要读取或写入文件内容，必须使用IO流来完成。

想要在Java程序中表示一个真实存在的文件或目录，那么必须有一个File对象，但是Java程序中的一个File对象，可能没有一个真实存在的文件或目录。

File类的对象常会**作为参数**传递到流的构造器中，指明读取或写入的"终点"。

### 2 File的实例化

#### 2.1 常用构造器

-  `File(String pathname)`：以pathname为路径创建File对象，可以是==绝对路径或者相对路径==，如果pathname是相对路径，则默认的当前路径在系统属性user.dir中存储。
  - 绝对路径：是一个固定的路径，从盘符开始
  - 相对路径：是相对于某个位置开始
- `File(String parent, String child)`：以parent为父路径，child为子路径创建File对象。
- `File(File parent, String child)`：根据一个父File对象和子文件路径创建File对象

**IDEA中**：

- 如果使用==JUnit中的单元测试方法测试，相对路径即为当前Module下==。
- 如果使用==main()测试，相对路径即为当前的Project下==。

**代码示例**：

```java
import org.junit.Test;

import java.io.File;

public class FileTest {
    @Test
    public void test1(){
        // 1.File(String pathname)
        // 相对路径:JUnit中的单元测试方法测试，相对路径即为当前Module下
        File file1 = new File("hello.txt");
        // 绝对路径
        File file2 = new File("D:\\Documents\\IdeaProjects" +
                "\\JavaBasic\\chapter13 IO\\hello.txt");
        // 此时还不需要真的存在文件
        System.out.println(file1);// hello.txt
        System.out.println(file2);// D:\Documents\IdeaProjects\JavaBasic\chapter13 IO\hello.txt


        // 2. File(String parent, String child)
        File file3 = new File("D:\\Documents\\IdeaProjects" +
                "\\JavaBasic","chapter13 IO");
        //D:\Documents\IdeaProjects\JavaBasic\chapter13 IO
        System.out.println(file3); 

        // 3. File(File parent, String child)
        File file4 = new File(file3, "hi.txt");
        // D:\Documents\IdeaProjects\JavaBasic\chapter13 IO\hi.txt
        System.out.println(file4);
    }
}
```



#### 2.2 路径分类

- 相对路径：相较于某个路径下，指明的路径。
- 绝对路径：包含盘符在内的文件或文件目录的路径。

**说明**：

- **IDEA中**：
  - 如果使用==JUnit中的单元测试方法测试，相对路径即为当前Module下==。
  - 如果使用==main()测试，相对路径即为当前的Project下==。
- Eclipse中：
  - 不管使用单元测试方法还是使用main()测试，相对路径都是当前的Project下。



#### 2.3 路径分隔符

- windows 和 DOS系统默认使用`\`来表示

- UNIX 和 URL使用`/`来表示

- Java程序支持跨平台运行，因此路径分隔符要慎用。

- 为了解决这个隐患，File类提供了一个常量： public static final String `separator`。

  - 根据操作系统，动态的提供分隔符：`File.separator`

  举例：

  ```java
  //windows和DOS系统
  File file1 = new File("E:\\io\\test.txt");
  //UNIX和URL
  File file = new File("E:/io/test.txt");
  //java提供的常量
  File file = new File("E:"+File.separator+"io"+File.separator+"test.txt");
  ```



### 3. File类常用方法

#### 3.1 File类的获取功能

- public String `getAbsolutePath()`：获取绝对路径
- public String `getPath()` ：获取路径
- public String `getName()` ：获取名称
- public String getParent()：获取上层文件目录路径。若无，返回null
- public long length() ：获取文件长度（即：字节数）。不能获取目录的长度。
- public long lastModified() ：获取最后一次的修改时间，**毫秒值**，可以使用==new Date(file.lastModified())==来返回具体的日期

如下的两个方法适用于文件目录：

- public String[] `list()` ：获取**指定目录**下的所有文件或者文件目录的名称数组
- public File[] `listFiles()` ：获取指定目录下的所有文件或者文件目录的File数组

**代码示例：**

```java
import java.io.File;
import java.util.Date;

public class FileTest {
    @Test
    public void test2(){
        File file1 = new File("hello.txt");

        // 1. String getAbsolutePath()：获取绝对路径
        // D:\Documents\IdeaProjects\JavaBasic\chapter13 IO\hello.txt
        System.out.println(file1.getAbsolutePath());

        // 2.String getPath():获取路径
        System.out.println(file1.getPath());// hello.txt

        // 3.String getName():获取名称
        System.out.println(file1.getName());// hello.txt

        // 4.String getParent():获取上层文件目录路径。若无，返回null
        System.out.println(file1.getParent()); // null

        // 5.long length():获取文件长度（即,字节数）。
        System.out.println(file1.length());// 0

        // 6.long lastModified()
        // 获取最后一次的修改时间，毫秒值
        System.out.println(new Date(file1.lastModified()));// Tue Dec 22 20:33:47 CST 2020

		/****** 测试文件目录方法 *********/
        File file2 = new File("D:\\Documents\\IdeaProjects" +
                "\\JavaBasic\\chapter13 IO");
        
        // 7. String[] list()
        // 获取指定目录下的所有文件或者文件目录的名称数组
        String[] list = file2.list();
        for (String s : list) {
            // chapter13 IO.iml hello.txt src(文件夹)
            System.out.println(s);
        }

        // 8.File[] listFiles()
        // 获取指定目录下的所有文件或者文件目录的File数组
        File[] files = file2.listFiles();
        for (File file : files) {
            // D:\Documents\IdeaProjects\JavaBasic\chapter13 IO\chapter13 IO.iml
            // D:\Documents\IdeaProjects\JavaBasic\chapter13 IO\hello.txt
            // D:\Documents\IdeaProjects\JavaBasic\chapter13 IO\src
            System.out.println(file);
        }
    }
```



#### 3.2 File类的重命名功能

- public boolean `renameTo(File dest)`：把文件重命名为指定的文件路径

注意：file1.renameTo(file2)为例：要想保证返回true，需要==file1在硬盘中是存在的，且file2不能在硬盘中存在==。

**代码示例：**

```java
import java.io.File;
import java.util.Date;

public class FileTest {
    @Test
    public void test3(){

        // hello.txt 存在
        File file1 = new File("hello.txt");
        // hi.txt 不存在
        File file2 = new File("hi.txt");

        // 9.src.renameTo(File dest):把file1文件重命名为指定file2的文件路径
        // 要想保证返回true，dest不能在硬盘中存在,且需要src在硬盘中是存在的
        boolean b = file1.renameTo(file2);
        System.out.println(b); // true
    }
}
```



#### 3.3 File类的判断功能

- public boolean `isDirectory()`：判断是否是文件目录
- public boolean `isFile()` ：判断是否是文件
- public boolean `exists()` ：判断是否存在
- public boolean canRead() ：判断是否可读
- public boolean canWrite() ：判断是否可写
- public boolean isHidden() ：判断是否隐藏

注意：如果文件或目录不存在，则都返回false.

**代码示例：**

```java
public class FileTest {
    @Test
    public void test4(){
        File file1 = new File("hello.txt");
        // 10.boolean isDirectory():判断是否是文件目录
        System.out.println(file1.isDirectory());//false

        // 11.boolean isFile():判断是否是文件
        System.out.println(file1.isFile());// true

        // 12.boolean exists():判断是否存在
        System.out.println(file1.exists());//true

        // 13.boolean canRead():判断是否可读
        System.out.println(file1.canRead());//true

        // 14.boolean canWrite():判断是否可写
        System.out.println(file1.canWrite());//true

        // 15.boolean isHidden():判断是否隐藏
        System.out.println(file1.isHidden());//false
    }
}
```



#### 3.4 Flie类的创建功能

创建硬盘中对应的文件或文件目录

- public boolean `createNewFile()` ：创建文件。若文件存在，则不创建，返回false
- public boolean `mkdir()` ：创建文件目录。如果此文件目录存在，就不创建了。如果此文件目录的**上层目录不存在，也不创建**。
- public boolean `mkdirs()` ：创建文件目录。如果此文件目录存在，就不创建了。如果**上层文件目录不存在，一并创建**

**代码示例：**

```java
public class FileTest {
    @Test
    public void test5() throws IOException {
        File file = new File("hi.txt");

        if (!(file.exists())) {
            // 16.boolean createNewFile()
            // 创建文件。若文件存在，则不创建，返回false
            file.createNewFile();
            System.out.println("hi.txt创建成功");
        }else {
            // 17 boolean delete()：删除文件或者文件夹
            file.delete();
            System.out.println("hi.txt删除成功");
        }

        // 文件目录的创建
        File file1 = new File("D:\\Documents\\IdeaProjects" +
                "\\JavaBasic\\chapter13 IO\\test0\\test1");

        // 18.boolean mkdir()
        // 创建文件目录。如果此文件目录存在，就不创建了。
        // 如果此文件目录的上层目录不存在，也不创建。
        boolean mkdir1 = file1.mkdir();
        if (mkdir1) {
            System.out.println("目录0/1创建成功");
        }else {
            System.out.println("目录0/1创建失败");// 目录0/1创建失败
        }

        File file2 = new File("D:\\Documents\\IdeaProjects" +
                "\\JavaBasic\\chapter13 IO\\test0\\test2");

        // 19. mkdirs()
        // 创建文件目录。如果此文件目录存在，就不创建了。
        // 如果上层文件目录不存在，一并创建.
        boolean mkdir2 = file2.mkdirs();
        if(mkdir2){
            System.out.println("目录0/2创建成功");//目录0/2创建成功
        }
        // 要想删除成功，文件目录下不能有子目录或文件
        File test = new File("test0");// test0/tes1 test2
        System.out.println(test.delete());// false
    }
}
```



#### 3.5 File类的删除功能

删除磁盘中的文件或文件目录

- public boolean `delete()`：删除文件或者文件夹。要想删除成功，文件目录下不能有子目录或文件。

> 注意：删除注意事项：Java中的删除不走回收站。



### 4. 内存解析

![image-20200430231445700](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200430231447.png)



### 5. 小练习

1、利用File构造器，new 一个文件目录file 

- 在其中创建多个文件和目录 
- 编写方法，实现删除 file 中指定文件的操作

```java
public class Examine {
    @Test
    public void test1() throws IOException {
        // 先指定创建目录的路径
        File dirTest = new File("test");
        // 创建目录
        dirTest.mkdir();
        // File(String parent, String child)
        File file = new File(dirTest, "hei.txt");
        // 创建文件
        boolean b = file.createNewFile();
        
        // 创建与file同目录下的文件
        File file1 = new File(file.getParent(), "hi.txt");
        // 创建新文件
        file1.createNewFile();
        
        if (b) {
            System.out.println("创建成功");
        }
        if (delete(file)){
            System.out.println("删除成功");
        }
    }
    public boolean delete(File file){
        return file.delete();
    }
}
```

2、判断指定目录下是否有后缀名为jpg的文件，如果有，就输出该文件名称

- public String[] `list()` ：获取**指定目录**下的所有文件或者文件目录的**名称数组**

```java
import java.io.File;
import java.io.IOException;
public class Examine {
    @Test
    public void test2(){
        // 判断指定目录下是否有后缀名为jpg的文件
        File dir = new File("test");
        String[] fileList = dir.list();
        for (String s : fileList) {
            // 判断String 结尾是否为 .jpg
            if(s.endsWith(".jpg")){
                // 1.jpg 2.jpg
                System.out.println(s);
            }
        }
    }
}
```

- public File[] `listFiles()` ：获取指定目录下的所有文件或者文件目录的**File数组**

```java
import java.io.File;
import java.io.IOException;
public class Examine {
    @Test
    public void test2(){
        // 判断指定目录下是否有后缀名为jpg的文件
        File dir = new File("test");
        File[] files = dir.listFiles();
        for (File file : files) {
            // file.getName()之后才是String 类型
            if (file.getName().endsWith(".jpg")) {
                System.out.println(file.getName());
            }
        }
    }
}
```

File类 提供了两个文件过滤器方法

* public String[] `list(FilenameFilter filter)`
 * public File[] `listFiles(FileFilter filter)`

```java
import java.io.File;
import java.io.FilenameFilter;
import java.io.IOException;
public class Examine {
    @Test
    public void test2(){
        // 判断指定目录下是否有后缀名为jpg的文件
        File dir = new File("test");
        File[] files = dir.listFiles(new FilenameFilter() {
            @Override
            public boolean accept(File dir, String name) {
                return name.endsWith(".jpg");
            }
        });
        for (File file : files) {
            System.out.println(file.getName());
        }
    }
}
```

3、遍历指定目录所有文件名称，包括子文件目录中的文件。

- 拓展1：并计算指定目录占用空间的大小 
- 拓展2：删除指定文件目录及其下的所有文件

> 注意：main()测试，**相对路径即为当前的Project下**

```java
import java.io.File;

public class ListFileTest {
    public static void main(String[] args) {
        // 1.创建目录对象

        // main()测试，相对路径即为当前的Project下
        File file = new File("test");
        // 2.打印子目录
        printSubFileName(file);

        System.out.println("---打印完毕---");

        // 3.计算目录大小
        long size = getDirectorySize(file);
        System.out.println("空间大小为: " + size);

        // 4.删除文件目录
        deleteDirectory(file);
    }
    /**
     * 递归方法遍历所有目录下的文件
     *
     * @param dir
     * @return void
     */
    private static void printSubFileName(File dir) {
        if (dir.isDirectory()) {
            File[] files = dir.listFiles();
            for (File file : files) {
                if (file.isDirectory()) {
                    // 如果为文件目录，则递归调用自身
                    printSubFileName(file);
                } else {
                    System.out.println(file.getName());
                }
            }
        }else {
            System.out.println(dir.getAbsolutePath());
        }
    }
    /**
     * 求指定目录所在空间的大小,求任意一个目录的总大小
     *      如果file是文件，那么直接返回file.length()
     *      file是目录，把它的下一级的所有大小加起来就是它的总大小
     * @param    file
     * @return   long
     */
    public static long getDirectorySize(File file){
        long size =0;
        if(file.isFile()){
            size += file.length();
        }else {
            // 获取file的下一级
            File[] files = file.listFiles();
            for (File f : files) {
                size += getDirectorySize(f);
            }
        }
        return size;
    }
    /**
     * 拓展2：删除指定的目录
     *      如果file是文件，直接delete
     *      如果file是目录，先把它的下一级干掉，然后删除自己
     * @param    file
     * @return   void
     */
    public  static  void  deleteDirectory(File file){
        if (file.isDirectory()) {
            File[] files = file.listFiles();
            // 递归调用删除file下一级
            for (File f : files) {
                deleteDirectory(f);
            }
        }else {
            // 删除文件
            file.delete();
        }
        
    }
}
```



## 二、IO流概述

### 1. 简述

==IO是Input / Output==的缩写，I/O技术是非常实用的技术，用于处理设备之间的数据传输。如读/写文件，网络通讯等。

Java程序中，对于数据的输入输出操作以“==流(stream)==”的方式进行。

- Java.IO包下提供了各种“流”类和接口，用以获取不同种类的数据，并通过**标准方法**输入或输出数据。



### 2. 流的分类

**操作数据单位：字节流、字符流**

- 对于文本文件(.txt，.java，.c，.cpp)，使用**字符流**处理，16bit
- 对于非文本文件(.jpg，.mp3，.mp4，.avi，.doc，.ppt，...)，使用**字节流**处理，8bit

**数据的流向：输入流、输出流**

==程序是内，外部是外。==

- 输入input：读取外部数据（磁盘、光盘等存储设备的数据）到程序（内存）中。
- 输出output：将程序（内存）数据输出到磁盘、光盘等存储设备中。

| 抽象基类 | 字节流       | 字符流 |
| -------- | ------------ | ------ |
| 输入流   | InputStream  | Reader |
| 输出流   | OutputStream | Writer |

**流的角色：节点流、处理流**

节点流：直接从数据源或目的地读写数据。

![image-20200502092206789](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200502092207.png)

==处理流==：不直接连接到数据源或目的地，而是“连接”在已存在的流（节点流或处理流）之上，通过对数据的处理为程序提供更为强大的读写功能。

![image-20200502092221437](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200502092222.png)

**图示：**

![image-20200502091548532](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200502091549.png)



### 3. IO流的体系分类

Java的lO流共涉及40多个类，实际上非常规则，都是从如下==4个抽象基类派生==的。

- 由这四个类派生出来的子类名称都是以其父类名作为子类名后缀。

| ==抽象基类== | 字节流       | 字符流 |
| ------------ | ------------ | ------ |
| 输入流       | InputStream  | Reader |
| 输出流       | OutputStream | Writer |

#### 3.1 总体分类 

![image-20200502091615386](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200502091616.png)

> 红框为抽象基类，蓝框为常用IO流

#### 3.2 常用的几个IO流结构

| 抽象基类    | 节点流（或文件流）                           | 缓冲流（处理流的一种）                                     |
| ----------- | -------------------------------------------- | ---------------------------------------------------------- |
| InputStream | FileInputStream (read(byte[] buffer))        | BufferedInputStream (read(byte[] buffer))                  |
| OutputSteam | FileOutputStream (write(byte[] buffer,0,len) | BufferedOutputStream (write(byte[] buffer,0,len) / flush() |
| Reader      | FileReader (read(char[] cbuf))               | BufferedReader (read(char[] cbuf) / readLine())            |
| Writer      | FileWriter (write(char[] cbuf,0,len)         | BufferedWriter (write(char[] cbuf,0,len) / flush()         |



#### 3.3 对抽象基类的说明

Java的lO流共涉及40多个类，实际上非常规则，都是从如下4个抽象基类派生的。由这四个类派生出来的子类名称都是以其**父类名作为子类名后缀**

| 抽象基类 | 字节流      | 字符流 |
| -------- | ----------- | ------ |
| 输入流   | InputSteam  | Reader |
| 输出流   | OutputSteam | Writer |

##### 3.3.1 InputSteam & Reader

InputStream 和 Reader是所有输入流的基类。

- FileInputStream从文件系统中的某个文件中获得输入字节。FileInputStream用于读取非文本数据之类的原始字节流。
- 要读取字符流，需要使用 FileReader。

程序中打开的文件IO资源不属于内存里的资源，垃圾回收机制无法回收该资源，所以==应该显式关闭文件IO资源==。

**InputStream**（典型实现：FileInputStream）

- int `read()`：从输入流中读取数据的下一个==字节==。
  - 返回0到255范围内的int字节值。如果因为已经到达流末尾而没有可用的字节，则返回值-1。
- `int read(byte[] b)`：从此输入流中将 最多**b.length个字节**的数据读入==byte数组==中。
  - 如果因为已经到达流末尾而没有可用的字节，则返回值-1。
  - 否则以整数形式返回实际读取的字节数。
- `int read(byte[] b, int off, int len)`：将输入流中最多**len个数据字节** 读入byte数组。
  - 尝试读取len个字节，但读取的字节也可能小于该值。
  - 以整数形式返回实际读取的字节数。如果因为流位于文件末尾而没有可用的字节，则返回值-1。
- public void close() throws IOException：**关闭**此输入流并释放与该流关联的所有系统资源。

**Reader**（典型实现：FileReader）

- int read()：读取单个==字符==。
  - 作为整数读取的字符，范围在0到65535之间（0x00-0xffff）(**2个字节**的 Unicode码)，如果已到达流的末尾，则返回-1。
- int read（char[] cbuf）：将**字符读入数组**。
  - 如果已到达流的末尾，则返回-1。否则返回本次读取的字符数。
- int read（char[] cbuf,int off,int len）：将字符读入数组的某一部分。存到数组cbuf中，从off处开始存储，**最多读len个字符**。
  - 如果已到达流的末尾，则返回-1。否则返回本次读取的字符数。
- public void close() throws IOException：**关闭**此输入流并释放与该流关联的所有系统资源。

##### 3.3.2 OutputSteam & Writer

OutputStream和Writer也非常相似；FileOutputStream从文件系统中的某个文件中获得输出==字节==。FileOutputstream用于写出非文本数据之类的原始字节流。要写出==字符==流，需要使用 FileWriter

两者的基本方法：

- void write(int b/int c);
- void write(byte[] b/char[] cbuf);
- void write(byte[] b /char[] buff,int off,int len);
- void flush();
- void close();
  - 需要先刷新，再关闭此流

因为字符流直接以字符作为操作单位，所以 Writer可以用字符串来替换字符数组，即以 String对象作为参数
- void write(String str);
- void write(String str,int off,int len);



**OutputStream:**

- void write(int b)：将指定的**字节**写入此输出流。 
  - write的常规协定是：向输出流写入一个字节。要写入的字节是参数b的八个低位。b的24个高位将被忽略。即写入0~255范围的
- void `write（byte[] b）`：等同wite(b,0,b.length)，将 b.length 个字节从指定的byte数组写入此输出流。
- void `write（byte[] b,int off,int len）`：将指定byte数组中从偏移量 off 开始的 len 个字节写入此输出流。
- public void flush() throws IOException：刷新此输出流并强制写出所有缓冲的输出字节，调用此方法指示应将这些字节**立即写入**它们预期的目标。
- public void `close()` throws IOException：关闭此输岀流并释放与该流关联的所有系统资源。

**Writer**

因为字符流直接以字符作为操作单位，所以 Writer可以用字符串来替换字符数组，即可以用 String对象作为参数。

- void write(int c)：写入单个**字符**。
  - 要写入的字符包含在给定整数值的16个低位中，16高位被忽略。即写入0到65535之间的 Unicode码。
- void write(char[] cbuf)：写入**字符数组**
- void `write(char[] cbuf,int off,int len)`：写入字符数组的某一部分。从off开始，写入len个字符
- void write(String str)：写入**字符串**。
- void write(String str,int off,int len)：写入字符串的某一部分。
- void flush()：刷新该流的缓冲，则立即将它们写入预期目标。
- public void close() throws IOException：关闭此输出流并释放与该流关联的所有系统资源



### 4. 输入、输出标准化过程

#### 4.1 输入过程

1、创建File类的对象，指明读取的数据的来源。（要求此文件一定要存在）

2、创建相应的输入流，将File类的对象作为参数，传入流的构造器中

3、具体的读入过程：创建相应的byte[] 或 char[]。

4、关闭流资源

说明：程序中出现的异常需要使用==try-catch-finally==处理。



#### 4.2 输出过程

1、创建File类的对象，指明写出的数据的位置。（不要求此文件一定要存在）

2、创建相应的输出流，将File类的对象作为参数，传入流的构造器中

3、 具体的写出过程：write(char[] / byte[] buffer,0,len)

4、关闭流资源

说明：程序中出现的异常需要使用try-catch-finally处理。



## 三、节点流（文件流）

### 1   FileReader 和 FileWriter 

文件==字符流== FileReader 和 FileWriter  的使用。

#### 1.1 文件输入

从文件中读取到内存（程序）中

**步骤：**

1. 建立一个流对象，将已存在的一个文件加载进流：
   - FileReader fr = new FileReader(new File("Test. txt"));
2. 创建一个临时存放数据的数组 
   - char[] ch = new char[1024];
3. 调用流对象的读取方法将流中的数据**读入**到字符数组 ch中。 
   - fr.read(ch);
4. 关闭资源。 
   - fr.close();

**代码示例：**

测试一

```java
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;

public class FileReaderWriterTest {
    /**
     * 从文件中读取到内存（程序）中
     *
     * @param
     * @return void
     */
    @Test
    public void testFileReader() throws IOException {
        FileReader fileReader = null;
        try {
            // 1.File类的实例化
            File file = new File("hello.txt");// 相较于当前module下

            // 2.FileReader流的实例化,文件字符流
            fileReader = new FileReader(file);

            // 3.读入的操作
            // read()的理解：返回读入的一个字符。如果达到文件末尾，返回-1
            int data1 = fileReader.read();
            // 方式一
            while (data1 != -1) {
                System.out.print((char) data1 + "");
                //hello world
                data1 = fileReader.read();
            }
            
            // 方式二:语法上针对方式一的修改
            int data2;
            while ((data2 = fileReader.read()) != -1) {
                System.out.print((char) data2);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 4.资源的关闭
            if (fileReader != null) {
                fileReader.close();
            }
        }
    }
}
```



测试二：测试int read(char cbuf[])

```java
public class FileReaderWriterTest {
    // 对read()操作升级：使用read的重载方法
    @Test
    public void testFileReader2() throws IOException {
        FileReader fileReader = null;
        try {
            // 1.File类的实例化
            File file = new File("hello.txt");
            
            // 2.FileReader流的实例化
            fileReader = new FileReader(file);
            
            // 3.读入的操作
            char[] cbuf = new char[5];
            
            // int read(char cbuf[])
            // 返回每次读入cbuf数组中的字符的个数。如果达到文件末尾，返回-1
            int len;
            while ((len = fileReader.read(cbuf)) != -1) {
                // 方式一：错误写法
                /*for (int i = 0; i < cbuf.length; i++) {
                    //hello worldworl
                    System.out.print(cbuf[i]);
                }*/
                // 方式一：正确写法
                for (int i = 0; i < len; i++) {
                    //hello world
                    System.out.print(cbuf[i]);
                }
                
                // 方式二
                // 错误写法:对应着方式一的错误写法
                /*String str0 = new String(cbuf);
                // hello worldworl
                System.out.print(str0);*/
                
                // 正确写法
                String str = new String(cbuf,0,len);
                // hello world
                System.out.print(str);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fileReader !=null) {
                // 4.资源的关闭
                fileReader.close();
            }
        }
    }
}
```

**注意点：**

1. read()的理解：返回读入的一个字符。如果达到文件末尾，返回-1
2. 异常的处理：为了保证流资源一定可以执行关闭操作。需要使用==try-catch-finally处理==
3. 读入的文件一定要存在，否则就会报==FileNotFoundException==。



#### 1.2 文件的输出

从内存（程序）到硬盘文件中。输出操作，对应的File可以不存在的。并不会报异常

1. File对应的硬盘中的文件如果不存在，在输出的过程中，会自动创建此文件。
2. FiLe对应的硬盘中的文件如果存在:
   - 如果流使用的构造器是`Filewriter(file, false)` / `FiLewriter(file)`：对原有文件的**履盖**
   - 如果流使用的构造器是`Filewriter(file, true)`：不会对原有文件覆盖，而是在原有文件基础上进行**添加**

**步骤：**

1. 创建流对象，建立数据存放文件
   - FileWriter fw = new FileWriter(new File("Test.txt"))
2. 调用流对象的写入方法，将数据写入流
   - fw.write("HelloWord")
3. 关闭流资源，并将流中的数据清空到文件中。 
   - fw.close();

**代码示例：**

```java
public class FileReaderWriterTest {
    @Test
    public void testFileWriter() {
        FileWriter fileWriter = null;
        try {
            // 1.提供File类的对象，指明写出到的文件
            File file = new File("hi.txt");

            // 2.提供FileWriter的对象，用于数据的写出
            // FileWriter(File file, boolean append)
            fileWriter = new FileWriter(file, false);

            // 3.写出的操作
            fileWriter.write("I have a dream!\n");
            fileWriter.write("you need to have a dream!");
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 4.流资源的关闭
            if (fileWriter != null) {
                try {
                    // 4.流资源的关闭
                    fileWriter.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



#### 1.3 小练习

实现文本文件的复制操作

- ==不能使用字符流来处理图片等字节数据==

```java
public class FileReaderWriterTest {
    @Test
    public void testFileReaderFileWriter() {
        FileReader fileReader = null;
        FileWriter fileWriter = null;
        try {
            // 1.创建File类的对象，指明读入和写出的文件
            File srcFile = new File("hi.txt");
            File destFile = new File("hit.txt");

            // 不能使用字符流来处理图片等字节数据
            // File srcFile = new File("test.jpg");
            // File destFile = new File("test1.jpg");

            // 2.创建输入流和输出流的对象
            fileReader = new FileReader(srcFile);
            fileWriter = new FileWriter(destFile);

            // 3.数据的读入和写出操作
            char[] cbuf = new char[5];
            
            // 记录每次读入到cbuf数组中的字符的个数
            int len;
            while ((len = fileReader.read(cbuf)) != -1) {
                // 将cbuf 中的内容写出到 fileWriter
                fileWriter.write(cbuf, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 4.关闭流资源
            if (fileWriter != null) {
                try {
                    fileWriter.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (fileReader != null) {
                try {
                    fileReader.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

        }
    }
}
```



### 2.  FileInputSteam 和 FileOutputSteam

==文件字节流== FileInputSteam 和 FileOutputSteam 的操作与字符流操作类似，只是实例化对象操作和数据类型不同。

- 使用字节流 FileInputStream 处理文本文件，可能出现乱码。

**代码示例：**

```java
public class FileInputOutStream {
    @Test
    public void testFileInputStream(){
        FileInputStream fileInputStream = null;
        try {
            // 1. 造文件
            File file = new File("hello.txt");
            // 2.造输入流
            fileInputStream = new FileInputStream(file);
            // 3.读数据
            byte[] buffer = new byte[5];
            
            // 记录每次读取的字节的个数
            int len;
            while (((len = fileInputStream.read(buffer)) != -1)) {
                // String(byte bytes[], int offset, int length)
                String s = new String(buffer, 0, len);
                System.out.print(s);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 4.关闭资源
            if (fileInputStream != null) {
                try {
                    fileInputStream.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

**小练习**

实现图片文件复制操作

```java
@Test
public void testFileInputOutputStream()  {
    FileInputStream fis = null;
    FileOutputStream fos = null;
    try {
        // 1.创建File对象
        File srcFile = new File("test.jpg");
        File destFile = new File("test2.jpg");

        // 2.创建操流
        fis = new FileInputStream(srcFile);
        fos = new FileOutputStream(destFile);

        // 3.复制的过程
        byte[] buffer = new byte[5];
        int len;
      	// 从文件中读入字节流到 字节数组buffer
        while((len = fis.read(buffer)) != -1){
            // 将 buffer 写出到输出流中
            fos.write(buffer,0,len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        //4.关闭流
        if(fos != null){
            try {
                fos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if(fis != null){
            try {
                fis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
    }

}
```



### 3. 注意点

- 定义路径时，可以用“/”或“\\”。
- 输出操作，对应的File可以不存在的。并不会报异常。
- File对应的硬盘中的文件如果不存在，在输出的过程中，会自动创建此文件。
- File对应的硬盘中的文件如果存在：
  - 如果流使用的构造器是FileWriter(file,false) / FileWriter(file)：对原有文件的覆盖。
  - 如果流使用的构造器是FileWriter(file, true)：不会对原有文件覆盖，而是在原有文件基础上追加内容。
- 读取文件时，必须保证文件存在，否则会报异常。
- 对于==文本文件(.txt，.java，.c，.cpp)，使用字符流处理==
- 对于==非文本文件(.jpg，.mp3，.mp4，.avi，.doc，.ppt，...)，使用字节流处理==



## 四、缓冲流 

缓冲流可以提高流的读取、写入的速度；缓冲流涉及到的类：

- BufferedInputStream
- BufferedOutputStream
- BufferedReader
- BufferedWriter

缓冲流内部提供了一个缓冲区，默认情况下是**8kb**

![image-20200502110552555](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200502110553.png)

**处理流与节点流的对比图示**

![image-20200502111050587](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200502111051.png)



![image-20200502111057666](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200502111058.png)



### 缓冲流使用

当读取数据时，数据按块读入缓冲区，其后的读操作则直接访问缓冲区。

- 当使用 BufferedInputStream读取字节文件时，BufferedInputStream 会一次性从文件中==读取8192个(8Kb)==，存在缓冲区中，直到缓冲区装满了，才重新从文件中读取下一个8192个字节数组。

向流中写入字节时，不会直接写到文件，先写到缓冲区中直到缓冲区写满，  BufferedOutputStream 才会把缓冲区中的数据一次性写到文件里。

- 使用方法flush()可以**强制**将缓冲区的内容全部写入输出流。

关闭流的顺序和打开流的顺序相反。只要关闭最外层流即可，关闭最外层流也会相应关闭内层节点流。

- flush()方法的使用：手动将buffer中内容写入文件。
- 如果是带缓冲区的流对象的close()方法，不但会关闭流，还会在关闭流之前刷新缓冲区，关闭后不能再写出。

**代码示例：**

#### 3.1 BufferInputStream / BufferOutputStream

使用 BufferInputStream 和 BufferOutputStream 实现**非文本文件**的复制

- 字节类型文件

```java
import java.io.*;
import java.util.stream.Stream;

public class BufferedTest {
    // 实现非文本文件的复制
    @Test
    public void testBufferedStreamTest() {
        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;
        try {
            // 1.造文件
            File srcFile = new File("test1.jpg");
            File destFile = new File("test10.jpg");

            // 2.造流
            // 2.1 造节点流,也即文件流
            FileInputStream fis = new FileInputStream(srcFile);
            FileOutputStream fos = new FileOutputStream(destFile);
            
            // 2.2 造缓冲流，可以合并书写
            bis = new BufferedInputStream(fis);
            bos = new BufferedOutputStream(fos);

            // 3.文件读取、写出操作
            byte[] buffer = new byte[1024];
            int len;
            // 文件读入到 buffer
            while (((len = bis.read(buffer)) != -1)) {
                // 将 buffer 写出
                bos.write(buffer, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 4.关闭流
            // 要求:先关闭外层的流，再关闭内层的流
            // 说明:关闭外层流的同时，内层流也会自动的进行关闭。关于内层流的关闭，可以省略
            if (bos != null) {
                try {
                    bos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (bis != null) {
                try {
                    bis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



#### 3.2 BufferedReader / BufferedWriter

使用BufferedReader和BufferedWriter实现**文本文件**的复制

```java
import java.io.*;
import java.util.stream.Stream;

public class BufferedTest {
    // BufferedReader和BufferedWriter实现文本文件的复制
    @Test
    public void testBufferedReaderBufferedWriter(){
        BufferedReader br = null;
        BufferedWriter bw = null;

        try {
            // 1.创建文件
            File srcFile = new File("hi.txt");
            File destFile = new File("hit.txt");

            // 2.创建相应的缓冲字符流
            br = new BufferedReader(new FileReader(srcFile));
            bw = new BufferedWriter(new FileWriter(destFile));

            // 3.读写操作
            // 方式一：使用char[]数组
            char[] buffer = new char[1024];
            int len;
            while (((len = br.read(buffer)) != -1)) {
                bw.write(buffer,0,len);
            }
            // 方式二：使用String
            String data;
            while (((data = br.readLine()) != null)) {
                // data中不包含换行符，需要手动添加换行符
                // 方法1
                bw.write(data + "\n");

                // 方法2
                bw.write(data); //data中不包含换行符
                bw.newLine();   //提供换行的操作
            }

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 4.关闭资源
            if (bw != null) {
                try {
                    bw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (br != null) {
                try {
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



### 4. 小练习

#### 4.1 测试缓冲流和节点流文件复制速度

 **节点流**实现复制方法

```java
//指定路径下文件的复制
public void copyFile(String srcPath,String destPath){
    FileInputStream fis = null;
    FileOutputStream fos = null;
    try {
        //1.造文件
        File srcFile = new File(srcPath);
        File destFile = new File(destPath);

        //2.造流
        fis = new FileInputStream(srcFile);
        fos = new FileOutputStream(destFile);

        //3.复制的过程
        byte[] buffer = new byte[1024];
        int len;
        while((len = fis.read(buffer)) != -1){
            fos.write(buffer,0,len);
        }

    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if(fos != null){
            //4.关闭流
            try {
                fos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if(fis != null){
            try {
                fis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
    }
}
```

**缓冲流**实现复制操作

```java
//实现文件复制的方法
public void copyFileWithBuffered(String srcPath,String destPath){
    BufferedInputStream bis = null;
    BufferedOutputStream bos = null;

    try {
        //1.造文件
        File srcFile = new File(srcPath);
        File destFile = new File(destPath);
        //2.造流
        //2.1 造节点流
        FileInputStream fis = new FileInputStream((srcFile));
        FileOutputStream fos = new FileOutputStream(destFile);
        //2.2 造缓冲流
        bis = new BufferedInputStream(fis);
        bos = new BufferedOutputStream(fos);

        //3.复制的细节：读取、写入
        byte[] buffer = new byte[1024];
        int len;
        while((len = bis.read(buffer)) != -1){
            bos.write(buffer,0,len);
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        //4.资源关闭
        //要求：先关闭外层的流，再关闭内层的流
        if(bos != null){
            try {
                bos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
        if(bis != null){
            try {
                bis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
    }
}
```

测试二者速度

```java
public class BufferedTest {
    @Test
    public void testCopyFileWithBuffered(){

        String srcPath = "D:\\Desktop\\we\\1.MOV";
        String destPath1 = "D:\\Desktop\\we\\2.MOV";
        String destPath2 = "D:\\Desktop\\we\\3.MOV";

        // 1.缓冲流复制文件花费时间
        long start1 = System.currentTimeMillis();
        copyFileWithBuffered(srcPath,destPath1);
        long end1 = System.currentTimeMillis();
        // 108
        System.out.println("复制操作花费的时间为：" + (end1 - start1));

        // 2.字节流复制文件花费时间
        long start2 = System.currentTimeMillis();
        copyFile(srcPath,destPath2);
        long end2 = System.currentTimeMillis();
        // 288
        System.out.println("复制操作花费的时间为：" + (end2 - start2));
    }
}
```



#### 4.2 实现图片加密操作

加密操作

```java
int b = 0;
while((b = fis.read()) != -1){
	fos.write(b ^ 5);
}
```

- 将图片文件通过字节流读取到程序中
- 将图片的字节流逐一进行 异或`^` 操作
- 将处理后的图片字节流输出

```java
public class BufferedExer {
    // 文件的加密
    @Test
    public void test2() {
        FileInputStream fis = null;
        FileOutputStream fos = null;
        try {
            File srcFile = new File("hit.txt");
            File destFile = new File("hitEncrypt.txt");

            fis = new FileInputStream(srcFile);
            fos = new FileOutputStream(destFile);

            byte[] buffer = new byte[20];
            int len;
            while (((len = (fis.read(buffer))) != -1)) {
                // 对字节逐个进行异或
                for (int i = 0; i < len; i++) {
                    // 文件加密操作
                    buffer[i] = (byte) (buffer[i] ^ 5);
                }
                fos.write(buffer);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fos != null) {
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (fis != null) {
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

解密操作

- 将加密后图片文件通过字节流读取到程序中
- 将图片的字节流逐一进行 ^ 操作（原理：A^ B ^ B = A）
- 将处理后的图片字节流输出

```java
public class BufferedExer {
    // 文件的解密
    @Test
    public void test2() {
        FileInputStream fis = null;
        FileOutputStream fos = null;
        try {
            File srcFile = new File("hitEncrypt.txt");
            File destFile = new File("hitDecrypt.txt");

            fis = new FileInputStream(srcFile);
            fos = new FileOutputStream(destFile);

            byte[] buffer = new byte[20];
            int len;
            while (((len = (fis.read(buffer))) != -1)) {
                // 对字节逐个进行异或
                for (int i = 0; i < len; i++) {
                    // 文件解密操作
                    buffer[i] = (byte) (buffer[i] ^ 5);
                }
                fos.write(buffer);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fos != null) {
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (fis != null) {
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



#### 4.3 统计文本字符出现次数

实现思路：

1. 遍历文本每一个字符
2. 字符出现的次数存在Map中
3. 把map中的数据写入文件

```java
import java.io.*;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

public class WordCount {
    @Test
    public void testWordCount() {
        FileReader fr = null;
        BufferedWriter bw = null;
        try {
            // 1.创建Map集合
            HashMap<Character, Integer> map = new HashMap<>();
            // 2.遍历每一个字符,每一个字符出现的次数放到map中
            File file = new File("hi.txt");
            fr = new FileReader(file);
            int c = 0;
            while (((c = fr.read()) != -1)) {
                char ch = (char) c;
                if (map.containsKey(ch)) {
                    map.put(ch, map.get(ch) + 1);
                } else {
                    map.put(ch, 1);
                }
            }
            // 3.把map中数据存在文件count.txt
            bw = new BufferedWriter(new FileWriter("count.txt"));
            Set<Map.Entry<Character, Integer>> entries = map.entrySet();
            for (Map.Entry<Character, Integer> entry : entries) {
			   // bw.write(entry.getKey() + "=" + entry.getValue());
                switch (entry.getKey()) {
                    case ' ':
                        bw.write("空格=" + entry.getValue());
                        break;
                    //\t表示tab 键字符
                    case '\t':
                        bw.write("tab键=" + entry.getValue());
                        break;
                    case '\r':
                        bw.write("回车=" + entry.getValue());
                        break;
                    case '\n':
                        bw.write("换行=" + entry.getValue());
                        break;
                    default:
                        bw.write(entry.getKey() + "=" + entry.getValue());
                        break;
                }
                // 提供换行的操作
                bw.newLine();                
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 4.关流
            if (bw != null) {
                try {
                    bw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (fr != null) {
                try {
                    fr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



## 五、转换流

转换流属于字符流，提供了在字节流和字符流之间的转换。

转换流 有两个 Java API：
- `InputstreamReader`：将 **Inputstream**（字节）转换为**Reader** （字符）
- `OutputStreamWriter`：将 **Writer** （字符）转换为 **OutputStream**（字节）

字节流中的数据都是字符时，转成字符流操作更高效。

- 很多时候使用转换流来处理文件乱码问题。实现编码和解码的功能。



### 1.1 InputStreamReader

InputStreamReader：==将一个字节的输入流转换为字符的输入流== 

- **解码**：字节、字节数组  --> 字符数组、字符串

构造器：

- public InputStreamReader(InputStream in)
- public InputStreamReader(Inputstream in, String charsetName)：指定编码集

### 1.2 OutputStreamWriter

OutputStreamWriter：==将一个字符的输出流转换为字节的输出流== 

- **编码**：字符数组、字符串 --> 字节、字节数组

构造器：

- public OutputStreamWriter(OutputStream out)
- public OutputStreamWriter(Outputstream out,String charsetName)：可以指定编码集

**图示：**

![image-20200502114233303](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200502114234.png)



### 2.代码示例

```java
import org.junit.Test;
import java.io.*;
public class InputStreamReaderTest {
    @Test
    public void test1() {
        InputStreamReader isr = null;
        OutputStreamWriter osw = null;
        try {
            // 1.造文件、造流
            FileInputStream fis = new FileInputStream("hello.txt");
            FileOutputStream fos = new FileOutputStream("helloGbk.txt");

            // InputStreamReader(InputStream in)
            // 字节的输入流,使用系统默认的字符集;
            // 参数2 指明了字符集,取决于文件使用的字符集
            isr = new InputStreamReader(fis, "UTF-8");

            // OutputStreamWriter(OutputStream out, String charsetName)
            // 以字节的方式输出
            osw = new OutputStreamWriter(fos, "gbk");

            // 2.读写过程
            char[] cbuf = new char[20];
            int len;
            // 字节流 -> 字符 写入到cbuf中
            while (((len = isr.read(cbuf)) != -1)) {\
                // 字符 -> 字节流 ：将cbuf中的字符 写出到 osw中。
                osw.write(cbuf,0,len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 3.关流
            if (isr != null) {
                try {
                    isr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (osw != null) {
                try {
                    osw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

> 说明：文件编码的方式（比如：GBK），决定了解析时使用的字符集（也只能是GBK）。



### 3. 编码集

计算机只能识别二进制数据，早期由来是电信号。为了方便应用计算机，让它可以识别各个国家的文字。就将各个国家的文字用数字来表示，并一一对应，形成一张表。这就是编码表。

#### 3.1 常见的编码表

- `ASCII`：美国标准信息交换码。用**一个字节**的7位可以表示128个。
- ISO8859-1：拉丁码表。欧洲码表用**一个字节**的8位表示。
- GB2312：中国的中文编码表。最多两个字节编码所有字符
- `GBK`：中国的中文编码表升级，融合了更多的中文文字符号。==最多两个字节==编码
- Unicode：国际标准码，融合了目前人类使用的所字符。为每个字符分配唯一的字符码。==所有的文字都用两个字节==来表示。
- `UTF-8`：==变长的编码方式，可用1-4个字节来表示一个字符==。

![image-20200502120238023](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200502120240.png)



面向传输的众多==UTF(UCS Transfer Format)==标准出现了，顾名思义，UTF-8就是**每次8个位传输数据**，而UTF-16就是每次16个位。这是为传输而设计的编码，并使编码无国界，这样就可以显示全世界上所有文化的字符了。

- Unicode只是定义了一个庞大的、全球通用的字符集，并为每个字符规定了唯确定的编号，具体存储成什么样的字节流，取决于**字符编码方案**。推荐的Unicode编码是==UTF-8==和UTF-16。

**UTF-8变长编码表示**

![image-20200502120042318](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200502120043.png)

分成三个部分

![image-20201223195743707](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201223195743.png)

#### 3.2 编码应用

- 编码：字符串 --> 字节数组
- 解码：字节数组 --> 字符串

转换流的编码应用
- 可以将字符按指定编码格式存储
- 可以对文本数据按指定编码格式来解读
- 指定编码表的动作由构造器完成

**使用要求：**

客户端/浏览器端 <----> 后台(java,GO,Python,Node.js,php) <----> 数据库

要求前前后后使用的字符集都要统一：==UTF-8==.



## 六、标准输入、输出流

### 1.简介

[System.in](http://system.in/)：类型是InputStream，标准的输入流，默认从键盘输入

==System.out==：类型是PrintStream，其是OutputStream的子类FilterOutputStream的子类。标准的输出流，默认从控制台输出

### 2. 常用方法

通过System类的setln，setOut方法对默认设备进行改变。

- public static void `setIn(InputStream in)`：重新指定输入的流
- public static void `setOut(PrintStream out)`：重新指定输出的流。



### 3. 使用示例

从键盘输入字符串，要求将读取到的整行字符串转成大写输出。然后继续进行输入操作，

直至当输入“e”或者“exit”时，退出程序。

**设计思路**

方法一：使用Scanner实现：

- 调用`next()`返回一个字符串

方法二：使用 System.in 实现：

- System.in -->  转换流  --> BufferedReader的readLine()



注意：IDEA的TEST 中不支持键盘输入，需要改成main方法

```java
public class OtherStreamTest {
    public static void main(String[] args) {
        BufferedReader br = null;
        try {
            // 转换流:InputStreamReader的父类是 Reader
            InputStreamReader isr = new InputStreamReader(System.in);

            // 缓冲流：BufferedReader(Reader in)
            br = new BufferedReader(isr);

            while (true) {
                System.out.print("请输入字符串:");
                String data = br.readLine();
                if ("e".equalsIgnoreCase(data) || "exit".equalsIgnoreCase(data)) {
                    System.out.println("程序结束");
                    break;
                }
                System.out.println(data);
            }

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (br != null) {
                try {
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



### 4. 小练习

设计实现Scanner类。

- 都是静态方法，可以通过类名进行调用，例如：MyInput.readInt() 

```java
public class MyInput {
    // Read a string from the keyboard
    public static String readString() {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

        // Declare and initialize the string
        String string = "";

        // Get the string from the keyboard
        try {
            string = br.readLine();

        } catch (IOException ex) {
            System.out.println(ex);
        }

        // Return the string obtained from the keyboard
        return string;
    }

    // Read an int value from the keyboard
    public static int readInt() {
        return Integer.parseInt(readString());
    }

    // Read a double value from the keyboard
    public static double readDouble() {
        return Double.parseDouble(readString());
    }

    // Read a byte value from the keyboard
    public static double readByte() {
        return Byte.parseByte(readString());
    }

    // Read a short value from the keyboard
    public static double readShort() {
        return Short.parseShort(readString());
    }

    // Read a long value from the keyboard
    public static double readLong() {
        return Long.parseLong(readString());
    }

    // Read a float value from the keyboard
    public static double readFloat() {
        return Float.parseFloat(readString());
    }
}
```



## 七、打印流

实现将**基本数据类型**的数据格式转化为**字符串**输出

打印流：`PrintStream`和`PrintWriter`

- 提供了一系列重载的print()和printIn()方法，用于多种数据类型的输出
- PrintStream 和PrintWriter的输出不会抛出IOException异常
- PrintStream 和PrintWriter有自动flush功能
- PrintStream 打印的所有字符都使用平台的默认字符编码转换为字节。在需要写入字符而不是写入字节的情况下，应该使用 PrintWriter类。
- ==System.out返回的是PrintStream的实例==

方法：

-  `PrintStream(OutputStream out, boolean autoFlush)`：



测试：重定向输出到文件

```java
public class OtherStreamTest {
    @Test
    public void test2() {
        PrintStream ps = null;
        try {
            FileOutputStream fos = new FileOutputStream(new File("text.txt"));
            
            // 创建打印输出流,设置为自动刷新模式(写入换行符或字节 '\n' 时都会刷新输出缓冲区)
            //  PrintStream(OutputStream out, boolean autoFlush)
            ps = new PrintStream(fos, true);
            // 把标准输出流(控制台输出)改成文件
            if (ps != null) {
                System.setOut(ps);
            }
            for (int i = 0; i <= 255; i++) { // 输出ASCII字符
                System.out.print((char) i);
                if (i % 50 == 0) { // 每50个数据一行
                    System.out.println(); // 换行
                }
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } finally {
            if (ps != null) {
                ps.close();
            }
        }
    }
}
```



## 八、数据流

为了方便地操作Java语言的基本数据类型和String的数据，可以使用数据流。**数据流用于读取和写出基本数据类型、String类的数据**的两个类：

- DataInputStream 
- DataOutputStream 
- 分别“套接”在 InputStream和 OutputStream子类的流上

> DatalnputStream中的方法

- boolean readBoolean()
- byte readByte()
- char readChar()
- float readFloat()
- double readDouble()
- short readShort()
- long readLong()
- int readlnt()
- String readUTF()
- void readFully(byte[] b)

> DataOutputStream中的方法

将上述的方法的`read`改为相应的`write`即可。

注意：读取不同类型的数据的顺序要与当初写入文件时，保存的数据的顺序一致



**示例代码：**

测试一：将内存中的字符串、基本数据类型的变量写出到文件中。

```java
public class OtherStreamTest {
    @Test
    // 将内存中的字符串、基本数据类型的变量写出到文件中。
    public void test3() {
        // 1.造对象、造流
        DataOutputStream dos = null;
        try {
            dos = new DataOutputStream(new FileOutputStream("data.txt"));
            // 2.数据输出
            dos.writeUTF("Bruce");
            // 刷新操作，将内存的数据写入到文件
            dos.flush();
            
            dos.writeInt(23);
            dos.flush();
            
            dos.writeBoolean(true);
            dos.flush();
            
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 3.关闭流
            if (dos != null) {
                try {
                    dos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



测试二：将文件中存储的基本数据类型变量和字符串读取到内存中，保存在变量中。

```java
public class OtherStreamTest {
    @Test
    // 将文件中存储的基本数据类型变量和字符串读取到内存中，保存在变量中。
    public void test4() {
        // 1.造对象、造流
        DataInputStream dis = null;
        try {
            dis = new DataInputStream(new FileInputStream("data.txt"));
            // 2.从文件读入数据
            String name = dis.readUTF();
            int age = dis.readInt();
            boolean isMale = dis.readBoolean();
            
            System.out.println("name:"+name);
            System.out.println("age:"+age);
            System.out.println("isMale:"+isMale);
            
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 3.关闭流
            if (dis != null) {
                try {
                    dis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



## 九、对象流

对象流用于==存储和读取基木数据类型数据或对象的处理流==。它的强大之处就是可以把Java中的对象写入到数据源中，也能把对象从数据源中还原回来。

ObjectInputStream 和 OjbectOutputSteam

- **序列化**：用`ObjectOutputStream`类**保存**基本类型数据或对象的机制
- **反序列化**：用`ObjectInputStream`类**读取**基本类型数据或对象的机制
- ObjectOutputStream和ObjectInputStream不能序列化**static**和==transient==修饰的成员变量

### 1. 对象流

ObjectInputStream 和 ObjectOutputStream



### 2.作用

- ObjectOutputStream：内存中的对象  -> 存储中的文件、通过网络传输出去：序列化过程
- ObjectInputStream：存储中的文件、通过网络接收过来   ->  内存中的对象：反序列化过程



### 3. 对象的序列化

==对象序列化机制==允许把内存中的Java对象转换成平台无关的**二进制流**，从而允许把这种二进制流持久地保存在磁盘上，或通过网络将这种二进制流传输到另一个网络节点。当其它程序获取了这种二进制流，就可以恢复成原来的Java对象。

序列化的好处在于可将任何实现了`Serializable接口`的对象转化为字节数据，使其在保存和传输时可被还原。

序列化是**RMI(Remote Method Invoke-远程方法调用)**过程的参数和返回值都必须实现的机制，RMI是JavaEE的基础。因此**序列化机制是JavaEE平台的基础**。

- 如果需要让某个对象支持序列化机制，则必须让对象所属的类及其属性是可序列化的，为了让某个类是可序列化的，该类必须实现如下两个接口之一。否则，会抛出 ==NotserializableEXception异常==
  - `Serializable`
  - Externalizable

凡是实现Serializable接口的类都有一个表示**序列化版本标识符**的静态变量：
- private static final long `serialVersionUID`;
- serialVersionUID 用来表明类的不同版本间的兼容性。简言之，其目的是以序列化对象进行版本控制，有关各版本反序列化时是否兼容。
- 如果类没有显示定义这个静态常量，它的值是Java运行时环境根据类的内部细节自动生成的。若类的实例变量做了修改，serialVersionUID可能发生变化。故建议显式声明。

- 简单来说，Java的序列化机制是通过在运行时判断类的serialversionUID来验证版本一致性的。在进行反序列化时，JVM会把传来的字节流中的serialversionUID与本地相应实体类的serialversionUID进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的==InvalidCastException异常==。



### 4.实现序列化的对象所属的类需要满足：

1. 需要实现接口：Serializable（标识接口）
2. 当前类提供一个全局常量：serialVersionUID（序列版本号）
3. 除了当前Person类需要实现Serializable接口之外，还必须保证其内部所属性也必须是可序列化的。（默认情况下，基本数据类型可序列化）

补充：ObjectOutputStream和ObjectInputStream==不能序列化static和transient修饰的成员变量==



### 5  对象流的使用

#### 5.1 序列化代码实现

序列化：将对象写入磁盘或进行网络传输

- 要求被序列化对象必须实现序列化

```java
public class objectInputOutputStreamTest {
	/**
     * 序列化：将对象写入磁盘或进行网络传输
     *
     * @param
     * @return void
     */
    @Test
    public void test() {
        ObjectOutputStream oos = null;
        try {
            // 1.创建对象，创建流
            oos = new ObjectOutputStream(new FileOutputStream("object.dat"));

            // 2.操作流
            oos.writeObject(new String("我爱北京天安门"));
            //刷新操作
            oos.flush();
            
            oos.writeObject(new Person("王铭", 23));
            oos.flush();
//
//            oos.writeObject(new Person("张学良", 23));
//            oos.flush();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 3.关闭流
            try {
                oos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }
}
```



可序列化的Person类

```java
/**
 * Person需要满足如下的要求，方可序列化
 * 1.需要实现接口: Serializable
 * 2.当前类提供一个全局常量：serialVersionUID（序列版本号）
 * 3.除了当前Person类需要实现Serializable接口之外，
 *     还必须保证其内部所属性也必须是可序列化的。（默认情况下，基本数据类型可序列化）
 */
class Person implements Serializable {
    // 序列版本号
    public static final long serialVersionUID = 4524436321L;

    private String name;
    private int age;

    public Person() {
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```





#### 5.2 反序列化代码实现

反序列化：将磁盘的对象数据源读出

```java
public class objectInputOutputStreamTest {
    /**
     * 反序列化：将磁盘的对象数据源读出
     *
     * @param
     * @return void
     */
    @Test
    public void test1() {
        ObjectInputStream ois = null;
        try {
            // 1.创建对象，创建流
            ois = new ObjectInputStream(new FileInputStream("object.dat"));

            // 2.操作流
            Object o = ois.readObject();
            String str = (String) o;
            Person p1 = (Person) ois.readObject();

            System.out.println(str);
            System.out.println(p1);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } finally {
            // 3.关闭流
            try {
                ois.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```



## 十、任意存取文件流

RandomAccessFile 直接继承于java.lang.Object类，==实现了DataInput和DataOutput接口==；RandomAccessFile既可以作为一个输入流，又可以作为一个输出流。

RandomAccessFile类支持“随机访问”的方式，程序可以直接跳到文件的任意地方来读、写文件
- 支持只访问文件的部分内容
- 可以向已存在的文件后追加内容

RandomAccessFile 对象包含一个**记录指针**，用以标示当前读写处的位置；RandomaccessFile 类对象可以自由移动记录指针：

- long `getFilePointer()`：获取文件记录指针的当前位置
- void `seek(long pos)`：将文件记录指针定位到pos位置

**构造器**

>- public RandomAccessFile(File file,String mode)
>
>- public RandomAccessFile(String name,String mode)

创建RandomAccessFile类实例需要指定一个`mode`参数，该参数指定RandomAccessFile的访问模式:

- `r`：以只读方式打开
- `rw`：打开以便读取和写入
- `rwd`：打开以便读取和写入；同步文件内容的更新
- `rws`：打开以便读取和写入；同步文件内容和元数据的更新

如果模式为只读 r，则不会创建文件，而是会去读取一个已经存在的文件，读取的文件不存在则会出现异常。如果模式为rw读写，文件不存在则会去创建文件，存在则不会创建。

### 使用说明

1. 如果RandomAccessFile作为输出流时，写出到的文件如果不存在，则在执行过程中自动创建。
2. 如果写出到的文件存在，则会对原文件内容进行覆盖，==默认情况下，从头覆盖==。
3. 可以通过相关的操作，实现RandomAccessFile“插入”数据的效果。借助seek(int pos)方法

### 使用示例

文件的读取和写出操作

```java
public class RandomAccessFileTest {
    /**
     * 文件的读取和写出操作
     * @param 
     * @return   void
     */
    @Test
    public void test1() {
        RandomAccessFile raf1 = null;
        RandomAccessFile raf2 = null;
        try {
            // 1.创建对象，创建流
            // RandomAccessFile(File file, String mode)
            raf1 = new RandomAccessFile(new File("test1.jpg"), "r");
            raf2 = new RandomAccessFile(new File("test11.jpg"), "rw");
            // 2.操作流
            byte[] buffer = new byte[1024];
            int len;
            // 读出
            while ((len = raf1.read(buffer)) != -1) {
                // 写入
                raf2.write(buffer, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 3.关闭流
            if (raf1 != null) {
                try {
                    raf1.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
            if (raf2 != null) {
                try {
                    raf2.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

    }
}
```

使用RandomAccessFile实现数据的插入效果

```java
public class RandomAccessFileTest {
    /**
     * 使用RandomAccessFile实现数据的插入效果
     *
     * @param
     * @return void
     */
    @Test
    public void test2() {
        RandomAccessFile raf1 = null;
        try {
            raf1 = new RandomAccessFile(new File("hello.txt"), "rw");
            // 将指针调到角标为3的位置
            raf1.seek(3);

            StringBuilder stringBuilder = new StringBuilder((int) new File("hello.txt").length());

            byte[] buffer = new byte[20];
            int len;
            while (((len = raf1.read(buffer)) != -1)) {
                // 保存指针3后面的所有数据到StringBuilder中
                stringBuilder.append(new String(buffer,0,len));
            }

            // 调回指针，写入“xyz”
            raf1.seek(3);
            raf1.write("xyz".getBytes());
            // 将StringBuilder中的数据写入到文件中
            // 字符串转byte[]
            raf1.write(stringBuilder.toString().getBytes());


        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (raf1 != null) {
                try {
                    raf1.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```





```java
@Test
public void test2(){
    RandomAccessFile raf1 = null;
    try {
        raf1 = new RandomAccessFile(new File("hello.txt"), "rw");

        raf1.seek(3);//将指针调到角标为3的位置
        //            //方式一
        //            //保存指针3后面的所有数据到StringBuilder中
        //            StringBuilder builder = new StringBuilder((int) new File("hello.txt").length());
        //            byte[] buffer = new byte[20];
        //            int len;
        //            while ((len = raf1.read(buffer)) != -1){
        //                builder.append(new String(buffer,0,len));
        //            }

        //方式二
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        byte[] buffer = new byte[20];
        int len;
        while ((len = raf1.read(buffer)) != -1){
            baos.write(buffer);
        }
        //调回指针，写入“xyz”
        raf1.seek(3);
        raf1.write("xyz".getBytes());
        //将StringBuilder中的数据写入到文件中
        raf1.write(baos.toString().getBytes());
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if (raf1 != null){
            try {
                raf1.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```



## 十一、流的基本应用总结

流是用来处理数据的。

处理数据时，一定要先明确数据源，与数据目的地数据源可以是文件，可以是键盘数据目的地可以是文件、显示器或者其他设备

- 而流只是在帮助数据进行传输，并对传输的数据进行处理，比如过滤处理、转换处理等

- 除去RandomAccessFile类外所有的流都继承于四个基本数据流抽象类InputSteam、OutputSteam、Reader、Writer

- 不同的操作流对应的后缀均为四个抽象基类中的某一个

  ![image-20200502091615386](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200502091616.png)

- 不同处理流的使用方式都是标准操作：

  - 创建文件对象，创建相应的流
  - 处理流数据
  - 关闭流
  - 用try-catch-finally处理异常



## 十二、NIO

Path、Paths、Files的使用，介绍比较简单。

### 1. NIO的使用说明

==Java NIO (New IO，Non-Blocking IO)==是从Java 1.4版本开始引入的一套新的IO API，可以替代标准的Java IO AP。

- NIO与原来的IO同样的作用和目的，但是使用的方式完全不同，NIO支持面向缓冲区的(IO是面向流的)、基于通道的IO操作。
- NIO将以更加高效的方式进行文件的读写操作。
- JDK 7.0 对NIO进行了极大的扩展，增强了对文件处理和文件系统特性的支持，称他为 NIO.2。

Java API中提供了两套NIO，一套是针对标准输入输出NIO，另一套就是网络编程NIO

- java.nio.channels.Channel
  - `FileChannel`：处理本地文件
  -  SocketChannel：TCP网络编程的客户端的Channel
  - ServerSocketChannel：TCP网络编程的服务器端的Channel
  - DatagramChannel：UDP网络编程中发送端和接收端的Channel



### 2.Path接口 ---JDK 7.0提供

早期的Java只提供了一个File类来访问文件系统，但File类的功能比较有限，所提供的方法性能也不高。而且，大多数方法在出错时仅返回失败，并不会提供异常信息。

NIO.2 为了弥补这种不足，引入了Path接口，代表一个平台无关的平台路径，描述了目录结构中文件的位置。==Path可以看成是File类的升级版本，实际引用的资源也可以不存在==。

#### 2.1 Path 说明

Path替换原有的File类。

- 在以前IO操作都是这样写的：
  - import java.io.File
  - File file = new File("index.html");
- 但在Java7中，我们可以这样写：
  - import java.nio.file.Path;
  - import java.nio.file.Paths;
  - Path path = `Paths.get("index. html");`

#### 2.2 Paths 使用

Paths类提供的静态 get() 方法用来获取Path对象：

- static Path `get(String first,String….more)`：用于将多个字符串串连成路径
- static Path `get(URI uri)`：返回指定uri对应的Path路径

**代码示例**

```java
@Test
public void test1(){
    Path path1 = Paths.get("hello.txt"); //new File(String filepath)

    Path path2 = Paths.get("E:\\", "test\\test1\\haha.txt");//new File(String parent,String filename);

    Path path3 = Paths.get("E:\\", "test");
    System.out.println(path1);
    System.out.println(path2);
    System.out.println(path3);

}
```



#### 2.3 Path 常用方法

- String toString() ： 返回调用 Path 对象的字符串表示形式
- boolean startsWith(String path) ：判断是否以 path 路径开始
- boolean endsWith(String path)：判断是否以 path 路径结束
- boolean isAbsolute()：判断是否是绝对路径
- Path getParent()：返回Path对象包含整个路径，不包含 Path 对象指定的文件路径
- Path getRoot()：返回调用 Path 对象的根路径
- Path getFileName()：返回与调用 Path 对象关联的文件名
- int getNameCount()：返回Path 根目录后面元素的数量
- Path getName(int idx)：返回指定索引位置 idx 的路径名称
- Path toAbsolutePath()：作为绝对路径返回调用 Path 对象
- Path resolve(Path p)：合并两个路径，返回合并后的路径对应的Path对象
- File toFile()：将Path转化为File类的对象

**代码示例**

```java
@Test
public void test2() {
    Path path1 = Paths.get("d:\\", "nio\\nio1\\nio2\\hello.txt");
    Path path2 = Paths.get("hello.txt");

    // String toString() ： 返回调用 Path 对象的字符串表示形式
    System.out.println(path1);

    // boolean startsWith(String path) : 判断是否以 path 路径开始
    System.out.println(path1.startsWith("d:\\nio"));
    
    // boolean endsWith(String path) : 判断是否以 path 路径结束
    System.out.println(path1.endsWith("hello.txt"));
    
    // boolean isAbsolute() : 判断是否是绝对路径
    System.out.println(path1.isAbsolute() + "~");
    System.out.println(path2.isAbsolute() + "~");
    
    // Path getParent() ：返回Path对象包含整个路径，不包含 Path 对象指定的文件路径
    System.out.println(path1.getParent());
    System.out.println(path2.getParent());
    
    // Path getRoot() ：返回调用 Path 对象的根路径
    System.out.println(path1.getRoot());
    System.out.println(path2.getRoot());
    
    // Path getFileName() : 返回与调用 Path 对象关联的文件名
    System.out.println(path1.getFileName() + "~");
    System.out.println(path2.getFileName() + "~");
    
    // int getNameCount() : 返回Path 根目录后面元素的数量
    // Path getName(int idx) : 返回指定索引位置 idx 的路径名称
    for (int i = 0; i < path1.getNameCount(); i++) {
        System.out.println(path1.getName(i) + "*****");
    }

    // Path toAbsolutePath() : 作为绝对路径返回调用 Path 对象
    System.out.println(path1.toAbsolutePath());
    System.out.println(path2.toAbsolutePath());
    
    // Path resolve(Path p) :合并两个路径，返回合并后的路径对应的Path对象
    Path path3 = Paths.get("d:\\", "nio");
    Path path4 = Paths.get("nioo\\hi.txt");
    path3 = path3.resolve(path4);
    System.out.println(path3);

    // File toFile(): 将Path转化为File类的对象
    // Path--->File的转换
    File file = path1.toFile();
	
    // File--->Path的转换
    Path newPath = file.toPath();

}
```



### 3.Files类

java.nio.file.Files用于操作文件或目录的工具类

#### 3.1 Files类 常用方法

Path `copy`(Path src, Path dest, CopyOption … how) : 文件的复制
- 要想复制成功，要求path1对应的物理上的文件存在。path1对应的文件没有要求。

Files.copy(path1, path2, StandardCopyOption.REPLACE_EXISTING);

Path `createDirectory(`Path path, FileAttribute<?> … attr) ：创建一个目录
- 要想执行成功，要求path对应的物理上的文件目录不存在。一旦存在，抛出异常。

Path `createFile`(Path path, FileAttribute<?> … arr) ：创建一个文件
- 要想执行成功，要求path对应的物理上的文件不存在。一旦存在，抛出异常。

void `delete`(Path path)：删除一个文件/目录，如果不存在，执行报错

void `deleteIfExists`(Path path) ：Path对应的文件/目录如果存在，执行删除；

- 如果不存在，正常执行结束

Path `move`(Path src, Path dest, CopyOption…how) ：将 src 移动到 dest 位置

- 要想执行成功，src对应的物理上的文件需要存在，dest对应的文件没有要求。

long `size`(Path path)：返回 path 指定文件的大小

**代码示例**

```java
@Test
public void test1() throws IOException{
    Path path1 = Paths.get("d:\\nio", "hello.txt");
    Path path2 = Paths.get("atguigu.txt");

    //		Path copy(Path src, Path dest, CopyOption … how) : 文件的复制
    //要想复制成功，要求path1对应的物理上的文件存在。path1对应的文件没有要求。
    //		Files.copy(path1, path2, StandardCopyOption.REPLACE_EXISTING);

    //		Path createDirectory(Path path, FileAttribute<?> … attr) : 创建一个目录
    //要想执行成功，要求path对应的物理上的文件目录不存在。一旦存在，抛出异常。
    Path path3 = Paths.get("d:\\nio\\nio1");
    //		Files.createDirectory(path3);

    //		Path createFile(Path path, FileAttribute<?> … arr) : 创建一个文件
    //要想执行成功，要求path对应的物理上的文件不存在。一旦存在，抛出异常。
    Path path4 = Paths.get("d:\\nio\\hi.txt");
    //		Files.createFile(path4);

    //		void delete(Path path) : 删除一个文件/目录，如果不存在，执行报错
    //		Files.delete(path4);

    //		void deleteIfExists(Path path) : Path对应的文件/目录如果存在，执行删除.如果不存在，正常执行结束
    Files.deleteIfExists(path3);

    //		Path move(Path src, Path dest, CopyOption…how) : 将 src 移动到 dest 位置
    //要想执行成功，src对应的物理上的文件需要存在，dest对应的文件没有要求。
    //		Files.move(path1, path2, StandardCopyOption.ATOMIC_MOVE);

    //		long size(Path path) : 返回 path 指定文件的大小
    long size = Files.size(path2);
    System.out.println(size);

}
```



#### 3.2 Files类 判断方法

- boolean exists(Path path, LinkOption … opts) : 判断文件是否存在

- boolean isDirectory(Path path, LinkOption … opts) : 判断是否是目录

  不要求此path对应的物理文件存在。

- boolean isRegularFile(Path path, LinkOption … opts) : 判断是否是文件

- boolean isHidden(Path path) : 判断是否是隐藏文件

  要求此path对应的物理上的文件需要存在。才可判断是否隐藏。否则，抛异常。

- boolean isReadable(Path path) : 判断文件是否可读

- boolean isWritable(Path path) : 判断文件是否可写

- boolean notExists(Path path, LinkOption … opts) : 判断文件是否不存在

**代码示例**

```java
@Test
public void test2() throws IOException{
    Path path1 = Paths.get("d:\\nio", "hello.txt");
    Path path2 = Paths.get("atguigu.txt");
    //		boolean exists(Path path, LinkOption … opts) : 判断文件是否存在
    System.out.println(Files.exists(path2, LinkOption.NOFOLLOW_LINKS));

    //		boolean isDirectory(Path path, LinkOption … opts) : 判断是否是目录
    //不要求此path对应的物理文件存在。
    System.out.println(Files.isDirectory(path1, LinkOption.NOFOLLOW_LINKS));

    //		boolean isRegularFile(Path path, LinkOption … opts) : 判断是否是文件

    //		boolean isHidden(Path path) : 判断是否是隐藏文件
    //要求此path对应的物理上的文件需要存在。才可判断是否隐藏。否则，抛异常。
    //		System.out.println(Files.isHidden(path1));

    //		boolean isReadable(Path path) : 判断文件是否可读
    System.out.println(Files.isReadable(path1));
    //		boolean isWritable(Path path) : 判断文件是否可写
    System.out.println(Files.isWritable(path1));
    //		boolean notExists(Path path, LinkOption … opts) : 判断文件是否不存在
    System.out.println(Files.notExists(path1, LinkOption.NOFOLLOW_LINKS));
}
```

**补充：**

- StandardOpenOption.READ:表示对应的Channel是可读的。
- StandardOpenOption.WRITE：表示对应的Channel是可写的。
- StandardOpenOption.CREATE：如果要写出的文件不存在，则创建。如果存在，忽略
- StandardOpenOption.CREATE_NEW：如果要写出的文件不存在，则创建。如果存在，抛异常



#### 3.3 Files类 操作内容

- InputStream newInputStream(Path path, OpenOption…how):获取 InputStream 对象
- OutputStream newOutputStream(Path path, OpenOption…how) : 获取 OutputStream 对象
- SeekableByteChannel newByteChannel(Path path, OpenOption…how) : 获取与指定文件的连接，how 指定打开方式。
- DirectoryStream<Path> newDirectoryStream(Path path) : 打开 path 指定的目录

**代码示例**

```java
@Test
public void test3() throws IOException{
    Path path1 = Paths.get("d:\\nio", "hello.txt");

    // InputStream newInputStream(Path path, OpenOption…how):获取 InputStream 对象
    InputStream inputStream = Files.newInputStream(path1, StandardOpenOption.READ);

    // OutputStream newOutputStream(Path path, OpenOption…how) : 获取 OutputStream 对象
    OutputStream outputStream = Files.newOutputStream(path1, StandardOpenOption.WRITE,StandardOpenOption.CREATE);


    // SeekableByteChannel newByteChannel(Path path, OpenOption…how) : 获取与指定文件的连接，how 指定打开方式。
    SeekableByteChannel channel = Files.newByteChannel(path1, StandardOpenOption.READ,StandardOpenOption.WRITE,StandardOpenOption.CREATE);

    // DirectoryStream<Path>  newDirectoryStream(Path path) : 打开 path 指定的目录
    Path path2 = Paths.get("e:\\teach");
    DirectoryStream<Path> directoryStream = Files.newDirectoryStream(path2);
    Iterator<Path> iterator = directoryStream.iterator();
    while(iterator.hasNext()){
        System.out.println(iterator.next());
    }
}
```