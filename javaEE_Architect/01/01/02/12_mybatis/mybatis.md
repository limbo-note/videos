# day01
### jdbc存在的问题

- 数据库连接创建、释放频繁造成系统资源浪费，从而影响系统性能。如果使用数据库连接池可解决此问题
- Sql语句在代码中硬编码，造成代码不易维护
- 使用preparedStatement向占有位符号传参数存在硬编码，不易维护
- 对结果集解析存在硬编码（查询列名），sql变化导致解析代码变化

### Mybatis架构

![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/12_mybatis/1-1.jpg)


### 基本步骤

类似hibernate：
- 核心配置文件
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/12_mybatis/1-2.jpg)
	- 另可以配置properties（导入属性配置文件），typeAliases（指定类型别名），mappers（导入映射器配置文件）等
	- 引入mappers的方式
		- `<mapper resource=" " />`
		- `<mapper class=" " />` 要求mapper接口名称和mapper映射文件名称相同，且放在同一个目录中
		- **(项目中推荐使用，引入包下所有映射)** `<package name=""/>`要求mapper接口名称和mapper映射文件名称相同，且放在同一个目录中
- 映射文件
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/12_mybatis/1-3.jpg)
	- `#{}`表示一个占位符号,`${}`表示拼接sql串
- 加载配置文件获取sessionFactory
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/12_mybatis/1-4.jpg)
- 获取session
- 查询
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/12_mybatis/1-5.jpg)

自增主键返回：
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/12_mybatis/1-6.jpg)

### Mybatis和hibernate的不同
Mybatis它不完全是一个ORM框架，需要程序员自己编写Sql语句。mybatis可以通过XML或注解方式灵活配置要运行的sql语句，并将java对象和sql语句映射生成最终执行的sql，最后将sql执行的结果再映射生成java对象

Mybatis直接编写原生态sql，灵活，但是灵活的代价是mybatis无法做到数据库无关性（比如从mysql换到oracle就需要修改）；Hibernate对象/关系映射能力强，数据库无关性好


### Mapper动态代理开发

原始的DAO开发的问题：
- Dao方法体存在重复代码：通过SqlSessionFactory创建SqlSession，调用SqlSession的数据库操作方法
- 调用sqlSession的数据库操作方法需要指定statement的id，这里存在硬编码，不利于开发维护

Mapper接口开发方法只需要程序员编写Mapper接口（相当于Dao接口），由Mybatis框架根据接口定义创建接口的动态代理对象，代理对象的方法体同Dao接口实现类方法

Mapper接口开发需要遵循以下规范：
- Mapper.xml文件中的namespace与mapper接口的类路径相同
- Mapper接口方法名和Mapper.xml中定义的每个statement的id相同 
- Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql 的parameterType的类型相同
- Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同

如：
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/12_mybatis/1-7.jpg)
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/12_mybatis/1-8.jpg)
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/12_mybatis/1-9.jpg)