####为什么要使用spring 

- 轻量

- 控制反转，依赖注入
- 支持aop编程
- 声明式事务的支持
- junit4的支持，方便测试
- 方便集成其他框架



#### ioc是什么

- ioc容器，管理bean的生命周期，控制着bean的依赖注入
- IoC（控制反转）,将类的创建和依赖关系写在配置文件里，由配置文件注入，由IOC容器在运行期间，动态地将某种依赖关系注入到对象之中实现了松耦合



#### **哪些不同类型的IOC（依赖注入）方式**

- **构造器依赖注入：**构造器依赖注入通过容器触发一个类的构造器来实现的，该类有一系列参数，每个参数代表一个对其他类的依赖。
- **Setter方法注入：**Setter方法注入是容器通过调用无参构造器或无参static工厂 方法实例化bean之后，调用该bean的setter方法，即实现了基于setter的依赖注入。



#### spring的作用域有哪些

- **singleton（默认） :** bean在每个Spring ioc 容器中只有一个实例。
- **prototype：**一个bean的定义可以有多个实例。
- **request：**每次http请求都会创建一个bean，该作用域仅在基于web的Spring ApplicationContext情形下有效。
- **session：**在一个HTTP Session中，一个bean定义对应一个实例。该作用域仅在基于web的Spring ApplicationContext情形下有效。
- **global-session：**在一个全局的HTTP Session中，一个bean定义对应一个实例。该作用域仅在基于web的Spring ApplicationContext情形下有效。



#### Beanfactory和applicationContext的区别

- Beanfactory接口
  - 延迟加载，用到才去进行bean的实例化
- applicationContext接口
  - 容器启动，一次性创建所有bean，在需要的时候无需等待
  - 扩展了beanfactory
  - 增加了统一资源访问
  - 国际化支持
  - 加载多个配置文件 

#### ApplicationContext通常的实现是什么？

- **FileSystemXmlApplicationContext** ：此容器从一个XML文件中加载beans的定义，XML Bean 配置文件的全路径名必须提供给它的构造函数。
- **ClassPathXmlApplicationContext**：此容器也从一个XML文件中加载beans的定义，这里，你需要正确设置classpath因为这个容器将在classpath里找bean配置。
- **WebXmlApplicationContext**：此容器加载一个XML文件，此文件定义了一个WEB应用的所有bean。



#### spring的自动装配有几种方式

- no：不进行自动装配，手动设置依赖

- byName：根据bean的名字进行装配，找不到报错
- byType：根绝bean的类型进行装配，找不到报错
- 构造器：仅适用于与有构造器相同参数的bean，找不到抛异常
- autodetect：有默认构造器用构造器，不然使用byType



####AOP思想

- AOP思想
  - AOP 将安全，事务等于程序逻辑相对独立的功能抽取出来，利用spring的配置文件将这些功能插进去，实现了按照方面编程，提高了复用性

- AOP的实现
  - 采用了动态代理和反射技术，有接口的用动态代理，没接口的用cglib进行动态代理

- 横切性关注点
  - 我们加在主线业务的前后的这些内容叫做横切性关注点

- 通知（advice）
  - 将横切性关注点的内容封装成一个方法，这个方法称作为通知
  - **before：**前置通知，在一个方法执行前被调用
  - **after：**在方法执行之后调用的通知，无论方法执行是否成功
  - **after-returning：**仅当方法成功完成后执行的通知
  - **after-throwing：**在方法抛出异常退出时执行的通知
  - **around：**在方法执行之前和之后调用的通知

- 连接点（joinpoint）
  - 被拦截到的点 ，Spring这些点就是方法，每一个方法就是一个连接点切入点就是要对那些方法进行拦截对需要拦截的joinpoint的定义

- 切面（aspect）
  - 一个个封装了方法称为advice，每一个方法 应该封装在一个类中

- 织入（weave）
  - 将aspect运用到代理目标对象上并导致proxy的创建的过程



####Spring AOP 和 AspectJ的区别

aspectJ

- 他定义了aop语法
- 静态代理，在编译期间将切面代码编译到目标代码中
- 可以切入final等修饰的类
- 需要使用专门的编译器（）来编译代码

aop

- 

AspectJ和Spring AOP都是对目标类增强，生成代理类。
AspectJ是在编译期间将切面代码编译到目标代码的，属于静态代理；

Spring AOP是在运行期间通过代理生成目标类，属于动态代理。
AspectJ是静态代理，故而能够切入final修饰的类，abstract修饰的类；

Spring AOP是动态代理，其实现原理是通过CGLIB生成一个继承了目标类(委托类)的代理类，因此，final修饰的类不能被代理，同样static和final修饰的方法也不会代理，因为static和final方法是不能被覆盖的。在CGLIB底层，其实是借助了ASM这个非常强大的Java字节码生成框架。关于CGLB和ASM的讨论将会新开一个篇幅探讨。

Spring AOP支持注解，在使用@Aspect注解创建和配置切面时将更加方便。

而使用AspectJ，需要通过.aj文件来创建切面，并且需要使用ajc（Aspect编译器）来编译代码；

Spring AOP我们可以使用Aspectj提供的注解；换句话说就是使用Aspectj的语法风格，所以你在做springAop的时候@Aspect；@Before；@PonitCut等等这些注解其实都是Aspectj提供的，不是spring提供的



#### aop的实现原理

- aop自动搭建的aop代理主要分为静态代理和动态代理
- 静态代理
  - AspectJ
- 动态代理
  - jdk自带动态代理，通过反射来接收被代理的类，并要求被代理的类必须实现一个接口
  - CGLIB动态代理，如果被代理类没有实现接口，则会调用CGLIB来动态代理目标类，他是通过集成来进行代理，如果类被声明为final，则无法被代理。

#### 为什么要用动态代理? 
- 动态代理可以让我们不对被代理者的代码进行改动而去添加一下业务
- 对代理者进行隐藏 
- 需要通过反射去实现

####spring的事务管理
​    事务就是对一系列的数据库的操作进行统一的提交或者回滚操作
​    事务分为编程式事物和声明式事务，一般用声明式事务用来控制数据操作的完整性一直性。
​    声明式事务只需要告诉spring那些是事务方法剩下的交给spring就可以

####声明式事务的配置
​    1.我们去 applicationcontext创建事务bean transactionmanager
​    2.去配置事务的属性<tx:advice>
​    3.配置aop <aop:config>

####spring主要用的设计模式

    工厂模式，
    
    单例模式
        必须保证只有一个实例存在，很多时候系统只需要有一个全局对象，确保所有对象访问的同一个实例
    代理模式

####事务的七大传播属性和四大隔离级别和四大特性（https://www.jianshu.com/p/c690761ed7ed ）
​    **传播属性**   

- required：已存在一个事务则加入该事务否则创建一个新事务，spring的默认传播行为
- requires_new：创建新的事务，如果已经存在一个事务则挂起此事务
- supports：如果存在以存在一个事务切加入此事务否则在无事务模式下运行
- not_supported：在无事务模式下运行，如果存在事务则挂起该事务
- mandatory：强制使用事务，不存在事务抛异常
- nested：存在事务，啧将该方法运行在嵌套的事务中， 被嵌套的事物可以从当前事物中单独提交和回        滚，没有事物则创建新的事物
- never： 在无事务的环境下运行，如存在事物则抛出异常。

**事务隔离级别：**

- 序列化 ：最高级别的事务隔离级别，解决了脏读，幻读，不可重复读
- 可重复读： 在select的时候数据不可以修改但是可以新增，会出现幻读
- 读提交： 事物在读取数据的时候立刻释放了读锁导致其他事物可以修改数据，会出现幻读和不可重复读
- 读未提交： 数据在为提交时呗其他事物读取，会出现脏读，幻读和不可重复读

**四大特性（ACID）：**

- **原子性**： 事务是一个整体要么一起干要么都不干，
-  **一致性** ：和原子性密致相关，
- **隔离性** ：事务的操作不会被其他事务干扰，
- **持久性** ：事务提交之后的数据改变是永久性的。

####Spring注解：
​    **@controller** 用于标注控制层的组件
​    **@service** 用于标注业务层的主键
​    **@repository** 用于标注bao层
​    **@component** 用于注解组件

**@autowired与@resourece的区别**

- @autowired是spring的 @resource是jdk的
-  @Autowire 默认按照类型装配，默认情况下它要求依赖对象必须存在如果允许为null，可以设置 required属性为false，如果我们想使用按照名称装配，可 以结合@Qualifier注解一起使用;
- @Resource默认按照名称装配，当找不到与名称匹配的bean才会按照类型装配，可以通过name属性指定，如果没有指定name属 性，当注解标注在字段上，即默认取字段的名称作为bean名称寻找依赖对象，当注解标注在属性的setter方法上，即默认取属性名作为bean名称寻找 依赖对象.