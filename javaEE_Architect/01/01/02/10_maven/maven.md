# day01
### maven介绍
- 优势
	- 一步构建
	- 依赖管理
	- 跨平台
	- 提高大型项目开发效率
- maven仓库
	- 本地仓库
	- 远程仓库（私服）
	- 中央仓库
- 工程目录
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/10_maven/1-1.jpg)
- 常用命令
	- compile/test/clean/package/install

### 生命周期

- clean
- default
	- 包括但不限于compile/test/package/install/deploy
- site
	- 生成项目的站点文档

不同的生命周期可以混合使用，如maven clean package；同一生命周期的不同阶段是有先后顺序的，运行靠后阶段的命令会自动执行之前所有阶段的命令，如maven package会执行compile/test的阶段

### 构建web工程基本流程

- 创建maven工程
- 定义工程坐标
	- ![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/10_maven/1-2.jpg)
- 设置编译版本
- 编码
- 添加依赖
	- 依赖范围：
		![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/10_maven/1-3.jpg)
- 测试运行打包

### 添加插件

tomcat举例：	
	![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/10_maven/1-4.jpg)

# day02
### 依赖传递与依赖版本冲突
- 第一声明优先
- 路径近者优先（明确定义出来的版本优先）
- 排除原则（加`exclusions`标签）
- 版本锁定原则（`dependencyManagement`标签）

### 分模块开发

- 创建父工程（普通maven工程）
	- 依赖都应添加在父工程的pom.xml中
	- 工程的packaging类型为pom
- 各子模块工程（maven module工程）
	- 依赖于父工程
	- 各子模块可自行打包，相互添加依赖
	- 普通模块打包成jar包，web层模块打包成war包

### 依赖范围对传递依赖的影响

![](https://github.com/limbo-note/videos/blob/master/javaEE_Architect/01/01/02/10_maven/2-1.jpg)

### 私服

- nexus
	- 上传
	- 下载