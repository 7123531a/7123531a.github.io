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
1. BeanFactoryPostProcessor，定制Configuration Metadata
2. BeanPostProcessor, 定制bean的实例
3. FactoryBean， 定制对象的创建

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
该接口可用来修改application context中内部维护的`BeanFactory`。当该接口被调用时， 所有的bean definition都已经被加载到`BeanFacotry`当中， 但是bean还没有实例化。这个接口提供了机会来修改bean definition。我们可以利用这个扩展机制来改写bean definition。

`PropertySourcesPlaceholderConfigurer` 就是利用了这个特性来解析（覆写）bean definition 属性值之中的${...}占位符。

## BeanPostProcessor的应用
先看一下interface定义
```java
public interface BeanPostProcessor {

	@Nullable
	default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}

	@Nullable
	default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}
```
当该callback被调用时，对象及其依赖的对象已经被创建。`postProcessBeforeInitialization`在对象的初始化方法前被调用，`postProcessAfterInitialization`在对象的初始化之后被调用。这个接口的实现者可以定制化bean的实例。例如校验是否实现接口, 包装该bean生成一个代理。当校验是否实现某些接口时， 一般在`postProcessBeforeInitialization`中做， 当包装原始bean生成一个代理时， 一般在`postProcessAfterInitialization`中做，这样的话原始bean已经完成了初始化。

## FactoryBean的应用
```java
public interface FactoryBean<T> {
	T getObject() throws Exception;
	Class<?> getObjectType();
	default boolean isSingleton() {
		return true;
	}
}
```
从接口的定义中我们可以看到`FactoryBean`就是对象的创建工厂。如果一个对象的创建逻辑非常复杂，我们就可以实现这个接口来把复杂的逻辑封装起来。如果一个bean实现了这个接口，它被作为一个要暴露的对象的工厂，而不是该bean本身被暴露出来。当从`BeanFactory`中获取该对象时，不是该对象被返回，而是返回由它创建的对象。
spring框架本身就大量利用了这个特性。如`JndiObjectFactoryBean`, 它封装了如何从jndi上获取对象的逻辑。

# How Java-based configurations works internally
所有的`@Configuration`类在Spring启动的时候都会利用CGLIB生成一个子类，该子类会覆写`@Bean`方法，它会首先检查spring容器中是否已经存在要请求的Bean， 如果不存在，则调用父类的方法来创建bean。
当以`@Bean`方式来注册`BeanPostProcessor`和`BeanFactoryPostProcessor`时， `@Bean`方法要声明成static，避免`@Configuration`类的提前加载。