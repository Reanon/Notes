# Java之网络编程

>参考Bilibili尚硅谷课程：[尚硅谷_Java 网络编程(宋红康主讲)](https://www.bilibili.com/video/BV1Kb411W75N?p=620)

InternetAccess类的使用

## 一、概述

1. 计算机网络
   - 把分布在不同地理区域的计算机与专门的外部设备用通信线路互连成一个规模大、功能强的网络系统，从而使众多的计算机可以方便地互相传递信息共享硬件、软件、数据信息等资源。
2. 网络编程的目的: 直接或间接地通过网络协议与其它计算机实现数据交换，进行通讯
3. 实现网络通信需要解决的两个问题
   - 如何准确地定位网络上一台或多台主机；定位主机上的特定的应用
   - 找到主机后如何可靠高效地进行数据传输

## 二、网络通讯要素

- 通信双方地址：
  - IP
  - 端口号
- 网络通信协议：
  - OSI参考模型：模型过于理想化，未能在因特网上进行广泛推广
  - TCP/IP参考模型(或TCP/IP协议)：事实上的国际标准。（应用层、传输层、网络层、物理+数据链路层）

**网络通讯协议**

![image-20200503155354025](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200503155401.png)

**通讯过程**

![image-20200503155428227](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201224110625.png)

### 3. 通讯要素一：IP和端口号

#### 3.1 IP的理解

- IP：唯一的标识 Internet 上的计算机（通信实体）
- 在Java中使用`InetAddress类`代表IP

> IP分类

唯一的标识Internet上的计算机（通信实体)

- ==本地回环地址(hostAddress): 127.0.0.1==   主机名(hostName): `localhost`

IP地址分类方式1:IPV4和 IPV6

- ==IPV4：4个字节==组成，4个0-255。大概42亿，30亿都在北美，亚洲4亿。2011年初已经用尽。以**点分十进制**表示，如192.168.0.1
- ==IPV6： 128位，16个字节==，写成8个无符号整数，每个整数用四个十六进制位表示，数之间用冒号(`:`）分开，如：3ffe:3201:1401:1280:c8ff:fe4d:db39:1984

IP地址分类方式2：**公网地址(万维网使用)**和**私有地址(局域网使用)**。

- 192.168.开头的就是私有址址，范围即为==192.168.0.0--192.168.255.255==，专门为组织机构内部使用
- 特点:不易记忆

> 域名

通过域名解析服务器将域名解析为IP地址 www.baidu.com www.mi.com www.jd.com

- 域名解析：域名容易记忆，当在连接网络时输入一个主机的域名后，==域名服务器(DNS)==负责将域名转化成IP地址，这样才能和主机建立连接。

#### 3.2 端口号：

用于标识正在计算机上运行的进程。

- 要求：不同的进程不同的端口号
- 范围：被规定为一个 **16 位**的整数 `0~65535`。
- 分类：
  - **公认端口**：0~1023。被预先定义的服务通信占用，如：
    - ==HTTP占用端口80==
    - FTP占用端口21
    - ==SSH占用端口22==
    - TeInet占用端口23。
  - **注册端口**：1024~49151。分配给用户进程或应用程序。如：
    - Tomcat占用端口8080
    - ==MSQL占用端口3306==
    - Oracle占用端口1521等。
  - **动态私有端口**：49152~65535。

>网络套接字

- 端口号与IP地址的组合得出一个网络套接字：==Socket==。

#### 3.3 InetAddress类

此类的一个对象就代表着一个具体的IP地址

##### 3.2.1 实例化

- static InetAddress `getByName(String host)` ：获取地址
- static InetAddress `getLocalHost()`：获取本地ip

##### 3.2.2 常用方法

- String `getHostName()`：获取域名
- String `getHostAddress()`：获取地址

```java
import org.junit.Test;
import java.net.InetAddress;
import java.net.UnknownHostException;
public class InetAddressTest {
    @Test
    public void test(){
        InetAddress inet1 = null;
        try {
            // 1.static InetAddress getByName(String host)
            inet1 = InetAddress.getByName("192.168.10.123");
            System.out.println(inet1); // /192.168.10.123

            InetAddress inet2 = InetAddress.getByName("www.baidu.com");
            System.out.println(inet2); // www.baidu.com/112.80.248.75

            InetAddress inet3 = InetAddress.getByName("127.0.0.1");
            System.out.println(inet3); // /127.0.0.1

            // 2.static InetAddress getLocalHost()
            // 获取本地ip
            InetAddress localHost = InetAddress.getLocalHost();
            System.out.println(localHost);// /127.0.0.1

            // 3.String getHostName()
            String hostName = inet2.getHostName();
            System.out.println(hostName); // www.baidu.com

            // 4.String getHostAddress()
            String hostAddress = inet2.getHostAddress();
            System.out.println(hostAddress); // 180.101.49.11
        } catch (UnknownHostException e) {
            e.printStackTrace();
        }
    }
}
```

### 4. 通信要素二：网络通信协议

传输层协议中有两个非常重要的协议:

- ==传输控制协议TCP(Transmission Control Protocol)==
- ==用户数据报协议UDP(User Datagram Protocol)==

TCP/IP以其两个主要协议：`传输控制协议(TCP)`和`网络互联协议(IP)`而得名，实际上是一组协议，包括多个具有不同功能且互为关联的协议。

- lP(Internet Protocol)协议是网络层的主要协议，支持网间互连的数据通信。

>TCP/IP协议模型从更实用的角度出发，形成了高效的四层体系结构，即**物理链路层**、**IP层**、**传输层**和**应用层**。

#### 4.1分层模型

![image-20200503160650146](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200503160651.png)

#### 4.2 TCP和UDP的区别

**TCP协议**

- 使用TCP协议前，须先建立TCP连接，形成传输数据通道
- 传输前，采用“==三次握手==”方式，点对点通信，**是可靠的**
- TCP协议进行通信的两个应用进程：客户端、服务端。
- 在连接中可进行**大数据量的传输**
- 传输完毕，需**释放已建立的连接，效率低**

**UDP协议**

- 将数据、源、目的封装成数据包，**不需要建立连接**
- 每个数据报的大小**限制在64K**内
- 发送不管对方是否准备好，接收方收到也不确认，故是**不可靠的**
- 可以广播发送
- 发送数据结束时**无需释放资源，开销小，速度快**

#### 4.3 TCP三次握手和四次挥手

![image-20200503160945073](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200503160946.png)





![image-20200503160952992](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200503160954.png)

### 5. 套接字Socket

端口号与IP地址的组合得出一个网络套接字：Socket

- 利用套接字(Socket)开发网络应用程序早已被广泛的采用，以至于成为事实上的标准。
- 网络上具有唯一标识的P地址和端口号组合在一起才能构成唯一能识别的标识符套接字。
- 通信的两端都要有 Socket，是两台机器间通信的端点。
- 网络通信其实就是 Socket间的通信
- Socket允许程序把网络连接当成一个流，数据在两个Socket间通过IO传输。
- 一般主动发起通信的应用程序属客户端，等待通信请求的为服务端
- Socket分类
  - 流套接字(stream socket)：使用TCP提供可依赖的字节流服务
  - 数据报套接字(datagram socket)：使用UDP提供“尽力而为”的数据报服务

#### Socket类常用方法

> Socket类的常用构造器

- public Socket(InetAddress address,int port)：创建一个流套接字并将其连接到指定IP地址的指定端口号。public Socket(String host,int port)：创建一个流套接字并将其连接到指定主机上的指定端口号。

> Socket类的常用方法

- public InputStream getInputStream()：返回此套接字的输入流。可以用于接收网络消息
- public OutputStream getOutputStream()：返回此套接字的输出流。可以用于发送网络消息
- public lnetAddress getInetAddress()：此套接字连接到的远程IP地址;如果套接字是未连接的，则返回nullpublic InetAddress getLocalAddress()获取套接字绑定的本地地址。即本端的IP地址
- public int getPort()：此套接字连接到的远程端口号;如果尚未连接套接字，则返回0。
  public int getLocalPort()：返回此套接字绑定到的本地端口。如果尚未绑定套接字，则返回-1。即本端的端口号。
- public void close()：关闭此套接字。套接字被关闭后，便不可在以后的网络连接中使用（即无法重新连接或重新绑定）。需要创建新的套接字对象。关闭此套接字也将会关闭该套接字的 InputStream 和OutputStream。
- public void shutdownInput()：如果在套接字上调用shutdownInput()后从套接字输入流读取内容，则流将返回EOF（文件结束符）。即不能在从此套接字的输入流中接收任何数据。
- public void shutdownOutput()：禁用此套接字的输出流。对于TCP套接字，任何以前写入的数据都将被发送，并且后跟TCP的正常连接终止序列。如果在套接字上调用shutdownOutput()后写入套接字输出流，则该流将抛出IOException。即不能通过此套接字的输出流发送任何数据。

## 三、TCP网络编程

Java语言的基于套接字Socket编程分为客户端和服务端

**基于TCP的Socket通信模型**

![image-20200503162557927](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200503162559.png)

### 1. 基于Socke的TCP编程

#### 1.1 客户端Socket的工作过程

- 创建Socket：根据指定服务端的P地址或端口号构造Sσcket类对象。若服务器端响应，则建立客户端到服务器的通信线路。若连接失败，会出现异常。
- 打开连接到 Socket的输入出流：使用getInputstream()方法获得输入流，使用getOutputStream()方法获得输出流，进行数据传输
- 按照一定的协议对Socket进行读/写操作：通过输入流读取服务器放入线路的信息（但不能读取自己放入线路的信息），通过输出流将信息写入线程
- 关闭 Socket：断开客户端到服务器的连接，释放线路

**说明**

- 客户端程序可以使用Socket类创建对象，创建的同时会自动向服务器方发起连接。
- Socket的构造器是：
  - Socket(String host，int port) throws UnknownHostException，EXCeption：向服务器（域名是host,端口号为port）发起TCP连接，若成功，则创建Socket对象，否则抛出异常。
  - Socket(InetAddress address，int port)throws IOException：根据InetAddress对象所表示的IP地址以及端口号port发起连接
- 客户端建立 socketAtClient对象的过程就是向服务器发出套接字连接请求

#### 1.2 服务器端Socket的工作过程：

- 调用ServerSocket(int port)：创建一个服务器端套接字，并绑定到指定端口上。用于监听客户端的请求。
- 调用accept0()：监听连接请求，如果客户端请求连接，则接受连接，返回通信套接字对象。
- 调用该Socket类对象的getOutputStream()和getInputStream()：获取输出流和输入流，开始网络数据的发送和接收。
- 关闭 ServerSocket和Socket对象：客户端访问结束，关闭通信套接字。

**说明：**

- ServerSocket对象负责等待客户端请求建立套接字连接，类似邮局某个窗口中的业务员。也就是说，服务器必须事先建立一个等待客户请求建立套接字连接的 Server Socket对象。
- 所谓“接收”客户的套接字请求，就是 accept()方法会返回一个Socket对象

#### 1.3 代码示例

例子1：客户端发送信息给服务端，服务端将数据显示在控制台上

客户端

```java
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.InetAddress;
import java.net.ServerSocket;
import java.net.Socket;

public class TCPTest1 {
    // 客户端
    @Test
    public void client() {
        Socket socket = null;
        OutputStream os = null;
        try {
            // 1.创建Socket对象，指明服务器端的ip和端口号
            InetAddress inet = InetAddress.getLocalHost();
            socket = new Socket(inet, 8899);
            // 2.获取一个输出流，用于输出数据
            os = socket.getOutputStream();
            // 3.写出数据的操作
            // getBytes(): String -> byte[]
            os.write("你好，我是客户端mm".getBytes());

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 4.资源的关闭
            if (os != null) {
                try {
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

服务端

```java
public class TCPTest1 {
    // 服务端
    @Test
    public void sever() {

        ServerSocket serverSocket = null;
        Socket socket = null;
        InputStream inputStream = null;
        ByteArrayOutputStream baos = null;
        try {
            // 1.创建服务器端的ServerSocket，指明自己的端口号
            // ServerSocket(int port)
            serverSocket = new ServerSocket(8899);

            // 2.调用accept()表示接收来自于客户端的socket
            socket = serverSocket.accept();

            // 3.获取输入流
            inputStream = socket.getInputStream();

            // 4.读取输入流中的数据
            // 方式一：不建议这样写，可能会有乱码
//            byte[] buffer = new byte[20];
//            int len;
//            while (((len = inputStream.read(buffer)) != -1)) {
//                String s = new String(buffer, 0, len);
//                System.out.println(s);
//            }

            // 方式二：使用ByteArrayOutputStream
            baos = new ByteArrayOutputStream();
            byte[] buffer = new byte[5];
            int len;
            while (((len = inputStream.read(buffer)) != -1)) {
                baos.write(buffer, 0, len);
            }
            System.out.println(baos.toString());

            //
            System.out.println("收到了来自于：" +
                    socket.getInetAddress().getHostAddress() + "的数据");

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //5.关闭资源
            try {
                baos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if (inputStream != null) {
            try {
                inputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if (socket != null) {
            try {
                socket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if (serverSocket != null) {
            try {
                serverSocket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

例题2：客户端发送文件给服务端，服务端将文件保存在本地。

客户端

- 这里涉及到的异常，应该使用try-catch-finally处理

```java
public class TCPTest2 {
    @Test
    public void client() throws IOException {
        // 1.创建Socket对象，指明服务器端的ip和端口号
        Socket socket = new Socket(InetAddress.getLocalHost(), 8989);
        // 2.获取一个输出流，用于输出数据
        OutputStream outputStream = socket.getOutputStream();
        // 从文件中写出
        FileInputStream fileInputStream = new FileInputStream(new File("test.jpg"));

        // 3.写出数据的操作
        byte[] buffer = new byte[1024];
        int len;
        while (((len = fileInputStream.read(buffer)) != -1)) {
            outputStream.write(buffer,0,len);
        }
        // 4.资源的关闭
        fileInputStream.close();
        outputStream.close();
        socket.close();
    }
}
```



服务端

```java
public class TCPTest2 {
    @Test
    public void sever() throws IOException {
        // 1.创建服务器端的ServerSocket，指明自己的端口号
        ServerSocket serverSocket = new ServerSocket(8989);
        // 2.调用accept()表示接收来自于客户端的socket
        Socket socket = serverSocket.accept();
        // 3.获取输入流
        InputStream inputStream = socket.getInputStream();
        FileOutputStream fileOutputStream = new FileOutputStream(new File("test1.jpg"));

        // 4.读取输入流中的数据
        byte[] buffer = new byte[1024];
        int len;
        while (((len = inputStream.read(buffer)) != -1)) {
            fileOutputStream.write(buffer,0,len);
        }
        System.out.println("输出来自：" +
                socket.getInetAddress().getHostAddress() + "的图片");
        // 5.关闭资源
        fileOutputStream.close();
        inputStream.close();
        socket.close();
        serverSocket.close();
    }
}
```



例题3：从客户端发送文件给服务端，服务端保存到本地。并返回“发送成功”给客户端。

客户端

- 这里涉及到的异常，应该使用try-catch-finally处理
- 客户端传输完成后，需要调用socket.`shutdownOutput()`;说明数据传输完毕

```java
public class TCPTest3 {
    @Test
    public void client() throws IOException {
        // 1.创建Socket对象，指明服务器端的ip和端口号
        Socket socket = new Socket(InetAddress.getLocalHost(), 8989);
        // 2.获取一个输出流，用于输出数据
        OutputStream outputStream = socket.getOutputStream();
        // 从文件中写出
        FileInputStream fileInputStream = new FileInputStream(new File("test.jpg"));

        // 3.写出数据的操作
        byte[] buffer = new byte[1024];
        int len;
        while (((len = fileInputStream.read(buffer)) != -1)) {
            outputStream.write(buffer, 0, len);
        }
        // 数据传输完毕之后，需要给服务器一个提示
        // 关闭数据的输出
        socket.shutdownOutput();

        // 4.接收来自于服务器端的数据，并显示到控制台上
        InputStream inputStream = socket.getInputStream();
        // 防止乱码使用ByteArrayOutputStream接受
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        byte[] bytes = new byte[20];
        int len2;
        while (((len2 = inputStream.read(bytes)) != -1)) {
            byteArrayOutputStream.write(bytes,0,len2);
        }
        System.out.println(byteArrayOutputStream.toString());

        // 5.资源的关闭
        byteArrayOutputStream.close();
        fileInputStream.close();
        outputStream.close();
        socket.close();
    }
}
```

服务端

```java
public class TCPTest3 {
    @Test
    public void sever() throws IOException {
        // 1.创建服务器端的ServerSocket，指明自己的端口号
        ServerSocket serverSocket = new ServerSocket(8989);
        // 2.调用accept()表示接收来自于客户端的socket
        Socket socket = serverSocket.accept();
        // 3.获取输入流
        InputStream inputStream = socket.getInputStream();
        FileOutputStream fileOutputStream = new FileOutputStream(new File("test2.jpg"));

        // 4.读取输入流中的数据
        byte[] buffer = new byte[1024];
        int len;
        while (((len = inputStream.read(buffer)) != -1)) {
            fileOutputStream.write(buffer, 0, len);
        }

        System.out.println("输出来自：" +
                socket.getInetAddress().getHostAddress() + "的图片");

        // 5. 服务器端给与客户端反馈
        OutputStream outputStream = socket.getOutputStream();
        // 需要转成byte[]类型
        outputStream.write(("你好，已收到来自："
                + socket.getInetAddress().getHostAddress() + "的图片").getBytes());

        // 6.关闭资源
        outputStream.close();
        fileOutputStream.close();
        inputStream.close();
        socket.close();
        serverSocket.close();
    }
}
```

## 四、UDP网络编程

### 1. 简述

- 类`DatagramSocket`和`DatagramPacket`实现了基于UDP协议网络程序。
- UDP数据报通过数据报套接字DatagramSocket发送和接收，系统不保证UDP数据报一定能够安全送到目的地，也不能确定什么时候可以抵达。
- DatagramPacket对象封装了UDP数据报，在数据报中包含了发送端的IP地址和端口号以及接收端的IP地址和端口号
- UDP协议中每个数据报都给出了完整的地址信息，因此无须建立发送方和接收方的连接。如同发快递包裹一样。

### 2. DatagramSocket类常用方法

![image-20200503165828431](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201224110612.png)



![image-20200503165847783](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200503165848.png)

### 3. DatagramSocket类的使用

流程：

1. DatagramSocket与DatagramPacket
2. 建立发送端，接收端
3. 建立数据包
4. 调用 Socket的发送、接收方法
5. 关闭 Socket

注意：发送端与接收端是两个独立的运行程序

**代码示例：**

发送端

```java
public class UDPTest {
    @Test
    public void sender() throws IOException {
        DatagramSocket socket = new DatagramSocket();

        String str = "我是UDP方式发送的导弹";
        byte[] data = str.getBytes();
        InetAddress inet = InetAddress.getLocalHost();
        // DatagramPacket(byte buf[], int offset, int length,InetAddress address, int port)
        DatagramPacket packet = new DatagramPacket(data, 0, data.length, inet, 9090);
        socket.send(packet);
        socket.close();
    }
}
```

接收端

```java
public class UDPTest {
	@Test
    public void receiver() throws IOException {

        DatagramSocket socket = new DatagramSocket(9090);

        byte[] buffer = new byte[100];
        DatagramPacket packet = new DatagramPacket(buffer, 0, buffer.length);
        socket.receive(packet);
        System.out.println(new String(packet.getData(), 0, packet.getLength()));
        socket.close();
    }
}
```

## 五、URL编程

==URL(Uniform Resource Locator)==：统一资源定位符，它表示Internet上某一资源的地址。

- 它是一种具体的URI，即URL可以用来标识一个资源，而且还指明了如何locate这个资源。
- 通过URL我们可以访问 Internet上的各种网络资源，比如最常见的www，ftp站点。浏览器通过解析给定的URL可以在网络上查找相应的文件或其他资源

URL的基本结构由5部分组成:

-  `<传输协议>://<主机名>:<端口号>/<文件名>#片段名?参数列表` 
  - **#片段名**：即锚点；例如看小说，直接定位到章节
  - **参数列表格式**：参数名=参数值&参数名=参数值...
  - 例如： http://192.168.1.100:8080/helloworld/indexjsp#a?username=shkstart&password=123 

### 1. URL类

#### 1.1 构造器

为了表示URL，java.net中实现了类URL。我们可以通过下面的构造器来初始化一个URL对象

- public `URL(String spec)`：通过一个表示URL地址的字符串可以构造一个URL对象。 
  - 例如：URL url = new URL（"http://www.baidu.com/");
- public `URL(URL context,String spec）`：通过基URL和相对URL构造一个URL对象 
  - 例如： URL downloadeUrl = new URL（url,"download.html");
- public `URL(String protocol,String host,String file)`
  - 例如：new URL("http","www.baidu.com",80,"download.html");
- public URL(String protocol,String host,int port,String file)
  - 例如：new URL("http","www.baidu.com",80,"download.html");

注意：URL类的构造器都声明抛出非运行时异常，必须要对这一异常进行处理，通常是用try-catch语句进行捕获。

#### 1.2 方法

- public String getProtocol( )：获取该URL的协议名
- public String getHost( )：获取该URL的主机名
- public String getPort( )：获取该URL的端口号
- public String getPath( )：获取该URL的文件路径
- public String getFile( )：获取该URL的文件名
- public String getQuery( )：获取该URL的查询名

**代码示例**

```java
import java.net.MalformedURLException;
import java.net.URL;

public class URLTest {
    public static void main(String[] args) {
        URL url;
        try {
            url = new URL("http://localhost:8080/examples/beauty.jpg?username=Tom");

            // 1. public String getProtocol()
            // 获取该URL的协议名
            System.out.println(url.getProtocol()); // http

            // 2.public String getHost()
            // 获取该URL的主机名
            System.out.println(url.getHost()); // localhost

            // 3.public String getPort()
            // 获取该URL的端口号
            System.out.println(url.getPort());// 8080

            // 4.public String getPath()
            // 获取该URL的文件路径
            System.out.println(url.getPath());// /examples/beauty.jpg

            // 5.public String getFile()
            // 获取该URL的文件名
            System.out.println(url.getFile());// /examples/beauty.jpg?username=Tom

            // 6.public String getQuery()
            // 获取该URL的查询名
            System.out.println(url.getQuery());// username=Tom

        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
    }
}
```



### 测试：通过URL下载

```java
/**
 * FileName: URLTest1
 * Description: 通过URL下载
 *
 * @create: 2020/12/24 11:05
 * @author Reanon
 */
package URLTest;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.URL;

public class URLTest1 {


    public static void main(String[] args) {
        URL url = null;
        HttpURLConnection urlConnection = null;
        InputStream is = null;
        FileOutputStream fos = null;
        try {
            url = new URL("https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201224110625.png");
            //
            urlConnection = (HttpURLConnection) url.openConnection();
            urlConnection.connect();
            // 输入流
            is = urlConnection.getInputStream();
            // 输出流
            fos = new FileOutputStream("chapter13 net\\src\\URLTest\\20201224110625.png");

            byte[] buffer = new byte[1024];
            int len;
            while ((len = is.read(buffer)) != -1) {
                fos.write(buffer, 0, len);
            }
            System.out.println("下载完成");

        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 关闭资源
            if (is != null) {
                try {
                    is.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (fos != null) {
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (urlConnection != null) {
                urlConnection.disconnect();
            }
        }
    }
}
```



## 六、测试题目

1、一个IP对应着哪个类的一个对象? 实例化这个类的两种方式?

InetAddresse

实例化这个类的两种方式

- InetAddress.getByName(String host);

- InetAddress.getLocalHost();//获取本地ip

两个常用的方法是?

- getHostName();
- getHostAddress();

2、传输层的TCP协议和UDP协议的主要区别是?

TCP：

- 可靠的数据传输(三次握手);进行大数据量的传输;效率低,

UDP：

- 不可靠;以数据报形式发送，数据报限定为64k;效率高·

3、什么是URL，你能写一个URL 吗?

- URL:统一资源定位符URL url = new 
- URL("http://192.168.14.100:8080/examples/hello.txt?username=Tom");

4、谈谈你对对象序列化机制的理解？

序列化机制

反序列化机制



5、对象要想实现序列化，需要满足哪几个条件？

1. 实现接口:Serializable标识接口。

2. 对象所在的类提供常量:序列版本鄂IF
3. 要求对象的属性也必须是可序列化的。(基本数据类型、string:本身就已经是可序列化