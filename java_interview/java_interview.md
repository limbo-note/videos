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

redis实现分布式锁：
1. setnx实现互斥，expire设置过期时间（即释放锁）
	```
	long status = jedis.setnx(key, "xx");
	
	if(status == 1){
		jedis.expire(key, expire);
		doJob();
	}
	```
	缺点：setnx和expire分开执行了，破坏了原子性，可能会造成expire不执行，发生死锁
2. 避免以上缺点，将set和expire操作合成原子性，即在set操作里设置过期时间
	```
	SET key value [EX sec] [PX] [NX|XX]
	```

大量key同时过期，清除大量key很耗时，会短暂阻塞，解决：
1. 随机设置key过期时间

redis做异步队列：
1. rpush和lpop
	- 缺点：不会等待，调用pop即立即消费；可在应用程序里sleep做控制
	- blpop key [timeout]：阻塞timeout秒直到队列里有数据才返回
	- 只能一个消费者消费，多个消费者可使用订阅模式

redis持久化rdb：
1. bgsave是fork子进程进行备份，并利用了写时复制，即：
	- 多个调用者调用相同的资源（内存或外存等），则共同获取相同的指针，直到某个调用者试图修改资源，系统才会真正复制一个副本给该调用者。所以子进程在备份时，父进程对数据进行的修改，都是在副本里修改的，子进程相当于拥有一个快照
2. 缺点：全量备份，数据大时，由于I/O会严重影响性能；只保存了最后一次快照的数据，之后的数据没有备份到

aof重写备份文件，**见书**
 
主从同步的详细步骤，**见书（全量同步，增量同步）**

哨兵：解决主从同步的master宕机的主从切换问题（流言协议，投票确定新的master）

分片，以节点数为模确定数据应该在的节点；无法动态变化

集群，其实就是相比分片，在hash时采用了**一致性hash算法**（哈希环，考虑增减服务器所带来的影响？？），以达到动态扩展的目的。
哈希环数据倾斜问题：节点少，且在环上
分布不均匀，这时候数据就会集中在某一节点上（引入虚拟节点使其分布均匀，解决此问题） 


# 5. Linux

用户态和内核态：
1. 内核本质是一段管理计算机硬件的程序
2. 内核的访问接口就是系统调用，不能再简化
3. 系统调用的组合就是公用函数库
4. shell是命令解释器

查找特定文件： find path [options] params, 如：
1. `find / -name "xxx.java"`(-iname 不区分大小写)
2. `man find`查看手册

检索文件内容：
1. `grep [options] pattern file`
2. `grep -o`只输出匹配的那部分内容，而不是整行

管道注意点：
1. 右边命令只处理左边命令的正确输出，不处理错误输出
2. 右边命令必须能够接受标准输入流，否则数据流被丢弃

统计命令：
1. `awk`，将每行按照分割符做切片，`-F`指定分隔符
2. 统计每行出现的次数：`| awk '{arr[$1]++}END{for(i in arr)print i "\t" arr[i]}'`

批量替换文本内容：
1. `sed`，`sed -i 's/Str/String/g xxx.java`

# 6. Java

### java的理解
1. 平台无关性
2. GC
3. 语言特性：泛型反射等
4. 面向对象
5. 类库
6. 异常处理

### 平台无关性
1. javac编译成字节码，javap可反编译，java运行class文件
2. java提供了不同平台的jvm，不同jvm对同一字节码文件生成不同的执行指令

### 反射
在运行状态中，对任意一个类，都能动态获取其属性和方法；对任意一个对象，都能动态调用其属性和方法，例如：
 	
```
public class Robot {
	private String name;
	public void sayHi(String helloSentence){
	    System.out.println(helloSentence + " " + name);
	}
	private String throwHello(String tag){
	    return "Hello " + tag;
	}
	static {
	    System.out.println("Hello Robot");
	}
}
```
```
Class rc = Class.forName("com.interview.javabasic.reflect.Robot");
Robot r = (Robot) rc.newInstance();

Method getHello = rc.getDeclaredMethod("throwHello", String.class);
getHello.setAccessible(true);
Object str = getHello.invoke(r, "Bob");

Method sayHi = rc.getMethod("sayHi", String.class);
sayHi.invoke(r, "Welcome");

Field name = rc.getDeclaredField("name");
name.setAccessible(true);
name.set(r, "Alice");

System.out.println(System.getProperty("java.ext.dirs"));
System.out.println(System.getProperty("java.class.path"));

```


### ClassLoader
1. BootStrapClassLoader：C++编写，加载核心库
2. ExtClassLoader：java编写，加载扩展库
3. AppClassLoader：java编写，加载程序所在目录的class
4. 自定义
	- 覆盖findClass和defineClass方法，具体见书

### 双亲委派机制
自底向上检查类是否已加载，自顶向下尝试加载类，代码架构可简化为如下：
	
```
Class loadClass(){
	c = findLoadedClass();  //是否已加载
	if(c == null){
		if(parent != null){ //若有父加载器
			c = parent.loadClass();  //体现自顶向下加载原则
		}else //说明是BootStrapClassLoader
			c = findBootStrapClass();
		
		if( c == null){ //若父加载器无法加载
			c = findClass(); //用本加载器加载
		}
	}else
		return c;
}
```
### 为什么需要双亲委派机制
- 避免多份同样字节码的加载

隐式加载：new  
显式加载：loadClass，forName,获取到class对象再newInstance，但此构造方式不能传递参数

### loadClass和forName的区别
类的装载过程
1. ClassLoader将class文件字节码转换成Class对象
2. 链接：
	- 校验：检查正确性和安全性
	- 准备：分配空间，设置默认值
	- 解析：符号引用转换成直接引用
3. 初始化：执行类中的static代码块和变量赋值

forName会对类进行链接和初始化，所以mysql在加载驱动时用的都是forName；loadClass只会生成一个class对象，不会进行链接和初始化

