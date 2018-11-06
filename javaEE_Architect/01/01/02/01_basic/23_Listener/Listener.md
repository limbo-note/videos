### 监听器

事件源：被监听的对象  
监听器：监听事件源对象  
注册监听器：将监听器与事件源进行绑定  
响应行为：监听器监听到事件源的状态变化时所涉及的功能代码  

- 按被监听对象划分
	- ServletRequest域
	- HttpSession域
	- ServletContext域
- 按监听的内容划分
	- 监听对象的创建与销毁
	- 监听对象的属性变化

![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/01_basic/23_Listener/1-1.jpg)

### 监听对象的创建与销毁

- 编写一个监听器类去实现监听器接口
- 覆盖监听器的方法
	
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/01_basic/23_Listener/1-2.jpg)
- 需要在web.xml中进行配置---注册
	
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/01_basic/23_Listener/1-3.jpg)

### 监听对象的属性变化

类似步骤
- 实现接口
- 覆盖方法

	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/01_basic/23_Listener/1-4.jpg)
- 注册

### 监听session中对象的绑定和钝化（持久化到硬盘）

HttpSessionBindingListener/HttpSessionActivationListener接口

### 邮件的发送过程

![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/01_basic/23_Listener/1-5.png)
