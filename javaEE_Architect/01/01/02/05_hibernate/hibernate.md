# day01
- CRM: Customer Relationship Management 客户关系管理
- EE开发三层框架（web、业务层、持久层）：

	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/05_hibernate/1-1.jpg)
- Hibernate: 持久层ORM框架
	- xml映射文件（类和表的映射配置）
		- class标签。 类和表名的映射
		- id标签。 主键对应
		- property标签。 普通字段对应
	- 核心配置文件（数据库驱动、用户名密码等必选配置，其它可选配置）
		![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/05_hibernate/1-2.jpg)
	- 主要代码框架
		- `Configuration config = new Configuration().configure(".xml");`(默认为hibernate.cfg.xml)
		- `SessionFactory sessionFactory = config.buildSessionFactory();`(SessionFactory是重量级的，一般只创建一个)
		- `Session session = sessionFactory.openSession();`（或getCurrentSession()获取与当前线程绑定的Session，Session是非线程安全的）
		- `Transaction tx = session.beginTransaction();`（开启事务后可进行事务操作，最后通过tx.commit()提交或rollback()回滚）

- 系统实例
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/05_hibernate/1-3.jpg)


# day02
### 持久化类编写规则
- 提供无参构造方法。 因为底层需要反射进行实例化
- 属性私有，并提供公有get,set方法
- 属性用包装类型
- 必须有唯一的属性与表的主键对应
- 不用final修饰，因为底层需要子类化

### hibernate主键生成策略
- 自然主键：原字段就符合主键的要求，可以直接做为主键。但一般不会这么用
- 代理主键：自定义的主键，和业务字段没关系，一般为id


- 主键生成策略
	- identity:主键自增.由数据库来维护主键值.录入时不需要指定主键.（Mysql中默认策略）
	- sequence: Oracle中的主键生成策略.
	- increment(了解): 主键自增.由hibernate来维护.每次插入前会先查询表中id最大值.+1作为新主键值.	（线程不安全，一般不用）
	- hilo(了解): 高低位算法.主键自增.由hibernate来维护.开发时不使用.
	- native: hilo+sequence+identity 自动三选一策略. hilo一般不会用到
	- uuid: 产生随机字符串作为主键. 主键类型必须为string 类型.
	- assigned： 不自动生成主键，由java程序自己填，不填会报错

### 持久化对象三种状态

- 瞬时态
	- 没有id，没有与session关联（即没有在session缓存中）
- 持久化状态
	- 有id，且在session缓存中
- 游离|托管态
	- 有id，没有在session缓存中 

示例：

![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/05_hibernate/2-1.jpg)

状态转换：

![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/05_hibernate/2-2.jpg)

持久态对象能自动更新：（其实就是在session缓存中的对象在提交后能自动同步到数据库，所有对持久化对象的更改就是更改了缓存中的数据）

![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/05_hibernate/2-3.jpg)

### hibernate一级缓存

![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/05_hibernate/2-4.jpg)

执行commit()提交时，会清理session缓存，并同时判断缓存和快照中数据是否一致，若变化，则会执行update同步到数据库，并更新快照；若一致，则不必同步到数据库

- save()、update()或saveOrUpdate()会将对象放入一级缓存中。即进入持久化状态
- load()、get()、list()、iterator()在查询时先查缓存
- close()会清空缓存
- 快照就是数据库的一个本地复制，缓存在同步到数据库之前都可以先比对快照，再决定需不需要同步

### hibernate事务
- acid特性
- 并发问题
	- 脏读。 一个事务读取到另一个事务未提交的数据
	- 不可重复读。 读到了另一个事务提交的update数据，导致同一事务的多次查询结果不一致
	- 虚读/幻读。 读到了另一个事务提交的insert数据，导致多次查询结果不一致
- 隔离级别
	- 读未提交。 最低隔离，可导致脏、不可重复、幻
	- 已提交读。 避免脏，可导致不可重复、幻
	- 可重复读。 可访问其它事务提交插入的数据，不可访问其它事务提交修改的数据。 避免脏、不可重复，可导致幻
	- 串行化。 最高隔离，效率低，没安全问题
- 项目中的事务
	- 应在service层控制事务
	- service层和dao层获取到的session应该是同一个，可用getCurrentSession()方法（主配置中需指定thread配置；getCurrentSession方法获得的session对象.当事务提交时,session会自动关闭.不要手动调用close关闭.）
	- 如![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/05_hibernate/2-5.jpg) ![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/05_hibernate/2-6.jpg)

### hibernate的批量查询
- HQL查询（Query面向对象）
	- 获得session
	- 编写HQL
	- createQuery创建查询对象
	- Query的set方法设置参数
	- list()或uniqueResult()查询
		![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/05_hibernate/2-7.jpg)
		![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/05_hibernate/2-8.jpg)
- Criteria查询
	- 获得session
	- 通过session获得Criteria
	- Restrictions创建Criterion条件对象，加入至Criteria对象
	- Criteria的list()或uniqueResult()查询
- 原生SQL
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/05_hibernate/2-9.jpg)

# day03
（...............还没看）hibernate的一对多，多对一，多对多关系

# day04
（...............还没看）查询优化

