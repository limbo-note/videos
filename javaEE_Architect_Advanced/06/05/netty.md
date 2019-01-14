# 1. NIO

### 传统IO和NIO比较

- 传统IO
	- 由于read()方法的阻塞，单线程情况下，accept()方法很可能不能发挥作用，通常只能有一个客户端
	- 用线程池为每一个accept()监听到的连接都分配一个线程，可以有多个客户端，但很消耗性能
- NIO
	- ServerSocketChannel对应传统IO的ServerSocket
	- SocketChannel对应传统IO的Socket
	- Selector，NIO核心部分。传统IO的单线程中，accept()方法必须等待read()方法完成才会继续工作，若客户端不发送数据则一直阻塞在read()方法；而单线程NIO的selector的select方法可同时监听这两类的消息（**此为IO多路复用？？？？**）（通过SelectionKey区分），所以可以在完成连接后马上返回select()方法继续监听，不需要阻塞在read()方法等待客户端的数据
	- SelectionKey，分两种类型的key，acceptable和readable，分别对应客户端的连接和数据发送
	- select方法默认情况下还是阻塞的，可以指定立即返回或者设定在某个时间内返回
- 注意事项
	- read()方法在客户端关闭时也会被触发，此时read()方法返回-1，应对read()方法做一个返回值判断 

# 2. Netty服务端

服务类->boss线程池监听端口，worker线程池负责数据读写->设置niosocket工厂->设置管道工厂

```
SimpleChannelHandler 处理消息接收和写{

	messageReceived()接收消息

	channelConnected()新连接，通常用来检测IP是否是黑名单

	channelDisconnected()链接关闭，可以再用户断线的时候清楚用户的缓存数据等
}
```

# 3. Netty客户端

与服务端写法类似

- channelDisconnected与channelClosed的区别
	- channelDisconnected只有在连接建立后断开才会调用
	- channelClosed无论连接是否成功都会调用关闭资源

# 4. Netty线程模型源码分析

看源码的一种**参考方法**：写一个简单的框架使用的程序，依赖不用jar包导入，而是导入源码工程；在源码内加入一些打印或断点，运行自己写的使用程序，一句句跟踪调试，观察打印；必要时查看调用栈

一个NIO系统可以有多个selector，每个selector可以注册多个ServerSocketChannel（用于监听不同serverSocket，即不同端口），也可以注册多个SocketChannel(用于接收不同的数据发送请求)

Netty中每个线程对应一个selector

NettyIO、单线程NIO和单线程传统IO的比较如下：
- NettyIO										
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect_Advanced/06/05/4-1.png)
- 单线程NIO										
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect_Advanced/06/05/4-2.png)
- 单线程传统IO										
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect_Advanced/06/05/4-3.png)

# 5. Netty5的简单使用
# 6. Netty心跳检测使用

主要使用idleStateHandler类，自动对读写状态进行监测，长时间不读写时，可进行一个断开操作

# 7. Protocol buff序列化框架

类似Avro的一个序列化框架，有自己的语法定义

一般步骤：
1. 定义.proto文件，其中描述了要生成的类的各个字段等。语法规则查资料
2. 用protoc.exe程序解析.proto文件，生成对应的java代码
3. 生成的java代码，每个类一般都有一个构造器builder
4. 获取到相应的构造器builder，设置字段值并build()构造
5. 构造出的类，可直接调用toByteArray()方法将其序列化
6. 用类的静态方法parseFrom(byte[])将字节数组反序列化成类对象

java自带的序列化协议一般都比较冗长，不易传输；Protocol buff\avro等序列化框架相当于把类的一些其它信息都写在的配置文件里面（代价是双方都需要用同一个配置文件来对字节进行解析，而java自带的序列化协议则不需要），精简了序列化后的字节长度

# 8. 序列化协议

### 8.1 序列化基础

- 最基础的序列化操作，就是自己写位运算方法。 缺点：太繁琐，容易出错
- ByteBuffer的putInt()等方法，自动转成byte字节；通过getInt()等方法转换回去。 缺点：需要指定空间大小，超出时会异常
- Netty封装好的ChannelBuffer，可动态扩展空间，通过writeXXX()方法转换成字节数据，通过writerIndex()方法获得空间大小，通过readXXX()方法读取字节数据

### 8.2 protocol buff序列化协议

以int型举例：每7位数据获取一次，若不为0，则序列化进结果数组。所以按照int类型所表示的数据的大小不同，最后的结果数组可能会占到1~5位，而通常小范围的数据用的比较多，所以通常能省空间。 long类型的数据类似

# 9. Netty自定义数据包协议

give me a coffee   give me a tea

**粘包现象**：give me a coffeegive me a tea

**分包现象**：  
give me   
a coffeegive me a tea   

粘包和分包出现的原因是：没有一个稳定数据结构

解决方法：
- 分割符。 give me a coffee|give me a tea|
- 长度 + 数据，定义数据包结构

(......)
 
# 10. 粘包分包分析，避免socket攻击
# 11. 聊天室小项目