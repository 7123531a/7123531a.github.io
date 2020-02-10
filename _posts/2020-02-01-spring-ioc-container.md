# What is Spring
spring 是一个实现IoC原理的容器。spring容器中的对象通过constructor 参数，Factory method 参数或者Properties来描述它的依赖，当spring创建对象的时候，有spring来注入它的依赖，而不是有对象本身来控制依赖对象的创建，这也是IoC名字的由来。

# Configuration Metadata
spring如何创建一个对象呢，你当然得告诉它如何去创建它。有三种方式来告诉spring如何创建对象。
1. xml
2. annotation-based configuration
3. java based configuration
    
    主要通过@Configuration和@Bean

第二种和第三种可以归结为一种，本质上都是基于annotaton的方式，第三种的方式对bean没有侵入，不需要在bean上来声明注解。
# Bean lifecycle
对象的创建有spring来负责，spring提供了许多callback和Aware接口来参与对象的创建过程。
1. InitializingBean
2. DisposableBean
3. ApplicationAware

# Container extension point
1. BeanPostProcessor, 定制bean的创建
2. BeanFactoryPostProcessor，定制Configuration Metadata
3. FactoryBean， 定制对象的创建

## BeanPostProcessor的应用

## BeanFactoryPostProcessor的应用
先看一下接口的定义。
```java
@FunctionalInterface
public interface BeanFactoryPostProcessor {

	/**
	 * Modify the application context's internal bean factory after its standard
	 * initialization. All bean definitions will have been loaded, but no beans
	 * will have been instantiated yet. This allows for overriding or adding
	 * properties even to eager-initializing beans.
	 * @param beanFactory the bean factory used by the application context
	 * @throws org.springframework.beans.BeansException in case of errors
	 */
	void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException;

}

```
该接口可用来修改application context中内部维护的`BeanFactory`。当该接口被调用时， 所有的bean definition都已经被加载到`BeanFacotry`当中， 但是bean的实例还没有创建。这个接口提供了机会来修改bean definition。我们可以利用这个扩展机制来复写bean definition。

`PropertySourcesPlaceholderConfigurer` 就是利用了这个特性来解析（覆写）bean definition 属性值之中的${...}占位符。

## FactoryBean的应用

# How Spring AOP works