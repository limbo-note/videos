# day01
### struts2介绍
处在三层架构层次的web层，具有先进的架构及思想，前身是webwork框架，和struts1基本没有关系

### struts2基本开发流程
1. 编写Action类，Action类中业务方法的返回值，为String类型，此返回字符串就是一个逻辑视图名称
2. 在src/struts.xml文件中配置Action，`action`标签下中配置的`result`标签的name属性与Action类返回的逻辑视图对应
3. 在web.xml中配置struts2的核心过滤器![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-2.jpg)

### struts2开发流程分析

![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-1.jpg)

- struts2整体架构：

![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-3.jpg)
图中灰色部分是架构实现的，无法更改；绿色部分是架构实现了默认版本，我们可以按需求更改；红色部分是需要自己实现。 struts2架构的核心就是interceptor

- 面向切面编程AOP：

![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-4.jpg)

![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-5.jpg)

### struts2的常见配置

- 配置文件加载顺序
	- ![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-9.jpg) 
- Action的配置
	- `package`标签。 使用包来管理Action和Interceptor![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-6.jpg)
	- `action`标签。 将一个请求的url映射到Action类上![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-7.jpg)
	- ![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-8.jpg) 
	- Action的默认配置：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-13.jpg) 
- 常量的配置
	- 大多常量已经默认配置（default.properties）好，但根据需求可自行配置，有三种方式：
	- src/struts.xml中的`constant`元素，name和value属性
	- src/struts.properties文件，以键值对形式
	- web.xml中
	- 常见常量配置：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-10.jpg)
- 分模块开发的配置
	- 即`include`标签，类似c语言中的include头文件使用


###  struts2的Action访问
用户的每次请求，都会转到一个相应的Action类中，Action就是用来处理一次用户请求的对象

- Action类的编写（三种方法）
	- 仅仅为POJO（Plain Ordinary Java Object，简单的Java对象）类。必须含有一个执行方法，方法为public，返回一个字符串且没有参数
	- 实现一个Action接口，覆盖execute()方法，接口中定义了5个字符串常量
	- 继承ActionSupport类，ActionSupport类实现了Action接口，提供了更多方法
- Action的访问 
	- 配置`action`标签的method属性，但是会出现一个类被配置多次的情况
	- 通过通配符完成配置（推荐）![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-11.jpg)
	- 动态方法访问（不推荐）
		- 默认不开启，需要在配置中开启
		- ![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-12.jpg)


# day02

### struts2访问Servlet的API
![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-18.jpg)

三种方式，其实本质上都是通过ActionContext访问：
- 通过ActionContext类访问（推荐）![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-19.jpg)
- 通过特定接口访问（需要哪个原生类就实现其对应的接口）![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-20.jpg)
- 通过ServletActionContext访问

### struts2结果页面配置
全局结果页面：同一个包下面配置的Action中返回相同字符串的，都跳转到该页面，通过标签`global-results`配置

局部结果页面：只在某一个Action中配置，只对此Action有效

几种结果跳转的方式(type属性)：
- 转发![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-14.jpg)
- 重定向![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-15.jpg)
- 转发到Action![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-16.jpg)
- 重定向到Action![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-17.jpg)

### struts2数据封装（接收表单参数）

- 属性驱动（不推荐）
	- 提供属性的set方法。在Action中定义属性，并提供set/get方法即可（由于action对象在每次请求都会创建一个新实例，所以属性是线程安全的）
- 对象驱动（其实就是属性驱动将属性封装到了一个类中）
- 模型驱动（实现ModelDriven接口，重写getModel方法）

### struts2封装集合类型的数据

将表单数据直接封装到List，Map类型的集合中

# day03

### OGNL表达式使用

OGNL:对象视图导航语言. ${user.addr.name} 这种写法就叫对象视图导航。 支持比EL表达式更加丰富的功能

![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-21.jpg)

代码准备工作：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-22.jpg)

### OGNL语法

- 基本取值：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-23.jpg)
- 基本赋值：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-24.jpg)
- 调用方法：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-25.jpg)
- 调用静态方法：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-26.jpg)
- 创建对象：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-27.jpg)

### OGNL与Struts2结合

原理：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/06_struts2/1-28.jpg)

OGNL与Struts2结合的体现方面：
- 参数接收。包括属性、对象、模型驱动，都有OGNL的体现
- 配置文件中
- struts2标签中

（.............Struts2的工作原理，先略过）

# day04

### 拦截器创建
拦截器生命周期：随项目启动而创建，随项目关闭而销毁

- 实现Interceptor接口。完成init,destroy,intercept方法
- 继承AbstractInterceptor类。默认实现了init和destory方法
- 继承MethodFilterInterceptor类。定制拦截器拦截的方法

### 拦截器api
- 放行。 `invocation.invoke();`
- 在放行前后，分别有前后处理
- 不放行。不调用`invoke()`方法，直接返回字符串。则不会执行后续的拦截器及Action，直接交给Result处理结果进行页面跳转

### 拦截器配置

1. 注册拦截器
	- `interceptor`标签，name属性指定名称，class属性指定实现类
2. 注册拦截器栈
	-  `interceptor-stack`标签。 `interceptor-ref`引入拦截器或者引入默认拦截器栈
3. 指定包中默认拦截器栈  
	- `default-interceptor-ref`标签

- 定制拦截方法
	- 指定方法不拦截：在拦截器`interceptor`标签配置中加入子标签`<param name="excludeMethods">method1,method2</param>`
	- 指定方法拦截：`<param name="includeMethods">method1,method2</param>`

拦截器实际用法案例：某网站中，除登陆操作外，其它操作都需要用户已登陆，则可对除登陆外的方法都进行拦截。可利用拦截器实现：首先判断用户是否登陆，未登陆时则不放行其方法，并跳转到登陆页面；已登陆时则放行

### struts2标签

（.................了解内容，先略过）
