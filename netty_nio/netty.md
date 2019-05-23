[TOC]

channel在注册到EventLoop上时，会触发handler的channelRegistered()方法，在其中会对initChannel()方法进行调用。所以，一般都是先将一个ChannelInitializer先加入至channel中，在channel注册时触发initChannel()将其他Handler也加入

#### Netty线程模型

Netty4 无锁串行化设计，即每个channel都只有一个EventLoop和线程对之处理，避免了线程切换带来的开销

#### ServerBootStrap启动流程分析

AbstractBootstrap、ServerBootstrap、Bootstrap类图（其中有ServerBootstrap的内部类ServerBootstrapAcceptor）

![](1.jpg)

ServerBootstrap的init(Channel)方法中（注意加入ServerBootstrapAcceptor所用的方法）：

```java
p.addLast(new ChannelInitializer<Channel>() {
    @Override
    public void initChannel(Channel ch) throws Exception {
        final ChannelPipeline pipeline = ch.pipeline();
        ChannelHandler handler = config.handler();
        if (handler != null) {
            pipeline.addLast(handler);
        }

        // We add this handler via the EventLoop as the user may have used a ChannelInitializer as handler.
        // In this case the initChannel(...) method will only be called after this method returns. Because
        // of this we need to ensure we add our handler in a delayed fashion so all the users handler are
        // placed in front of the ServerBootstrapAcceptor.
        ch.eventLoop().execute(new Runnable() {
            @Override
            public void run() {
                pipeline.addLast(new ServerBootstrapAcceptor(
                        currentChildGroup, currentChildHandler, currentChildOptions, currentChildAttrs));
            }
        });
    }
});
```

启动时序图如下:

![](2.jpg)

#### NioEventLoop开启和处理accept事件

NioEventLoop中都包含一个selector，channel注册到的selector指的就是NioEventLoop中的selector

上述时序图可概括为：

1. ServerBootstrap开启bind事件，init进行初始化，生成通道，设置参数等
2. register过程中，会开启NioEventLoop的线程
3. 线程处理task，即对应图中的execute(Runnable)，其中一个task就是doRegister()，将通道注册到selector
4. 由于channel是先注册到NioEventLoop上（即先执行doRegister()任务，先被注册到selector上），才会触发channel上ChannelInitializer的initChannel方法，才会执行 将ServerBootstrapAcceptor加入pipeline的任务

NioEventLoop线程启动的详细过程：

![](3.jpg)

在ServerBootstrapAcceptor中，其`channelRead(ChannelHandlerContext ctx, Object msg)`方法传入的消息的类型其实就是一个Channel类型，代表accept之后获取到的SocketChannel。之后只需要类似的，将childHandler加入其pipeline，并且将其注册到childGroup上（过程类似parentGroup，会将channel注册到对应的selector上去）即可

#### NioEventLoop接收客户端请求过程

数据在ChannelHandler中的流动过程时序图：

#### 读半包处理分析

分析ByteBuf类型的cumulation变量，看源码好像是只支持连续两次的拼接，可测试下发三次会怎么样