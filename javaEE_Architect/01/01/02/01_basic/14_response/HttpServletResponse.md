### HttpServletResponse流程

![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/01_basic/14_response/1-1.png)

### HttpServletResponse常见方法

- 设置响应状态码
- 设置响应头
- 设置响应体
	- 响应体设置文本. getWriter.write()方法将内容写到response缓冲区，缓冲区默认编码iso8859-1（不支持中文），response可设置setCharacterEncoding()；也可以setContentType()设置浏览器的解析方式
	- 响应体设置字节. getOutputStream()直接写字节流

重定向（状态码302，响应头中含location表示重定向的地址）：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/01_basic/14_response/1-2.png)

文件字节传输代码示例：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/01_basic/14_response/1-3.png)

文件下载功能需要再加入几个特定的响应头，告诉浏览器以下载的方式处理

注意点：
- response获取的流不需要手动关闭，tomcat会自动关闭
- response的getWriter和getOutputStream不能同时调用