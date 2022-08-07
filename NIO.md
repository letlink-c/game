# 网络编程

## Socket

.getByte() 字符串转换为字节

socket是传输层和应用层之间的连接

 Socket套接字是通信的基石，是支持TCP/IP协议的网络通信的基本操作单元

服务端监听端口，客户端发送消息，收到客户端的消息并输出

服务端serversocket

文件传输：

用输入流将文件读入内存

用输出流将文件输出到socket

用输出流先为文件预留位置

用输入流将socket从网卡输入到内存

用输出流将内存中的文件输出到硬盘

udp:

DatagramPacket

# IO

## File 类

https://blog.csdn.net/ai_bao_zi/article/details/81015141

其实现了Serializable, Comparable两大接口以便于其对象可序列化和比较

可获得上级目录，文件名等

输入：文件输入内存（java程序） 输出：内存输出到文件

io流不关的话会一直占用内存空间，直到jvm结束



![这里写图片描述](https://img-blog.csdn.net/20160522165107051)

文件流

File类给我们提供了抽象的表示File.separator，屏蔽了系统层的差异

## InputStream 

InputStream：字节输入流基类  抽象类

InputStream extends Object implements Closeable

file:

read()读取一个字节的数据 返回-1表示读完了

将流放入i 判断

```
while ((i=inputStream.read())!=-1){
    System.out.println((char) i);
}
```

## outputStream

`OutputStream`是输出流的抽象，它是将程序内存中的数据写入到目的地（也就是接收数据的一端）

System.out是一个PrintStream流

file：

```
FileOutputStream outputStream = new FileOutputStream("C:a.txt");
```

向文件中写入字符

```
outputStream.write('a');
```

写入字符串

```
outputStream.write("dwxcc".getBytes(StandardCharsets.UTF_8));
```

限长写入，从第二个字符g 写两位 gv

```
outputStream.write("tgvbdwsc".getBytes(StandardCharsets.UTF_8),1,2);
```

## 文件复制 io版

```
FileInputStream inputStream = new FileInputStream("C:a.txt");
FileOutputStream outputStream = new FileOutputStream("C:b.txt");
byte[] bytes = new byte[1024];
int a = 0;
while ((a=inputStream.read(bytes))!=-1){
    outputStream.write(bytes,0,a);
}
inputStream.close();outputStream.close();
```

处理流

## BufferedReader 

按行读取 效率高  读取文件内容，输出到控制台

```
BufferedReader reader = new BufferedReader(new FileReader("C:\\Users\\admin\\Desktop\\Projects\\cloud-ali\\demo\\src\\main\\java\\com\\example\\poo\\file1\\a.txt"));
String el;
while ((el = reader.readLine()) != null){
    System.out.println(el);
}
reader.close();
```

## BufferedWriter

0 代表从第0个开始，3代表向后数3个字符串

```
writer.write("dcc",0,3);
 writer.newLine(); 换行
```

```
while ((el = reader.readLine()) != null){
    writer.write(el);
    writer.newLine();
}
```

## 对象处理流 序列化

ObjectInputStream  ObjectoutputStream 

```
ObjectOutputStream os = new ObjectOutputStream(new FileOutputStream("C:\\Users\\admin\\Desktop\\Projects\\cloud-ali\\demo\\src\\main\\java\\com\\example\\poo\\file2\\n.txt"));
os.write
```

什么都能输入

序列化：

```
ObjectOutputStream os = new ObjectOutputStream(new FileOutputStream("C:\\Users\\admin\\Desktop\\Projects\\cloud-ali\\demo\\src\\main\\java\\com\\example\\poo\\file2\\n.txt"));
os.writeObject(UDPsocket.class);
```

指定格式保存 为自定义后缀

反序列化

```
Object o = inputStream.readObject();
System.out.println(o.getClass());
```

字符流处理纯文本数据



# NIO 

https://blog.csdn.net/zcw4237256/article/details/78662762



## 概念

BIO 阻塞io

thread-》write，read-》socket

如果有100个线程，而有100个用户在下载，第101个用户的请求会无法处理

客户端有连接请求时服务器需要启动一个线程进行处理，如果线程没有炒作时会造成不必要的开销

一个线程一个读写io，一个socket

非阻塞io

thread-》selector-》channel（write，read）-》buffer-》socket

一个线程通过一个选择器操作多个io，每个io一个socket

io事件本身不阻塞，获取io事件的方法需要阻塞

非阻塞是面向缓冲区编程，使用selector实现nio

当用户线程发起一个read操作后，并不需要等待，而是马上就得到了一个结果。如果结果是一个error时，它就知道数据还没有准备好，于是它可以再次发送read操作。一旦内核中的数据准备好了，并且又再次收到了用户线程的请求，那么它马上就将数据拷贝到了用户线程，然后返回。

   所以事实上，在非阻塞IO模型中，用户线程需要不断地询问内核数据是否就绪，也就说非阻塞IO不会交出CPU，而会一直占用CPU

io核心：

Channels

Buffers

Selectors

系统流：

```
byte[] data = new byte[10];
System.in.read(data);
System.out.println();
for (int i = 0; i < data.length; i++) {
    System.out.println((char)[i]);
}
```

## 各种io理解

同步阻塞IO， 其实就是服务端创建一个ServerSocket， 然后就是客户端用一个Socket去连接服务端的那个ServerSocket， ServerSocket接收到了一个的连接请求就创建一个Socket和一个线程去跟那个Socket进行通讯。接着客户端和服务端就进行阻塞式的通信，客户端发送一个请求，服务端Socket进行处理后返回响应，在响应返回前，客户端那边就阻塞等待，上门事情也做不了。 这种方式的缺点， 每次一个客户端接入，都需要在服务端创建一个线程来服务这个客户端，这样大量客户端来的时候，就会造成服务端的线程数量可能达到了几千甚至几万，这样就可能会造成服务端过载过高，最后崩溃死掉


## 核心组件关系

BIO基于字节流和字符流进行操作，nio基于channel和buffer操作 buffer通过file可实现双向操作

thread-》selector-》channel（write，read）-》buffer-》socket

## Channel 

channel 是通道 读取与写入 双工

- FileChannel 从文件读写数据
- SocketChannel 通过TCP读写网络数据
- ServerSocketChannel 可以监听新进来的TCP连接，并对每个链接创建对应的SocketChannel
- DatagramChannel 通过UDP读写网络中的数据

### FileChannel

读操作：将通道数据读到缓冲区

写操作：将缓冲区的数据写入通道

buf.flip 读写转换

```
/**
 * 创建文件流 channel通道
 * 创建字符缓冲区
 * 将数据读取到channel中 当其不等于负一时代表没读完
 * flip读写转换 写完了后开始读取
 * byteBuffer.hasRemaining()表示里面是否有剩余内容
 * 通过get取出字符 char转换
 */
```



```
public static void main(String[] args) throws IOException {
    RandomAccessFile file = new RandomAccessFile("a.txt","rw");
    FileChannel channel = file.getChannel();
    ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
    int bytesRead = channel.read(byteBuffer);
    while (bytesRead != -1){
        System.out.println( bytesRead);
        byteBuffer.flip();
        while (byteBuffer.hasRemaining()){
            System.out.println((char) byteBuffer.get());
        }
        byteBuffer.clear();
        bytesRead = channel.read(byteBuffer);
    }
    file.close();
}
```

向txt写入数据 替换式写入

不能之间向channel写数据，要写入buffer再写入channel

```
RandomAccessFile file = new RandomAccessFile("C:\\Users\\admin\\Desktop\\Projects\\cloud-ali\\demo\\src\\main\\java\\com\\example\\poo\\file1\\a.txt", "rw");
FileChannel channel = file.getChannel();
ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
String a = "data";
byteBuffer.clear();
byteBuffer.put(a.getBytes(StandardCharsets.UTF_8));
byteBuffer.flip();
while(byteBuffer.hasRemaining()){
    channel.write(byteBuffer);
}
channel.close();
```

position: 调用position方法在文件特定位置进行读写

![image-20220402160125043](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220402160125043.png)

在**ASCII码**编码方案中，一个英文字符占用一个字节，一个汉字字符占用两个字节的空间；二：在**Unicode编码**方案中，一个英文字符或一个汉字字符都占用两个字节的空间；三：在UTF-8编码方案中，一个英文字符占用一个字节，一个汉字字符占用三个字节的空间

![image-20220402161054653](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220402161054653.png)

![image-20220402161117263](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220402161117263.png)

```
RandomAccessFile file = new RandomAccessFile("C:\\Users\\admin\\Desktop\\Projects\\cloud-ali\\demo\\src\\main\\java\\com\\example\\poo\\file1\\a.txt", "rw");
RandomAccessFile file1 = new RandomAccessFile("C:\\Users\\admin\\Desktop\\Projects\\cloud-ali\\demo\\src\\main\\java\\com\\example\\poo\\file1\\b.txt","rw");
FileChannel channel = file.getChannel();
FileChannel channel1 = file1.getChannel();
channel1.transferFrom(channel,0,channel.size());
channel1.close();
channel.close();
```

```
channel.transferTo(0,channel.size(),channel1);
channel1.transferFrom(channel,0,channel.size());
大小为写入者的大小
```

### ServerSocketChannel

ServerSocketChannel 是一个可以监听新进来的TCP连接的通道

可阻塞式监听和非阻塞式监听

```
开启监听
ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
设置监听端口
serverSocketChannel.socket().bind(new InetSocketAddress(9999));

```

阻塞式接收

```
while(true){
    SocketChannel socketChannel =
            serverSocketChannel.accept();
    System.out.println(socketChannel.socket().getRemoteSocketAddress());
}
```

非阻塞： 非阻塞会使cpu占用过高

```
while(true){
    SocketChannel socketChannel =
            serverSocketChannel.accept();
             if(socketChannel != null){
       System.out.println(socketChannel.socket().getRemoteSocketAddress());
    }
   
}
```

### SocketChannel

SocketChannel是一个连接到TCP网络套接字的通道，它是Socket类的对等类，通常SocketChannel用在客户端以向服务器发起 
连接请求。每个SocketChannel对象创建时都关联一个对等的Socket对象

![image-20220403160447270](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220403160447270.png)

![image-20220403160506850](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220403160506850.png)

```
SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1",9999));
 ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
 byteBuffer.put(new String("dwx").getBytes(StandardCharsets.UTF_8));
 byteBuffer.flip();
 socketChannel.configureBlocking(true);
 while(byteBuffer.hasRemaining()){
     socketChannel.write(byteBuffer);
 }
```

### DatagramChannel

DatagramChannel是一个能收发UDP包的通道,其底层实现为DatagramSocket+Selector。DatagramChannel可以调用socket()方法获取对等DatagramSocket对象

一个未绑定具体的端口DatagramChannel仍可以接受数据包，当一个底层的socket被创建时，一个随机的端口号被分配给他

upd 数据传输：

```
public static void sendData() throws IOException {
    DatagramChannel channel = DatagramChannel.open();
    ByteBuffer buffer = ByteBuffer.wrap("dw对我说".getBytes(StandardCharsets.UTF_8));
    InetSocketAddress address = new InetSocketAddress("127.0.0.1",7000);
    channel.send(buffer,address);
}
```

接 收

```
public static void receiveData() throws IOException {
    DatagramChannel channel = DatagramChannel.open();
    channel.socket().bind(new InetSocketAddress(7000));
    ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
    while (true){
        byteBuffer.clear();
        SocketAddress address = channel.receive(byteBuffer);
        byteBuffer.flip();
        System.out.println(address.toString());
        System.out.println(StandardCharsets.UTF_8.decode(byteBuffer));
    }

}
```

### 分散聚集

![image-20220404134150789](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220404134150789.png)

## Buffer 缓冲区

https://blog.csdn.net/czx2018/article/details/89502699

![image-20220404134850265](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220404134850265.png)

**传统阻塞型I/O的问题**

传统的网络I/O编程是ServerSocket的accept方法一直等待着TCP请求的接入，每当收到一个TCP请求后，ServerSocket就会创建出一组I/O流，把它们交给一个线程去处理

1.假设访问的高峰期[并发](https://so.csdn.net/so/search?q=并发&spm=1001.2101.3001.7020)量较大，我们必须为程序配置一个较大的线程池，可是当过了高峰期，并发量减少了，那些空闲的线程岂不是一种资源的浪费？
2.当I/O流打开以后如果数据传输并不频繁，那么这个线程很大一部分时间都是在等待中度过的，这又是一种资源的利用率低下



nio让更少的线程能够处理更多的事情

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190424215349475.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2N6eDIwMTg=,size_16,color_FFFFFF,t_70)

Buffer(缓冲区)的底层其实就是一个数组，它提供了一些方法来向数组中写入和读出数据



Buffer的几个常用方法：

allocate() - 初始化一块缓冲区
put() - 向缓冲区写入数据
get() - 向缓冲区读数据
filp() - 将缓冲区的读写模式转换
clear() - 这个并不是把缓冲区里的数据清除，而是利用后来写入的数据来覆盖原来写入的数据，以达到类似清除了老的数据的效果
compact() - 从读数据切换到写模式，数据不会被清空，会将所有未读的数据copy到缓冲区头部，后续写数据不会覆盖，而是在这些数据之后写数据
mark() - 对position做出标记，配合reset使用
reset() - 将position置为标记值



Buffer的几个核心属性：

capacity - 缓冲区大小，缓冲区一旦创建出来以后，这个属性就不会再变化了
position - 读写数据的定位指针，用来标识当前读取到了哪一个位置。
limit - 读写的边界，用于限制指针的最大指向位置。当指针走到边界上的时候就要停住，否则就会抛出BufferUnderflowException



使用buffer

创建缓冲区 ，写数据到buffer ， 读写转换，读数据，调用clear以便再次接收

读写文件

```
RandomAccessFile file = new RandomAccessFile
```

```
FileChannel channel = file.getChannel();
```

```
ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
channel.read(byteBuffer);
byteBuffer.flip();
while (byteBuffer.hasRemaining()){
    System.out.println((char) byteBuffer.get());
}
file.close();
```

### file方法 clear方法

直接打印byytebuffer

java.nio.HeapByteBuffer[pos=0 lim=5 cap=1024]
d
java.nio.HeapByteBuffer[pos=1 lim=5 cap=1024]
w
java.nio.HeapByteBuffer[pos=2 lim=5 cap=1024]
o
java.nio.HeapByteBuffer[pos=3 lim=5 cap=1024]
k
java.nio.HeapByteBuffer[pos=4 lim=5 cap=1024]
c

pos代表的就是`position`属性，lim代表`limit`属性，cap代表`capacity`属性

在缓冲区刚初始化出来的时候，position指向的是数组的第一个位置，limit和数组的容量一样。

用图片来表示大概就如下图

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190424230709653.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2N6eDIwMTg=,size_16,color_FFFFFF,t_70)

向缓冲区中每写入一个数据，指针就会后移一位

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190424231635223.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2N6eDIwMTg=,size_16,color_FFFFFF,t_70)

当调用了flip()方法后，position又会重新指向数组的第一个位置，而limit会指向原来的position的位置


intBuffer.hasRemaining()方法就是判断position < limit

### 读写数据

放入什么类型就要用什么类型取出

写

channel.read(buffer)

buffer.put()

读到channel

```
channel.write()
```

### MappedByteBuffer

可以让文件在内存（堆外的内存）修改

操作系统不需要拷贝

```
RandomAccessFile file
```

```
FileChannel channel
```

表示读写模式，从索引0开始，修改5个字节

```
MappedByteBuffer byteBuffer = channel.map(FileChannel.MapMode.READ_WRITE,0,5);
```

### 分散聚集























## Selector

Selector 一般称 为多路复用器 。它是Java NIO核心组件中的一个，用于检查一个或多个NIO Channel（通道）的状态是否处于可读、可写

使用 Selector, 我们首先需要将 Channel 注册到 Selector 中, 随后调用 Selector 的 select()方法, 这个方法会阻塞, 直到注册在 Selector 中的 Channel 发送可读写事件（或其它注册事件）. 当这个方法返回后, 当前的这个线程就可以处理 Channel 的事件了


select监控注册的通道，当有io时将对应的key加入到集合中并返回，可设置超时时间

channel注册成功后返回key

filechannel无法注册到selector

步骤：

创建channel selector 将channel设置非阻塞，

使用 Channel.register()方法 注册

```
Selector selector = Selector.open();
ServerSocketChannel socketChannel = ServerSocketChannel.open();
socketChannel.configureBlocking(false);
socketChannel.bind(new InetSocketAddress(9999));
socketChannel.register(selector, SelectionKey.OP_ACCEPT);
```

Connect, 连接事件(TCP 连接), 对应于

SelectionKey.OP_CONNECT **连接就绪事件，表示客户与服务器的连接已经建立成功**
SelectionKey.OP_ACCEPT  **接收连接进行事件，表示服务器监听到了客户连接，那么服务器可以接收这个连接了**
SelectionKey.OP_READ, **读就绪事件，表示通道中已经有了可读的数据，可以执行读操作了（通道目前有数据，可以进行读操作了）**
SelectionKey.OP_WRITE,**写就绪事件，表示已经可以向通道写数据了（通道目前可以用于写操作）**
用或运算可以组合事件

通过 SelectionKey 获取相对应的 Channel 和 Selector

---------------

Select会等待通道注册 selector.select()判断通道事件发生

调用select方法，返回有事件发生个数 SelectionKey返回集合

通过key反向获取channel

```
next.isAcceptable() 和 accept
```

一个不知道会有连接来，需要等待，一个知道连接来了，不用等待

channel发生连接，可读事件

```
// 取消注册
key.cancel();
```

可以通过key得到channel

# 聊天室

服务端：

定义属性：selector serversocketchannel 

```
package com.example.poo.nio;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.Iterator;

public class GroupChatServer {

    // 定义需要用到属性
    private Selector selector;
    private ServerSocketChannel listenChannel;
    private static final int PORT = 6666;

    /**
     * 构造器，进行初始化
     */
    public GroupChatServer() {
        try {
            // 得到选择器
            selector = Selector.open();
            // 得到ServerSocketChannel
            listenChannel = ServerSocketChannel.open();
            // 绑定端口
            listenChannel.socket().bind(new InetSocketAddress(PORT));
            // 设置非阻塞模式
            listenChannel.configureBlocking(false);
            // 将listenChannel注册到selector上
            listenChannel.register(selector, SelectionKey.OP_ACCEPT);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 监听
     */
    public void listen() {
        try {
            while (true) {
                int count = selector.select();
                // count > 0 就表示有事件要处理
                if (count > 0) {
                    // 拿到selectionKey的集合
                    Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
                    while (iterator.hasNext()) {
                        // 取出selectionKey
                        SelectionKey key = iterator.next();
                        // 监听到不同的事件进行不同的处理
                        listenHandler(key);
                        // 删除当前key，防止重复处理
                        iterator.remove();
                    }
                } else {
                    System.out.println("当前没事件要处理，等待中……");
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }


    /**
     * 针对监听到的不同事件做不同的处理
     * @param key
     * @throws Exception
     */
    public void listenHandler(SelectionKey key) {
        SocketChannel sc = null;
        try {
            // 如果是连接事件
            if (key != null && key.isAcceptable()) {
                // 拿到socketChannel并注册到selector上
                sc = listenChannel.accept();
                sc.configureBlocking(false);
                sc.register(selector, SelectionKey.OP_READ);
                // 提示：xxx上线了
                System.out.println(sc.getRemoteAddress().toString().substring(1) + "上线了");
            }
            // 如果是读取事件
            if (key != null && key.isReadable()) {
                // 拿到socketChannel
                sc = (SocketChannel) key.channel();
                sc.configureBlocking(false);
                // 创建buffer
                ByteBuffer buffer = ByteBuffer.allocate(1024);
                // 将客户端消息读到buffer中去
                int count = sc.read(buffer);
                if (count > 0) {
                    // 如果读取到了数据，就把缓冲区数据转成字符串输出
                    String msg = new String(buffer.array());
                    System.out.println("from client " + msg);
                    // 将该消息转发给其他客户端，
                    sendMsgToClient(msg, sc);
                }
            }
        } catch (IOException e) {
            try {
                System.out.println(sc.getRemoteAddress().toString().substring(1) + "离线了");
                // 取消注册
                key.cancel();
                // 关闭通道
                sc.close();
            } catch (IOException e1) {
                e1.printStackTrace();
            }
        }
    }


    /**
     * 发送消息到客户端，要排除当前客户端。
     * 假如A、B、C三个客户端，当前消息是A发来的，那么只转发给B和C就可以。
     * @param msg
     * @param self
     * @throws IOException
     */
    public void sendMsgToClient(String msg, SocketChannel self) throws IOException {
        System.out.println("服务器转发消息中");
        // 遍历所有注册到selector上的SocketChannel，并排除自己
        for (SelectionKey key : selector.keys()) {
            Channel channel = key.channel();
            if (channel instanceof SocketChannel && channel != self) {
                SocketChannel targetChannel = (SocketChannel) channel;
                // 将msg写到buffer中
                ByteBuffer buffer = ByteBuffer.wrap(msg.getBytes());
                // 将buffer数据写入通道
                targetChannel.write(buffer);
            }
        }
    }


    public static void main(String[] args) {
        GroupChatServer server = new GroupChatServer();
        server.listen();
    }
}
```

```
package com.example.poo.nio;

import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.SocketChannel;
import java.util.Iterator;
import java.util.Scanner;
import java.util.concurrent.TimeUnit;

public class GroupChatClient {

    // 定义相关属性
    private final String HOST = "127.0.0.1";
    private final int PORT = 6666;
    private Selector selector;
    private SocketChannel socketChannel;
    private String username;

    /**
     * 构造器，进行初始化
     */
    public GroupChatClient() {
        try {
            selector = Selector.open();
            // 连接服务器
            socketChannel = SocketChannel.open(new InetSocketAddress(HOST, PORT));
            // 设置非阻塞
            socketChannel.configureBlocking(false);
            // 将socketChannel注册到selector上
            socketChannel.register(selector, SelectionKey.OP_READ);
            // 拿到username
            username = socketChannel.getLocalAddress().toString().substring(1);
            System.out.println(username + " is ok");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 向服务端发送消息
     *
     * @param msg
     */
    public void sendMsgToServer(String msg) {
        msg = username + "：" + msg;
        try {
            // 发送消息
            socketChannel.write(ByteBuffer.wrap(msg.getBytes()));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 接收服务端发来的消息
     */
    public void acceptMsgFromServer() {
        try {
            int readChannel = selector.select();
            if (readChannel > 0) { // 有可用的通道
                Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
                while (iterator.hasNext()) {
                    SelectionKey key = iterator.next();
                    if (key.isReadable()) {
                        // 获取到通道
                        SocketChannel sc = (SocketChannel) key.channel();
                        // 创建buffer
                        ByteBuffer buffer = ByteBuffer.allocate(1024);
                        // 将通道中的数据读到buffer中
                        sc.read(buffer);
                        // 将buffer中的数据转成字符串
                        String msg = new String(buffer.array());
                        System.out.println(msg.trim());
                        // 移除当前的key，防止重复操作
                        iterator.remove();
                    }
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        // 1. 启动客户端
        GroupChatClient client = new GroupChatClient();
        // 2. 启动线程，每隔3秒就读取一次服务端的消息
        new Thread(() -> {
            while (true) {
                client.acceptMsgFromServer();
                try {
                    TimeUnit.SECONDS.sleep(1); } catch (Exception e) { e.printStackTrace();}
            }
        }).start();
        // 3. 发送数据到服务端
        @SuppressWarnings("resource")
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNextLine()) {
            String msg = scanner.nextLine();
            client.sendMsgToServer(msg);
        }
    }
}
```
