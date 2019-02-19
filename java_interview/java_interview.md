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

HTTP无连接（传送完即断开连接）无状态

输入URL回车后：
1. DNS解析
2. TCP连接
3. HTTP请求
4. HTTP响应
5. 浏览器解析
6. 关闭连接

GET和POST的区别：
1. GET放在URL，POST放在报文体
2. 数据库：GET幂等性和安全性，POST无
3. GET可以被缓存，POST不行

SESSION的实现方式：
1. Cookie实现，回复给客户端一个sessionId做cookie
2. URL回写

HTTPS数据传输流程：
1. ........


# 4. Redis

Memcache:
1. 类似hash
2. 只有简单数据类型
3. 不能持久化
4. 不支持主从
5. 不支持分片

为什么redis快：
1. 内存
2. 数据结构简单
3. 单线程，没有上下文切换，锁竞争
	- CPU一般不是Redis的性能瓶颈，一般都是网络因素
4. 多路IO复用(selector)，非阻塞IO
	- 复用函数epoll/kqueue/evport/select
	- 优先选择o(1)的函数
	- o(n)的函数保底
	- 基于react设计模式监听IO事件

redis数据类型：
1. String： 二进制安全，最长512M	
	```
	字符串对象的结构
	struct sdshdr {
		int len; //buf 中已用的长度
		int free; //buf 中可用的长度
		char buf[]; //数据空间
	}	
	```
2. Hash 
3. List（有序）
4. Set（无序），可求交并差集
5. Sorted Set，有序，排序依据是分数
6. HyperLogLog, Geo

