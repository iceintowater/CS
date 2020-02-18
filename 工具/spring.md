# spring

## ioc

### 定义：
调用类对实现某一接口的类的调用关系由第三方容器注入，对象无需自行创建或者管理依赖关系

### 原理：
ioc容器就是一个大工厂（beanfactory和applicationcontext），管理类以及对象与对象之间的关系
1、通过反射，得到类的所有信息
2、通过解析xml或者通过注解方式得到类与类之间的依赖关系

### bean实例化过程
1、 spring通过xml或者注解读到bean,并放入到bean注册表中
2、 通过bean注册表创建bean的实例
3、 将实例放入到spring缓存池中
4、 使用bean

### applicationcontext中bean加载的具体过程
0、启动容器
1、ResourceLoader加载配置信息，并使用Resource表示配置文件
2、BeanDefinitionReader读取Resource的配置文件，并解析配置文件——把配置文件中的每个<bean>解析成一个BeanDefinition对象，并保存到BeanDefinitionRegistry中。 
3、容器扫描所有的BeanDefinition，使用反射机制识别出Bean工厂后处理器——即实现了BeanFactoryPostProcessor接口的bean，并调用这些Bean工厂后处理器的postProcessBeanFactory方法对BeanDefinition进行加工处理，主要完成以下两项任务： 
a.解析使用占位符的<bean>元素，得到配置值 
b.扫描所有的BeanDefinition，通过java反射机制找出所有属性编辑器的bean——即实现了java.beans.PropertyEditor接口的bean，并将它们注册到spring的属性编辑器注册表中。 
4、调用InstantiationStrategy实例化bean【会对scope为singleton且非懒加载的bean进行实例化】
这里用到了策略模式：InstantiationStrategy接口有一个实现类SimpleInstantiationStrategy，SimpleInstantiationStrategy又有一个实现类CglibSubclassingInstantiationStrategy；SimpleInstantiationStrategy是最常用到的实例化策略；CglibSubclassingInstantiationStrategy利用CGLib为bean动态生成子类，在子类中生成方法注入的逻辑以支持方法注入，然后使用这个动态生成的子类创建Bean的实例。
  
5、实例化bean后，容器主程序调用BeanWrapper的setWrapperInstance(Object obj)方法把实例化后的bean传递到BeanWrapper里。 
6、设置对象属性 BeanWrapper结合BeanDefinition、属性编辑器完成属性注入 
  
7、如果bean实现了BeanNameAware 接口，Spring 传递bean 的ID 到 setBeanName方法。 
8、如果Bean 实现了 BeanFactoryAware 接口， Spring传递beanfactory 给setBeanFactory 方法。 
9、如果Bean实现了ApplicationContextAware接口，会回调该接口的setApplicationContext()方法，传入该Bean的ApplicationContext, 这样该Bean就获得了自己所在的ApplicationContext.
  
  
10、如果有一个Bean实现了BeanPostProcessor接口，并将该接口配置到配置文件中，则会调用该接口的postProcessBeforeInitialization()方法。
11、如果Bean实现了InitializingBean接口，则会回调该接口的afterPropertiesSet()方法。 
12、 如果Bean配置了init-method方法，则会执行init-method配置的方法。 
13、 如果有一个Bean实现了BeanPostProcessor接口，并将该接口配置到配置文件中，则会调用该接口的postProcessAfterInitialization方法。 
14、经过10之后，就可以正式使用该Bean了，对于scope为singleton的Bean, Spring IoC容器会缓存一份该Bean的实例，而对于scope为prototype的Bean, 每次被调用都回new一个对象，而且生命周期也交给调用方管理了，不再是Spring容器进行管理了。 
15. 容器关闭后，如果Bean实现了DisposableBean接口，则会调用该接口的destroy()方法。 
16. 如果Bean配置了destroy-method方法，则会执行destroy-method配置的方法，至此，整个Bean生命周期结束 
总结：整个生命周期要执行的方法可以分为三类： 
（一）Bean自身的方法：实例化Bean对象、设置对象属性、调用init-method方法、调用destory-method方法 
（二）Bean级别接口的方法：BeanNameAware接口的setBeanName()方法、BeanFactoryAware接口的setBeanFactory()方法、ApplicationContextAware接口的setApplicationContext()方法、InitializingBean接口的afterPropertiesSet()方法  
（三）容器级别接口的方法：BeanPostProcessor接口的postProcessBeforeInitialization()方法、BeanPostProcessor接口的     
postProcessAfterInitialization()方法 

## spring mvc 流程
1：启动工程时，系统加载web.xml  
2：当用户输入一个请求时，请求被web.xml中被拦截【过滤器实现拦截】，转到了springmvc.xml中进行处理 
3：由于HandlerMapping有不同的实现类，所以map<url,Handler>映射的规则也不同 
4：HandlerMapping根据请求的url找到相应的Handler，返回给DispatcherServlet 
5：DispactherServlet请求处理器适配器HandlerAdapter去执行Handler(常称为Controller) 
6：处理器适配器(HandlerAdapter)执行Handler 
7：Handler执行完毕后会返回给处理器适配器(HandlerAdapter)一个ModelAndView对象 
8：HandlerAdapter接收到Handler返回的ModelAndView后，将其返回给前端控制器 
9：DispatcherServlet接收到ModelAndView后，会请求视图解析器(View Resolver)对视图进行解析 
10：View Resolver根据View信息匹配到相应的视图结果，反馈给DispatcherServlet 
11：DispatcherServlet收到View具体的视图后，进行视图渲染，将Model中的数据模型填充到View视图中的request域，生成最终的视图View 
12：DispatcherServlet向用户返回请求的结果 


# Spring AOP 

### 定义 
将相同逻辑的重复代码横向抽取出来，使用动态代理技术将这些重复代码织入到目标对象方法中，实现和原来一样的功能 

### 原理  
默认是使用JDK动态代理，如果代理的类没有接口则会使用CGLib代理。 
如果是单例的我们最好使用CGLib代理，如果是多例的我们最好使用JDK代理 

JDK在创建代理对象时的性能要高于CGLib代理，而生成代理对象的运行性能却比CGLib的低。 
如果是单例的代理，推荐使用CGLib 

### 实现 
#### 代理创建 
1、创建代理工厂，代理工厂需要 3 个重要的信息：拦截器数组，目标对象接口数组，目标对象 
2、创建代理工厂时，默认会在拦截器数组尾部再增加一个默认拦截器 —— 用于最终的调用目标方法。 
【每个bean有拦截器，分两层，外层由 Spring 内核控制流程，内层拦截器是用户设置，也就是 AOP】 
3、当调用 getProxy 方法的时候，会根据接口数量大余 0 条件返回一个代理对象 

#### 代理调用 
1、对代理对象进行调用时，就会触发外层拦截器  
2、外层拦截器根据代理配置信息，创建内层拦截器链。创建的过程中，会根据表达式判断当前拦截是否匹配这个拦截器。而这个拦截器链设计模式就是职责链模式 
3、当整个链条执行到最后时，就会触发创建代理时那个尾部的默认拦截器，从而调用目标方法。最后返回 

### 过滤器和拦截器区别 
拦截器是基于java的反射机制的，而过滤器是基于函数回调。 
拦截器不依赖与servlet容器，过滤器依赖与servlet容器。 
拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求起作用。 
拦截器可以访问action上下文、值栈里的对象，而过滤器不能访问。 
在action的生命周期中，拦截器可以多次被调用，而过滤器只能在容器初始化时被调用一次 
 #### 执行顺序 
 filter>service(servlet)>interceptor>controller 
