# 配置外部化

开发应用的时候，我们经常将一些配置放到properties文件中，这样当修改这些配置的时候我们可以不用修改源代码，不用重新打包部署。如果我们的应用基于java config的方式来配置Bean，我们可以使用`@PropertySource`来指定外部properties文件。
## 引用properties中的property：
### 使用`Environment` API

```java
 @Configuration
 public class AppConfig {

     @Autowired Environment env;

     @Bean
     public MyBean myBean() {
         MyBean myBean = new MyBean();
         myBean.setName(env.getProperty("bean.name"));
         return myBean;
     }
 }

```

### 使用`@value`

```java
 @Configuration
 @PropertySource("classpath:/com/acme/app.properties")
 public class AppConfig {

     @Value("${bean.name}") String beanName;

     @Bean
     public MyBean myBean() {
         return new MyBean(beanName);
     }
 }
```

# 如何解析property placeholder

但是你知道上面的${...}是如何被解析的吗？
这就需要用到`PropertySourcesPlaceholderConfigurer`。该类会将引用的property替换成properties文件中对应的property的值。

# Spring Boot 是如何加载PropertySourcesPlaceholderConfigurer

要解析${...}, PropertySourcesPlaceholderConfigurer必须在Spring的 ApplicationContext， 那SpringBoot是如何加载PropertySourcesPlaceholderConfigurer到ApplicationContext呢？

SpringBoot自动装配的威力就显现出来了。

```java
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
        ...       
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
        ...

@Configuration(proxyBeanMethods = false)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)
public class PropertyPlaceholderAutoConfiguration {

	@Bean
	@ConditionalOnMissingBean(search = SearchStrategy.CURRENT)
	public static PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer() {
		return new PropertySourcesPlaceholderConfigurer();
	}

}

```