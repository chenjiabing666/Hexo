---
title: Spring解决循环依赖
date: 2019-07-17 01:13:29
categories: Spring
tags: Spring
---

## 导读

- 前几天发表的文章[SpringBoot多数据源动态切换](https://chenjiabing666.github.io/2020/03/12/SpringBoot%E6%95%B4%E5%90%88%E5%A4%9A%E6%95%B0%E6%8D%AE%E6%BA%90%EF%BC%8C%E4%BD%A0%E4%BC%9A%E4%BA%86%E5%90%97%EF%BC%9F/)和[SpringBoot整合多数据源的巨坑](https://chenjiabing666.github.io/2020/03/18/SpringBoot%E6%95%B4%E5%90%88%E5%A4%9A%E6%95%B0%E6%8D%AE%E6%BA%90%E7%9A%84%E5%B7%A8%E5%9D%91/)中，提到了一个坑就是动态数据源添加@Primary接口就会造成循环依赖异常，如下图：

![微信所有码猿技术专栏](https://gitee.com/chenjiabing666/Blog-file/raw/master/circleex.png)

- 这个就是典型的构造器依赖，详情请看上面两篇文章，这里不再详细赘述了。本篇文章将会从源码深入解析Spring是如何解决循环依赖的？为什么不能解决构造器的循环依赖？



## 什么是循环依赖

- 简单的说就是A依赖B，B依赖C，C依赖A这样就构成了循环依赖。

![微信搜索码猿技术专栏](https://gitee.com/chenjiabing666/Blog-file/raw/master/15282870029143.jpg)

- 循环依赖分为构造器依赖和属性依赖，众所周知的是Spring能够解决属性的循环依赖（set注入）。下文将从源码角度分析Spring是如何解决属性的循环依赖。



## 思路

- 如何解决循环依赖，Spring主要的思路就是依据三级缓存，在实例化A时调用doGetBean，发现A依赖的B的实例，此时调用doGetBean去实例B，实例化的B的时候发现又依赖A，如果不解决这个循环依赖的话此时的doGetBean将会无限循环下去，导致内存溢出，程序奔溃。spring引用了一个早期对象，并且把这个"早期引用"并将其注入到容器中，让B先完成实例化，此时A就获取B的引用，完成实例化。



## 三级缓存

- Spring能够轻松的解决属性的循环依赖正式用到了三级缓存，在AbstractBeanFactory中有详细的注释。

```java
	/**一级缓存，用于存放完全初始化好的 bean，从该缓存中取出的 bean 可以直接使用*/
	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

	/**三级缓存 存放 bean 工厂对象，用于解决循环依赖*/
	private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);

	/**二级缓存 存放原始的 bean 对象（尚未填充属性），用于解决循环依赖*/
	private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);
```

- 一级缓存：singletonObjects，存放完全实例化属性赋值完成的Bean，直接可以使用。
- 二级缓存：earlySingletonObjects，存放早期Bean的引用，尚未属性装配的Bean
- 三级缓存：singletonFactories，三级缓存，存放实例化完成的Bean工厂。



## 开撸

- 先上一张流程图看看Spring是如何解决循环依赖的

![微信搜索码猿技术专栏](https://gitee.com/chenjiabing666/Blog-file/raw/master/spring-fix-cirdependence.png)

- 上图标记蓝色的部分都是涉及到三级缓存的操作，下面我们一个一个方法解析

【1】 getSingleton(beanName)：源码如下：

```java
		//查询缓存
		Object sharedInstance = getSingleton(beanName);
		//缓存中存在并且args是null
		if (sharedInstance != null && args == null) {
			//.......省略部分代码
            
     		//直接获取Bean实例
			bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);
		}
		
	//getSingleton源码，DefaultSingletonBeanRegistry#getSingleton
protected Object getSingleton(String beanName, boolean allowEarlyReference) {
    	//先从一级缓存中获取已经实例化属性赋值完成的Bean
		Object singletonObject = this.singletonObjects.get(beanName);
    	//一级缓存不存在，并且Bean正处于创建的过程中
		if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
			synchronized (this.singletonObjects) {
                //从二级缓存中查询，获取Bean的早期引用，实例化完成但是未赋值完成的Bean
				singletonObject = this.earlySingletonObjects.get(beanName);
                //二级缓存中不存在，并且允许创建早期引用（二级缓存中添加）
				if (singletonObject == null && allowEarlyReference) {
                    //从三级缓存中查询，实例化完成，属性未装配完成
					ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
					if (singletonFactory != null) {
						singletonObject = singletonFactory.getObject();
                        	//二级缓存中添加
						this.earlySingletonObjects.put(beanName, singletonObject);
                        //从三级缓存中移除
						this.singletonFactories.remove(beanName);
					}
				}
			}
		}
		return singletonObject;
	}
		


```



- 从源码可以得知，doGetBean最初是查询缓存，一二三级缓存全部查询，如果三级缓存存在则将Bean早期引用存放在二级缓存中并移除三级缓存。（升级为二级缓存）

【2】addSingletonFactory：源码如下

```java

		//中间省略部分代码。。。。。
		//创建Bean的源码，在AbstractAutowireCapableBeanFactory#doCreateBean方法中
		if (instanceWrapper == null) {
            //实例化Bean
			instanceWrapper = createBeanInstance(beanName, mbd, args);
		}
		//允许提前暴露
		if (earlySingletonExposure) {
            //添加到三级缓存中
			addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));
		}
		try {
            //属性装配，属性赋值的时候，如果有发现属性引用了另外一个Bean，则调用getBean方法
			populateBean(beanName, mbd, instanceWrapper);
            //初始化Bean，调用init-method，afterproperties方法等操作
			exposedObject = initializeBean(beanName, exposedObject, mbd);
		}
		}

//添加到三级缓存的源码，在DefaultSingletonBeanRegistry#addSingletonFactory
protected void addSingletonFactory(String beanName, ObjectFactory<?> singletonFactory) {
		synchronized (this.singletonObjects) {
            //一级缓存中不存在
			if (!this.singletonObjects.containsKey(beanName)) {
                //放入三级缓存
				this.singletonFactories.put(beanName, singletonFactory);
                //从二级缓存中移除，
				this.earlySingletonObjects.remove(beanName);
				this.registeredSingletons.add(beanName);
			}
		}
	}
```



- 从源码得知，Bean在实例化完成之后会直接将未装配的Bean工厂存放在**三级缓存**中，并且**移除二级缓存**



【3】addSingleton：源码如下：

```java
//获取单例对象的方法，DefaultSingletonBeanRegistry#getSingleton
//调用createBean实例化Bean
singletonObject = singletonFactory.getObject();

//。。。。中间省略部分代码	

//doCreateBean之后才调用，实例化，属性赋值完成的Bean装入一级缓存，可以直接使用的Bean
addSingleton(beanName, singletonObject);

//addSingleton源码，在DefaultSingletonBeanRegistry#addSingleton方法中
protected void addSingleton(String beanName, Object singletonObject) {
		synchronized (this.singletonObjects) {
            //一级缓存中添加
			this.singletonObjects.put(beanName, singletonObject);
            //移除三级缓存
			this.singletonFactories.remove(beanName);
            //移除二级缓存
			this.earlySingletonObjects.remove(beanName);
			this.registeredSingletons.add(beanName);
		}
	}


				
```

- 总之一句话，Bean添加到一级缓存，移除二三级缓存。



## 扩展

 【1】为什么Spring不能解决构造器的循环依赖？

- 从流程图应该不难看出来，在Bean调用构造器实例化之前，一二三级缓存并没有Bean的任何相关信息，在实例化之后才放入三级缓存中，因此当getBean的时候缓存并没有命中，这样就抛出了循环依赖的异常了。

【2】为什么多实例Bean不能解决循环依赖？

- 多实例Bean是每次创建都会调用doGetBean方法，根本没有使用一二三级缓存，肯定不能解决循环依赖。





## 总结

- 根据以上的分析，大概清楚了Spring是如何解决循环依赖的。假设A依赖B，B依赖A（注意：这里是set属性依赖）分以下步骤执行：

1. A依次执行**doGetBean**、查询缓存、**createBean**创建实例，实例化完成放入三级缓存singletonFactories中，接着执行**populateBean**方法装配属性，但是发现有一个属性是B的对象。
2. 因此再次调用doGetBean方法创建B的实例，依次执行doGetBean、查询缓存、createBean创建实例，实例化完成之后放入三级缓存singletonFactories中，执行populateBean装配属性，但是此时发现有一个属性是A对象。
3. 因此再次调用doGetBean创建A的实例，但是执行到getSingleton查询缓存的时候，从三级缓存中查询到了A的实例(早期引用，未完成属性装配)，此时直接返回A，不用执行后续的流程创建A了，那么B就完成了属性装配，此时是一个完整的对象放入到一级缓存singletonObjects中。
4. B创建完成了，则A自然完成了属性装配，也创建完成放入了一级缓存singletonObjects中。

- Spring三级缓存的应用完美的解决了循环依赖的问题，下面是循环依赖的解决流程图。

![微信搜索码猿技术专栏](https://gitee.com/chenjiabing666/Blog-file/raw/master/cirdependece.PNG)








## 笔者有话说

- 最近建了一个微信交流群，提供给大家一个交流的平台，扫描下方笔者的微信二维码，备注【交流】，我会把大家拉进群

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20200310211704.jpg)







