# SpringBoot

​	ssm框架的项目为war包，在tomcat上运行

​	springboot项目为jar包，内嵌tomcat

​	**什么为最核心原理：自动装配**

​	**约定大于配置**

#### 	学习路线：

​			配置编写yaml

​			自动装配原理

​			集成web开发

​			集成数据库

​			分布式开发：dubbo+zookeeper

​			swagger：接口文档

​			任务调度

​			springSecurity:shiro



## 	微服务架构

​		一种架构风格：

​			**一个应用被构建成为一系列小服务的组合**

​			可以**通过http方式互通**。

​		单体服务架构（all in one)：

​			如ssm架构，所有应用均在一个war包中，

​			优点：易于开发和测试，方便部署。扩展时只需将war包复制多分，然后放到服务器，做负载均衡即可。

​			缺点：修改麻烦，改一点地方需要停掉整个服务，重新打包。

​		微服务架构：

​			把每个功能元素独立出来

​			优点：节省了调度资源

​			每个功能元素是可替换的，可独立升级的软件代码（降低耦合性）

​			一个微服务单元通过springboot搭建

​			一个大型分布式网络服务由spring cloud完成，**实现分布式**

## Springboot程序

​		项目名Application为程序主入口，不可删改

​		application.properties为主配置文件

​		Application本身是一个Spring组件

​		spring-boot-starter-web默认使用tomcat作为嵌入式容器

### 	自动装配原理

​		自动配置pom.xml

​		spring-boot-dependencies:核心依赖在父工程中

​		在写或引入springboot依赖的时候，不需要指定版本

#### 		启动器

​			Springboot的启动场景

​			启动类下的所有资源

```xml
		<!-- 自动导入web环境下的所有依赖-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
```

​			Springboot会将所有功能场景，都变成一个个启动器

​			需要使用的功能只需要找到对应的启动器

### 	@SpringBootApplication

​			标注是一个springboot应用

#### 			@SpringBootConfiguration:spring配置类

​					@Configuration: Component，spring组件

#### 			@EnableAutoConfiguration自动配置

​					@AutoConfigurationPackage:自动配置包

​						@Import(AutoConfigurationPackages.Registrar.class):自动配置包注册

​					@Import(AutoConfigurationImportSelector.class):自动导入选择

​	META-INF/spring.factorys : 自动配置核心文件

## Springboot配置

​	配置文件默认为application.properties

​		语法结构：key=value;

​	可以另选的配置文件application.yaml

​		语法结构:	key: 空格 value

​	配置文件**application**名称是固定的

### 	yaml基本概念

​	倾向于存储数据的一种标记语言

​	可以注入到配置类中

​	可以为实体类赋值

​	yaml可以存储对象

```yaml
#对象
student:
	name: natsuki
	age: 20
#或
student: {name: natsuki,age: 20}

#数组
pets:
	- cat
	- dog
	- pig
#或
pets: [cat,dog,pig]
```

