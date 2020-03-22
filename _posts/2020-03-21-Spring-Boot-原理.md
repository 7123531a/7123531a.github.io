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

# Spring Boot是如何简化Spring 开发的

## 自动创建Application Context

创建Spring应用， 首先要创建对应的Application Context。Spring Boot根据classpath下的jar包会自动推断创建什么类型的Application Context。开发者只需管理好项目的依赖即可。

## Spring Boot 如何加载Bean Definition到Application Context

从应用的入口`main`方法开始来看Spring Boot如何加载Bean Definition的。

```java
    public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}
```
`SpringApplication`是用来启动基于Spring的application的。第一个参数就是告诉Spring Boot从哪里找到Bean Definition信息，第二个参数args是应用启动时传入的参数。

在这个例子中Bean Definition信息是`DemoApplication.class`, 为什么它可以提供Bean Definition信息呢，因为它是`@Configuration` Class。任何有`@Configuration`标记的类都可以是Spring Bean Definition的来源。让我们回顾一下我们的Demo。

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


### `@SpringBootConfiguration`

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

### `@ComponentScan`

`@ComponentScan` 是spring XML <context:component-scan> 元素对应的注解形式，它告诉Spring从哪里找到`@Component`。注意`@Configuration`也是`@Component`， 所以`@Configuration`都可以被`@ComponentScan`识别。在没有指定搜索路径的情况下，Spring 会从该注解所标记的Class所属的package来进行搜索。一个Spring Boot应用程序的典型布局如下：

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

`@SpringBootApplication`注解标记在`Application.java`上，这样所有的`@Configuration`和`@Component`都能被识别到，然后加载到`ApplicationContext`。

## 自动配置

Spring Boot 自动配置的秘密都在`EnableAutoConfiguration`这里，正如该注解名字所揭示的，它是用来启用自动配置的。

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
	...
}
```
为什么它可以启用自动配置呢， 关键就在于`@Import`。`@Import`与`@Configuration`结合来引入Bean Definition。
`@Import`可以指定四种Bean Definition来源。
1. `@Configuration` Class
2. 实现`ImportSelector`接口的类，该接口的实现者决定哪些`@Configuration` Class要导入
3. `ImportBeanDefinitionRegistrar`
4. `@Component`

在`@EnableAutoConfiguration`中指定的`AutoConfigurationImportSelector.class`实现了`ImportSelector`。该类会扫描classpath 下所有的META-INF/spring.factories文件来找到以`org.springframework.boot.autoconfigure.EnableAutoConfiguration`为Key的配置项。该Key所对应的配置项就是要加载的`@Configuarion` class。以MyBatis为例，看看是如何配置的。

```java
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.mybatis.spring.boot.autoconfigure.MybatisLanguageDriverAutoConfiguration,\
org.mybatis.spring.boot.autoconfigure.MybatisAutoConfiguration

```
# Spring Boot 加载流程

```java
public ConfigurableApplicationContext run(String... args) {
		StopWatch stopWatch = new StopWatch();
		stopWatch.start();
		ConfigurableApplicationContext context = null;
		Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
		configureHeadlessProperty();
		//加载所有的SpringApplicationRunListener
		SpringApplicationRunListeners listeners = getRunListeners(args);
		//通知SpringApplicationRunListener SpringBoot开始启动了
		listeners.starting();
		try {
			//封装应用启动参数
			ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
			//创建Environment,添加应用启动参数到Environment中，通知environmentPrepared给SpringApplicationRunListener
			ConfigurableEnvironment environment = prepareEnvironment(listeners, applicationArguments);
			configureIgnoreBeanInfo(environment);
			Banner printedBanner = printBanner(environment);
			//创建Application Context
			context = createApplicationContext();
			exceptionReporters = getSpringFactoriesInstances(SpringBootExceptionReporter.class,
					new Class[] { ConfigurableApplicationContext.class }, context);
			//关联Enviromnent和ApplciationContext
			//通知contextPrepared
			//加载@Configuration class到Application Context
			//通知contextLoaded		
			prepareContext(context, environment, listeners, applicationArguments, printedBanner);
			//刷新Application Context，加载所有的non lazy Bean
			refreshContext(context);
			afterRefresh(context, applicationArguments);
			stopWatch.stop();
			if (this.logStartupInfo) {
				new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), stopWatch);
			}
			//通知Spring Boot started
			listeners.started(context);

			//调用ApplicationRunner和CommandLineRunner
			callRunners(context, applicationArguments);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, exceptionReporters, listeners);
			throw new IllegalStateException(ex);
		}

		try {
			//通知listener Spring Boot正在Running
			listeners.running(context);
		}
		catch (Throwable ex) {
			handleRunFailure(context, ex, exceptionReporters, null);
			throw new IllegalStateException(ex);
		}
		return context;
	}
```

# 如何扩展Spring Boot

