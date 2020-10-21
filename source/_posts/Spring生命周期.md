---
title: Spring生命周期
date: 2020-03-23 10:13:32
categories: 杂谈
tags: 杂谈
---


## 导读

- Spring中Bean的生命周期从容器的启动到停止，涉及到的源码主要是在`org.springframework.context.support.AbstractApplicationContext.refresh`方法中，下面也是围绕其中的逻辑进行讲解。

## 开撸

【1】 prepareRefresh()

 内部其实很简单，就是设置一些标志，比如开始时间，激活的状态等。

【2】prepareBeanFactory(beanFactory)

做一些简单的准备工作，此处不再赘述！！！

【3】postProcessBeanFactory(beanFactory)

主要的作用就是添加了一个后置处理器`ServletContextAwareProcessor`

【4】invokeBeanFactoryPostProcessors(beanFactory)

调用容器中的所有的**BeanFactoryPostProcessor**中的**postProcessBeanFactory**方法，按照优先级调用，主要实现逻辑在org.springframework.context.support.PostProcessorRegistrationDelegate.invokeBeanFactoryPostProcessors(org.springframework.beans.factory.config.ConfigurableListableBeanFactory, java.util.List<org.springframework.beans.factory.config.BeanFactoryPostProcessor>)
    (1) 执行所有BeanDefinitionRegistryPostProcessor(对BeanFactoryPostProcessor的扩展，运行在普通的实现类之前注册bean)的方法，同样是内部按照优先级进行排序调用
    (2) 对剩余的进行按照优先级排序调用，同样是内部进行排序执行

【5】**registerBeanPostProcessors(beanFactory)**

注册所有的**BeanPostProcessor**（后置处理器），按照优先级注册，分别是PriorityOrdered，Ordered，普通的，内部的。主要的实现逻辑在PostProcessorRegistrationDelegate.registerBeanPostProcessors()

【6】initMessageSource()
注册MessageSource,提供消息国际化等功能

【7】initApplicationEventMulticaster();

注册事件广播器ApplicationEventMulticaster，用于spring事件的广播和事件监听器的处理

【8】registerListeners()

注册事件监听器ApplicationListener，并且广播一些早期的事件，主要的逻辑在org.springframework.context.support.AbstractApplicationContext.registerListeners

【9】finishBeanFactoryInitialization(beanFactory)

实例化所有非懒加载的Bean，spring生命周期中的主要方法，主要逻辑在org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons，深入进去其实就是getBean()方法创建，详情向下看。

【10】finishRefresh()

主要的功能是发布事件ContextRefreshedEvent

【11】destroyBeans()

容器启动出现异常时销毁Bean



以上就是Spring容器启动的过程，主要的逻辑都在org.springframework.context.support.AbstractApplicationContext#refresh中，其他的都很容易理解，现在我们着重分析一下单例Bean的创建过程，入口是第9步。



## 实例化单例Bean

【1】debug进入，实际主要的逻辑都在org.springframework.beans.factory.support.DefaultListableBeanFactory#preInstantiateSingletons方法中，逻辑如下：

```java
//获取所有注入到ioc容器中的bean定义信息
List<String> beanNames = new ArrayList<>(this.beanDefinitionNames);
		//循环创建
		for (String beanName : beanNames) {
			RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);
            //非抽象，单例，非懒加载的bean初始化
			if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit()) {
                //如果是FactoryBean
				if (isFactoryBean(beanName)) {
                    //getBean
					Object bean = getBean(FACTORY_BEAN_PREFIX + beanName);
                    //非FactoryBean，getBean
				else {
					getBean(beanName);
				}
			}
		}
```



以上源码总结得知，最终实例化Bean的方法肯定在getBean中的，debug进入，得知doGetBean是大boss，spring源码有趣的是最终的实现都是在doxxxx()。



【2】AbstractBeanFactory#doGetBean，由于篇幅太短，就不贴源码了，只贴关键代码

实例化的主要流程全部都在这里，下面一一解析即可。

(1) Object sharedInstance = getSingleton(beanName)

从早期的缓存中获取，如果存在返回Bean，实例化

（2）BeanFactory parentBeanFactory = getParentBeanFactory()

从父工厂的中获取Bean

（3）if (mbd.isSingleton()) 

分单例和多例进行分开创建Bean，这里只分析单例Bean的创建

（4）sharedInstance = getSingleton(beanName, () -> {   try {      return createBean(beanName, mbd, args);   }

createBean方法创建Bean，进入createBean(）

​	a. Object bean = resolveBeforeInstantiation(beanName, mbdToUse)：执行所有的InstantiationAwareBeanPostProcessor中的**postProcessBeforeInstantiation**，在实例化之前调用，返回null继续下一步，返回一个bean，那么bean实例化完成，将调用其中的**postProcessAfterInstantiation**方法

​       b. Object beanInstance = doCreateBean(beanName, mbdToUse, args)：创建Bean的完成过程

​	c. 进入**doCreateBean**，instanceWrapper = createBeanInstance(beanName, mbd, args)：创建Bean的实例

​	d. populateBean(beanName, mbd, instanceWrapper)：属性装配，执行InstantiationAwareBeanPostProcessor的**postProcessAfterInstantiation**，再执行**postProcessProperties**方法。

​	e. exposedObject = initializeBean(beanName, exposedObject, mbd)：初始化Bean，debug进入

​	f. invokeAwareMethods(beanName, bean)：调用**BeanNameAware**，**BeanClassLoaderAware**，**BeanFactoryAware**中的对应方法

​	g. wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName)：调用BeanPostProcessord中的**postProcessBeforeInitialization**方法

​	h. invokeInitMethods(beanName, wrappedBean, mbd)：执行**InitializingBean**中的**afterPropertiesSet**

​	i. wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName)：调用**BeanPostProcessor**中的**postProcessAfterInitialization**方法



## 总结

以上是spring容器从启动到销毁的全部过程，根据源码陈某画了一张生命周期的图，仅供参考，请勿转载！！！

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/lifecy.png)


## 笔者有话说

- 最近建了一个微信交流群，提供给大家一个交流的平台，扫描下方笔者的微信二维码，备注【交流】，我会把大家拉进群

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20200310211704.jpg)












