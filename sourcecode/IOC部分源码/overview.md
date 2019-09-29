##### Spring项目的引入

+ 导入依赖

```xml
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.1.6.RELEASE</version>
    </dependency>
```

**Springcontext依赖于其他几个spring部分，会自动导进来，依赖图如下**

![spring项目依赖](E:\DarkhorseRoad\github\priv\SpingCloud\sourcecode\IOC部分源码\images\Springcontext依赖.png)

##### 容器部分

+ ApplicationContext的继承结构

![ApplicationContext继承结构](E:\DarkhorseRoad\github\priv\SpingCloud\sourcecode\IOC部分源码\images\ApplicationContext结构.png)

+ BeanFactory简介

  ![BeanFactory结构](E:\DarkhorseRoad\github\priv\SpingCloud\sourcecode\IOC部分源码\images\BeanFactory结构.png)

  + 需要注意的几点
    + ApplicationContext 继承了 ListableBeanFactory，这个 Listable 的意思就是，通过这个接口，我们可以获取多个 Bean，最顶层 BeanFactory 接口的方法都是获取单个 Bean 的。
    + ApplicationContext 继承了 HierarchicalBeanFactory，Hierarchical 单词本身已经能说明问题了，也就是说我们可以在应用中起多个 BeanFactory，然后可以将各个 BeanFactory 设置为父子关系。
    + AutowireCapableBeanFactory 这个名字中的 Autowire 大家都非常熟悉，它就是用来自动装配 Bean 用的，但是仔细看上图，ApplicationContext 并没有继承它，不过不用担心，不使用继承，不代表不可以使用组合，如果你看到 ApplicationContext 接口定义中的最后一个方法 getAutowireCapableBeanFactory() 就知道了。
    + ConfigurableListableBeanFactory 也是一个特殊的接口，看图，特殊之处在于它继承了第二层所有的三个接口，而 ApplicationContext 没有。这点之后会用到。

+ 容器启动过程分析

  + ClassPathXmlApplicationContext构造方法

    ```java
        //这个数组用于存放资源（配置文件）
    	@Nullable
    	private Resource[] configResources;
        //如果已经有ApplicationContext，并且需要配置成父子关系，则调用这个构造方法
    	public ClassPathXmlApplicationContext(ApplicationContext parent) {
    		super(parent);
    	}
    	public ClassPathXmlApplicationContext(
    			String[] configLocations, boolean refresh, @Nullable ApplicationContext parent)
    			throws BeansException {
    
    		super(parent);
            //解析配置文件列表，放到上面说的configResources数组中
    		setConfigLocations(configLocations);
    		if (refresh) {
    			refresh();  //核心方法
    		}
    	}
    跟踪refresh()方法
    	@Override
    	public void refresh() throws BeansException, IllegalStateException {
    		synchronized (this.startupShutdownMonitor) {
    			// Prepare this context for refreshing.
    			//准备工作，记录容器的启动时间、标记“已启动”状态，处理配置文件中的占位符
    			prepareRefresh();
    
    			// Tell the subclass to refresh the internal bean factory.
    			
    			 // 这步比较关键，这步完成后，配置文件就会解析成一个个 Bean 定义，注册到 BeanFactory 中，
          // 当然，这里说的 Bean 还没有初始化，只是配置信息都提取出来了，
          // 注册也只是将这些信息都保存到了注册中心(说到底核心是一个 beanName-> beanDefinition 的 map)
    			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();
    
    			// Prepare the bean factory for use in this context.
    			 //设置 BeanFactory 的类加载器，添加几个 BeanPostProcessor，手动注册几个特殊的 bean
    			prepareBeanFactory(beanFactory);
    
    			try {
    				// Allows post-processing of the bean factory in context subclasses.
    				postProcessBeanFactory(beanFactory);
    
    				// Invoke factory processors registered as beans in the context.
    				invokeBeanFactoryPostProcessors(beanFactory);
    
    				// Register bean processors that intercept bean creation.
    				registerBeanPostProcessors(beanFactory);
    
    				// Initialize message source for this context.
    				initMessageSource();
    
    				// Initialize event multicaster for this context.
    				initApplicationEventMulticaster();
    
    				// Initialize other special beans in specific context subclasses.
    				onRefresh();
    
    				// Check for listener beans and register them.
    				registerListeners();
    
    				// Instantiate all remaining (non-lazy-init) singletons.
    				finishBeanFactoryInitialization(beanFactory);
    
    				// Last step: publish corresponding event.
    				finishRefresh();
    			}
    
    			catch (BeansException ex) {
    				if (logger.isWarnEnabled()) {
    					logger.warn("Exception encountered during context initialization - " +
    							"cancelling refresh attempt: " + ex);
    				}
    
    				// Destroy already created singletons to avoid dangling resources.
    				destroyBeans();
    
    				// Reset 'active' flag.
    				cancelRefresh(ex);
    
    				// Propagate exception to caller.
    				throw ex;
    			}
    
    			finally {
    				// Reset common introspection caches in Spring's core, since we
    				// might not ever need metadata for singleton beans anymore...
    				resetCommonCaches();
    			}
    		}
    	}
    
    ```

    

  + 

  + 

    

    



​		