### filter

编程规范同Servlet和Listener

- filter访问流程

	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/01_basic/24_filter/1-1.png)

配置文件中的`dispatcher`标签：
- REQUEST：默认值，代表直接访问某个资源时执行filter
- FORWARD：转发时才执行filter
- INCLUDE: 包含资源时执行filter
- ERROR：发生错误时 进行跳转是执行filter

### 案例：自动登陆

![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/01_basic/24_filter/1-2.png)

### 案例：全局解决乱码问题

面向切面，在filter中解决乱码，其下游的servlet均受影响，不需要每一个servlet都做同样操作

总结Filter的作用：
- 公共代码的提取
- 可以对request和response中的方法进行增强(装饰者模式/动态代理)
- 进行权限控制
