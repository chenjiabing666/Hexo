---
title: SpringBoot整合多数据源的巨坑
date: 2020-03-18 16:51:57
categories: 杂谈
tags: Spring Boot
---
## 导读

- 本篇文章接上篇[SpringBoot整合多数据源，你会了吗？](https://chenjiabing666.github.io/2020/03/12/SpringBoot%E6%95%B4%E5%90%88%E5%A4%9A%E6%95%B0%E6%8D%AE%E6%BA%90%EF%BC%8C%E4%BD%A0%E4%BC%9A%E4%BA%86%E5%90%97%EF%BC%9F/)，前面文章最后留了几个问题供大家思考，今天一一揭晓。



## 配置如何优化

- 上文整合的过程中的还顺带整合Mybatis和TransactionManager，为什么还要重新定义他们呢？SpringBoot不是给我们都配置好了吗？注意，此处优化就是这两个配置去掉，直接用SpringBoot的自动配置，顿时高级了，别人一看你的代码如此简单就实现了多数据源的切换，牛叉不？
- 如何去掉？SpringBoot万变不离自动配置类，且看MybatisAutoConfiguration，如下：

```java
@org.springframework.context.annotation.Configuration
@ConditionalOnClass({ SqlSessionFactory.class, SqlSessionFactoryBean.class })
@ConditionalOnSingleCandidate(DataSource.class)
@EnableConfigurationProperties(MybatisProperties.class)
@AutoConfigureAfter(DataSourceAutoConfiguration.class)
public class MybatisAutoConfiguration implements InitializingBean {
```

- 不多帖了，都是废话，看前几行就行了，醒目的一行啊，`@ConditionalOnSingleCandidate(DataSource.class)`，什么鬼？该注解的意思就是IOC容器中只有一个指定的候选对象才起作用，但是我们注入了几个DataSource，足足三个啊，这还起作用吗？那不废话嘛。
- 事务管理器也是一样，且看`DataSourceTransactionManagerAutoConfiguration`，如下：

```java
public class DataSourceTransactionManagerAutoConfiguration {

	@Configuration
	@ConditionalOnSingleCandidate(DataSource.class)
	static class DataSourceTransactionManagerConfiguration {
```

- 又看到了什么，`@ConditionalOnSingleCandidate(DataSource.class)`同样的醒目，mmp，这不玩我呢吗。这怎么搞？
- 咦，不着急，此时就要看看`@ConditionalOnSingleCandidate`注解搞了什么，进去看看，有如下的介绍：

```java
The condition will also match if multiple matching bean instances are already contained in the BeanFactory but a primary candidate has been defined; essentially, the condition match if auto-wiring a bean with the defined type will succeed.
```

- 什么鬼，看不懂，英语太差了吧，不着急，陈某给大家推荐一个IDEA插件，文档翻译更加专注于程序员的专业术语，不像xx度翻译，如下：

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/transac1.png)

- 好了，翻译准确了就知道了，大致意思就是IOC容器中允许你有多个候选对象，但是你必须有一个主（primary）候选对象，顿时灵光一现，这不就是@Primary注解吗，艹，我这也太优秀了吧。
- 二话不说，直接开撸，轻轻松松一个注解搞定，此时的数据源配置变得简单多了，如下：

```java
/**
 * @Description 数据源的配置
 * @Author CJB
 * @Date 2020/3/9 13:45
 */
@Configuration
@MapperScan(basePackages = {"com.vivachek.service.dao","com.vivachek.service.dao2"})
public class DatasourceConfig {

    /**
     * 注入数据源1
     */
    @ConfigurationProperties(prefix = "spring.datasource1")
    @Bean(value = "dataSource1")
    public DataSource dataSource1() {
        return new DruidDataSource();
    }

    /**
     * 第二个数据源
     */
    @Bean(name = "dataSource2")
    @ConfigurationProperties(prefix = "spring.datasource2")
    public DataSource dataSource2() {
        return new DruidDataSource();
    }

    /**
     * 动态数据源
     *
     * @return
     */
    @Bean
    @Primary
    public DynamicDataSource dynamicDataSource() {
        DynamicDataSource dataSource = new DynamicDataSource();
        //默认数据源，在没有切换数据源的时候使用该数据源
        dataSource.setDefaultTargetDataSource(dataSource2());
        HashMap<Object, Object> map = Maps.newHashMap();
        map.put("dataSource1", dataSource1());
        map.put("dataSource2", dataSource2());
        //设置数据源Map，动态切换就是根据key从map中获取
        dataSource.setTargetDataSources(map);
        return dataSource;
    }
}
```

- 直接在`DynamicDataSource`添加了一个@Primary就省去了SqlSessionFactory和TransactionManager的手动配置，是不是很easy并且显得自己很牛叉，太有成就感了.....
- 好了，牛也吹了，运行一下吧，满怀期待等待30秒.......，what？什么鬼？失败了，抛出了异常，如下：

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/circleex.png)

- 什么鬼，循环依赖异常，搞什么飞机，一万个草泥马在奔腾在横无际涯的草原上。。。。。。。。
- 别急，还有后续，关注我，将会定时更新后续文章。另外需要源码的联系我，微信联系方式在[个人独立博客](https://chenjiabing666.github.io/)【关于我】中，加我注明来意，谢谢。
- 别忘了点赞哟，多来走动走动呗..........



## 动态路由数据源添加@Primary报循环依赖异常

- 前面文章[Spring解决循环依赖](https://chenjiabing666.github.io/2019/07/17/Spring%E8%A7%A3%E5%86%B3%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96/)有说过Spring对于循环依赖是完全能够解决的，没有读过的小伙伴建议看一下，里面详细的讲述了Spring是如何解决循环依赖的，此处就不再赘述了。
- 既然Spring能够解决循环依赖，为什么这里又会报循环依赖的异常呢？我们不妨跟着代码看看是怎样的循环依赖，如下：

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/circleex.png)

- 上面两个数据源都是自己定义的，先不用看，那么肯定是`DataSourceInitializerInvoker`造成的循环依赖了，果不其然，其中确实依赖了DataSource，源码如下：

```java
DataSourceInitializerInvoker(ObjectProvider<DataSource> dataSource, DataSourceProperties properties,
			ApplicationContext applicationContext) {
		this.dataSource = dataSource;
		this.properties = properties;
		this.applicationContext = applicationContext;
	}

```

- what？即使依赖了又怎样？Spring不是可以解决循环依赖吗？别着急下面分析
- ObjectProvider应该不陌生吧，其实内部就是从IOC容器中获取Bean而已，但是，转折来了......... ，这是什么，这是构造器，Spring能解决构造器的循环依赖吗？答案是不能，所以原因找到了，这里不再细说了，欲知原因请读[Spring解循环依赖](https://chenjiabing666.github.io/2019/07/17/Spring%E8%A7%A3%E5%86%B3%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96/)
- 问题找到了，如何解决？此时心中一万个草泥马奔腾，怎么解决呢？

- 哈哈，此时插播一条广告，本人的独立博客已经发布了很多文章，感兴趣的可以收藏一下，【关于我】中有我的微信联系方式，欢迎交流。
- 回到正题，如何解决？很简单，找到这个`DataSourceInitializerInvoker`是什么时候注入到IOC容器中的，因此我们找到了`DataSourceAutoConfiguration`，继而找到了`DataSourceInitializationConfiguration`这个配置类，源码如下：

```java
@Configuration
@ConditionalOnClass({ DataSource.class, EmbeddedDatabaseType.class })
@EnableConfigurationProperties(DataSourceProperties.class)
@Import({ DataSourcePoolMetadataProvidersConfiguration.class, DataSourceInitializationConfiguration.class })
public class DataSourceAutoConfiguration {
    @Configuration
	@Conditional(EmbeddedDatabaseCondition.class)
	@ConditionalOnMissingBean({ DataSource.class, XADataSource.class })
	@Import(EmbeddedDataSourceConfiguration.class)
	protected static class EmbeddedDatabaseConfiguration {

	}

	@Configuration
	@Conditional(PooledDataSourceCondition.class)
	@ConditionalOnMissingBean({ DataSource.class, XADataSource.class })
	@Import({ DataSourceConfiguration.Hikari.class, DataSourceConfiguration.Tomcat.class,
			DataSourceConfiguration.Dbcp2.class, DataSourceConfiguration.Generic.class,
			DataSourceJmxConfiguration.class })
	protected static class PooledDataSourceConfiguration {

	}   
}
    
    
@Configuration
@Import({ DataSourceInitializerInvoker.class, DataSourceInitializationConfiguration.Registrar.class })
class DataSourceInitializationConfiguration {
```

- 贴了那么多代码谁看的懂？草泥马又奔腾了，可以看到源码中出现了两次`@ConditionalOnMissingBean({ DataSource.class, XADataSource.class })`，这什么鬼，不多说了，相信读过SpringBoot源码的都知道，这个配置类根本不起作用啊，那还要它干嘛，直接搞掉不就完事了。好了，分析到这里终于知道解决的方案了，搞掉`DataSourceAutoConfiguration`，怎么搞呢？一个注解搞定。

```java
//排除配置类
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
```

- 问题迎刃而解了，简单不，惊喜不，不好，又奔腾了。。。。


## 笔者有话说

- 最近建了一个微信交流群，提供给大家一个交流的平台，扫描下方笔者的微信二维码，备注【交流】，我会把大家拉进群

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20200310211704.jpg)

















