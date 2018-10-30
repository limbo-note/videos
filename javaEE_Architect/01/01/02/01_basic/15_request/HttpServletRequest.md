### HttpServletRequest流程

![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/01_basic/15_request/1-1.png)

### HttpServletRequest常见方法

- 获得请求行. 
	- 请求方式，uri/url，和url后面的GET方式带的参数
	- 也可获得客户端的ip/port等信息
- 获得请求头 
- 获得请求体
	- 请求体当中的post请求参数

### HttpServletRequest域对象和请求转发

重定向（客户端的行为）和请求转发（服务端的行为）的区别：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/01_basic/15_request/1-2.png)
1. 重定向两次请求，转发一次请求
2. 重定向地址栏的地址变化，转发的地址栏地址不变
3. 重新定向可以访问外部网站（所以重定向地址是相对于外部的），转发只能访问内部资源（转发地址是相对于此web项目的）
4. 转发的性能要优于重定向

请求转发：
- 获得请求转发器----path是转发的地址
	
	`RequestDispatcher getRequestDispatcher(String path);`
- 通过转发器对象转发

	`requestDispathcer.forward(ServletRequest request, ServletResponse response);`

ServletContext域对象：
- 创建：服务器启动
- 销毁：服务器关闭
- 域的作用范围：整个web应用

request域对象：
- 创建：访问时创建request
- 销毁：响应结束request销毁
- 域的作用范围：一次请求中

### 其它
BeanUtils库：将Map中的数据映射到实体中，根据map的key与实体的域变量进行匹配（service层）

关于request中的乱码问题：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/01_basic/15_request/1-3.png)
