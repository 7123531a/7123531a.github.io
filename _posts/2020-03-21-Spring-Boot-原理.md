# Spring Boot是什么
Spring Boot是有大名鼎鼎的Spring Framework的开发者设计的，旨在用来简化基于Spring的应用开发。

# Spring Boot特性
1. 创建可以直接运行的独立的应用程序
2. 可以直接内嵌应用服务器到应用程序中，无需生成war包部署到应用服务器中，简化了web应用的部署和测试。
3. 引入了自动配置机制。

# Spring Boot 设计原则
1. 约定大于配置
2. 自动配置

# Spring Boot应用是什么样子的

使用[Spring Initializr](https://start.spring.io/)来快速生成一个Spring Boot应用。下面只列出最重要的部分。
1. java
```java

@SpringBootApplication
public class DemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}

}

```

2. pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.2.4.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>demo</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>2.1.1</version>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>

```

# Spring Boot是如何工作的
要理解Spring Boot是如何工作的，我们就从应用的入口`main`方法开始。
```java
    public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}
```
`SpringApplication`是用来启动基于Spring的application的。第一个参数就是告诉Spring Boot从哪里找到bean的配置信息。第二个参数args是应用启动时传入的参数。

在这个例子中bean的配置信息是`DemoApplication.class`, 为什么它可以作为Bean的配置信息呢。因为它是@configuration。任何有@configuration标记的类都可以是Spring Bean definition的来源。让我们回顾一下我们的Demo。

```java
@SpringBootApplication
public class DemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}

}
```

`@SpringBootApplication`在我们的入口类，看一下它的定义。
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
    ...
}
```
`@SpringBootApplication`包含了`@SpringBootConfiguration`, `@EnableAutoConfiguration`, `@ComponentScan`，下面会逐一介绍。


## `@SpringBootConfiguration`

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
    ...
}
```
它就是一个@Configuraion。所以`DemoApplication.class`可以用来制定Bean Definition的信息。但是我们在这个类里面没有看到任何的Bean Definition信息。那spring boot如何找到Bean 定义信息呢。

## `@ComponentScan`
`@ComponentScan` 是spring xml <context:component-scan> 元素对应的注解形式，它告诉Spring从哪里找到`@Component`。注意`@Configuration`也是`@Component`， 所`@Configuration`都可以被`@ComponentScan`识别。在没有指定搜索路径的情况下，Spring 会从该注解所在的Class所属的package来进行搜索。一个Spring Boot应用程序的典型布局如下：

```java
com
 +- example
     +- myapplication
         +- Application.java    应用程序入口
         |
         +- customer
         |   +- Customer.java
         |   +- CustomerController.java
         |   +- CustomerService.java
         |   +- CustomerRepository.java
         |
         +- order
             +- Order.java
             +- OrderController.java
             +- OrderService.java
             +- OrderRepository.java
```