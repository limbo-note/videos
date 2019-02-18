# 2. 网络

ip+协议+端口（socket)，标识网络中的一个进程

三次握手：
syn . syn+ack.  ack  
意义：
获得初始化的seq号；  
syn flood：客户端不断发送syn但不回复syn+ack，造成服务端syn队列满，无法正常连接（解决：syn cookies）

time_wait的意义：
让服务端有足够时间收到ack；
避免和新连接混淆

为什么挥手要四次：
全双工，客户和服务都得发送fin和ack

大量close_wait（占用文件句柄 to many open files）：
对方关闭连接，fin请求收到，但本方未关闭（未正常关闭资源）。所以一直是close_wait，仍然可以发数据给对方

UDP面向报文，不作拆分合并

TCP滑动窗口





