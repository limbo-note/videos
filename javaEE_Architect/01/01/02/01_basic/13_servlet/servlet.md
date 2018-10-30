### servlet开发基本步骤
servlet 开发基本步骤：
1. 创建类实现Servlet接口
2. 覆盖尚未实现的方法---service方法
3. 在web.xml进行servlet的配置

但一般会继承HttpServlet类，而不是实现Servlet接口：
1. 创建类继承HttpServlet类
2. 覆盖doGet和doPost
3. 在web.xml进行servlet的配置

### servlet生命周期

Servlet接口的方法：
- init(). servlet对象创建的时候执行，默认第一次访问servlet时创建该对象
- service(ServletRequest, ServletResponse). 每次请求都会执行，并且每次都会创建一对新的ServletRequest和ServletResponse
- destroy(). 服务器关闭servlet就销毁了，销毁时执行destroy()

### servlet执行过程

![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/01_basic/13_servlet/1-1.png)

![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/01_basic/13_servlet/1-2.png)

### servlet配置

- 基本配置

	![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/01_basic/13_servlet/1-3.png)
	- url-pattern匹配方式：完全匹配、目录匹配（可含正则）、扩展名匹配（可含正则）；目录匹配和扩展名匹配不能同时使用
- 实例化Servlet配置 
	- Servlet对象默认在第一次被访问时创建。当在servlet的配置时 加上一个配置 <load-on-startup> servlet对象在服务器启动时就创建
- 缺省servlet
	- 将url-pattern配置一个/，代表该servlet是缺省的servlet
	- web应用中所有的资源的响应都是servlet负责，包括静态资源。所以在访问静态资源时，其实不是直接去返回静态资源的内容，而是启用的默认的缺省servlet(DefaultServlet)，该配置在tomcat的全局配置当中。当具体的Web应用的配置与全局配置有冲突时，web应用的配置就会覆盖全局配置，当覆盖了缺省servlet时，就可能出现访问不到静态资源的情况
- 欢迎页面 
	- <welcome-file-list>配置欢迎页面，优先级从上到下依次降低

### ServletContext对象
ServletContext对象内部封装是该web应用的信息，一个web应用只有一个；而一个web应用可以用多个Servlet对象

ServletContext对象在web应用加载时被创建，在web应用卸载时被销毁

- ServletContext的作用
	- 获得web应用全局的初始化参数（在web.xml中<param-name>/<param-value>配置）
	- 获得web应用中任何资源的绝对路径，getRealPath()方法
	- ServletContext是域对象（存储数据的区域），所有的web资源都可以随意向ServletContext域中存取数据，数据可以共享![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/01_basic/13_servlet/1-4.png)方法：`setAtrribute(String name,Object obj);getAttribute(String name);removeAttribute(String name);`
