### hamcrest断言

所有之前的assert方法均可以assertThat来代替。hamcrest的匹配方法更加自然，如：
![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect_Advanced/04/02/junit/1-1.jpg)

- Failure是指测试失败
- Error是指测试程序本身出错

### JUnit4 Annotation

- @Test: 测试方法
	- (expected=XXException.class)：期望捕获到xx异常
	- (timeout=xxx)：运行时间在xx内
- @Ignore: 被忽略的测试方法
- @Before: 每一个测试方法之前运行
- @After: 每一个测试方法之后运行
- @BeforeClass: 所有测试开始之前运行
- @AfterClass: 所有测试结束之后运行

可同时运行多个测试

### 注意事项

- 遵守约定，比如：
	- 类放在test包中
	- 类名用XXXTest结尾
	- 方法用testMethod命名
