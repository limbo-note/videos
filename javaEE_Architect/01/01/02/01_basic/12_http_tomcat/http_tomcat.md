Tomcat 目录结构：
- bin
- conf. 配置文件目录
- lib. 依赖库
- logs. 日志文件
- temp. 临时文件
- webapps. 默认的WEB项目存放的目录
- work. tomcat处理jsp的工作目录

环境变量JAVA_HOME需配置到jdk目录下，否则直接start.bat启动tomcat会找不到jdk

web项目目录结构：![](https://github.com/limbo-china/videos/blob/master/javaEE_Architect/01/01/02/01_basic/12_http_tomcat/1-1.jpg)


在eclipse的perferences/server/Runtime Environment中配置好tomcat服务器，与之绑定。然后可右键项目，Run on server，将项目发布到tomcat中（其实本质上就是把项目的WebContent文件夹下的内容拷贝到tomcat的webapps中，文件夹名与项目名相同，这样就可以直接通过localhost:8080/项目名，直接访问web内容）