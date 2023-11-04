##   SpringFramework

**广义的Spring：Spring技术栈(全家桶)**

包括Spring Framework、Spring MVC、SpringBoot、Spring Cloud、Spring Data、Spring security等

其中Spring Framework是其他子项目的基础

**狭义的Spring：Spring Framework(基础框架)**

Spring框架是一个开源的应用程序框架，有SpringSource公司开发，最初是为了解决企业级开发中各种常见

的问题而创建的。它提供了很多功能，例如：依赖注入(Dependency Injection)、面向切面编程(AOP)、声明

式事务管理等。其主要目标是使企业级应用程序的开发变得简单和快速，并且Spring框架被广泛应用于Java

企业开发领域。

**Spring IOC 容器**

IOC容器管理组件。组件：可复用的对象。

### Spring IOC实践和应用

#### 基于xml配置方式组件管理

##### 1.准备项目

创建maven工程，导入SpringIOC相关依赖

```xml
<!--        spring context依赖
            引入spring context依赖后，表示将spring的基础依赖引入了-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>6.0.13</version>
        </dependency>
```

##### 2.基于无参构造/静态工厂类/非静态工厂类的IOC配置

```xml
<!--    1.使用无参数构造函数实例化的组件，进行IOC配置
            <bean> : 一个组件
                id：组件的唯一标识
                class：组件的类的全限定符(利用反射可直接实例化对象)
            将一个组件类声明两个组件信息，默认是单例模式，会实例化两个组件对象
-->
    <bean id="happyComponent" class="com.cbliu.HappyComponent"/>

    <bean id="happyComponent2" class="com.cbliu.HappyComponent"/>
<!--    2.静态工厂类如何声明工厂方法进行IOC的配置
            factory-method指定静态工厂方法(静态方法无需类实例化可直接调用)-->
    <bean id="clientService" class="com.cbliu.ClientService" />

<!--    3.非静态工厂类如何声明IOC配置-->
<!--            3.1配置工工厂类的组件信息-->
    <bean id="defaultServiceLocator" class="com.cbliu.DefaultServiceLocator"  />
<!--            3.2指定非静态工厂对象和方法名 来配置生成的IOC信息(非静态方法需要类实例化才能调用)-->
    <bean id="clientService2" factory-bean="defaultServiceLocator" factory-method="createDefaultServiceLocato
```

##### 3.组件依赖注入(DI)配置

- **基于构造函数的依赖注入**

  准备组件类：

  ```java
  public class UserDao {
  }
  public class UserService {
      private UserDao userDao;
      public UserService(UserDao userDao){
          this.userDao=userDao;
      }
  }
  ```

  ```xml
  <!-- 引用和被引用的组件必须都在IOC容器里-->
  <!--    1.单个构造函数注入-->
  <!--        步骤1：将他们都放在IOC容器里-->
      <bean id="userDao" class="com.cbliu.ioc_02.UserDao"/>
      <bean id="userService" class="com.cbliu.ioc_02.UserService">
  <!--        构造参数传值 DI的配置
                  constructor-arg
                      value:直接属性值
                      ref：引用其他的bean(bean id的值)
                     -->
          <constructor-arg ref="userDao"/>
      </bean>
  <!--	2.多个构造函数注入-->
  <!--        2.1按顺序填写参数-->
      <bean id="userService2" class="com.cbliu.ioc_02.UserService">
  
          <constructor-arg value="18"/>
          <constructor-arg value="张三"/>
          <constructor-arg ref="userDao"/>
      </bean>
  <!--        2.2按参数名字-->
      <bean id="userService3" class="com.cbliu.ioc_02.UserService">
          <constructor-arg name="age" value="18"/>
          <constructor-arg name="name" value="张三"/>
          <constructor-arg name="userDao" ref="userDao"/>
      </bean>
      
  ```

- **<font color="#dd0000">基于Setter方法依赖注入</font>**

  准备组件类

  ```java
  public class MovieFinder {
  }
  public class SimpleMovieLister {
      private MovieFinder movieFinder;
      private String movieName;
      public void setMovieFinder(MovieFinder movieFinder){
          this.movieFinder = movieFinder;
      }
  
      public void setMovieName(String movieName) {
          this.movieName = movieName;
      }
  }
  ```

  ```xml
  <!--    3.触发setter方法进行注入-->
      <bean id="movieFinder" class="com.cbliu.ioc_02.MovieFinder"/>
      <bean id="simpleMovieLister" class="com.cbliu.ioc_02.SimpleMovieLister">
  <!--        name: 属性名(对应的set方法去掉"set",首字母小写，一般为属性名)
              value/ref 二选一-->
          <property name="movieName"  value="肖申克的救赎"/>
          <property name="movieFinder" ref="movieFinder"/>
      </bean>
  ```

##### 4.创建IOC容器

```java
		//创建容器，选择合适的容器实现即可
        /**
         * 接口：BeanFactory
         *      ApplicationContext
         * 实现类：
         *      可以直接通过构造函数实例化
         *      ClassPathXmlApplicationContext 读取类路径下的xml配置方式 classes
         *      FileSystemXmlApplicationContext 读取指定文件位置的xml配置方式
         *      AnnotationConfigApplicationContext 读取配置类方式的ioc容器
         *      WebApplicationContext web项目专属的配置的ioc容器
         */
		//方式1：直接创建容器并且指定配置文件即可(推荐)
        //构造函数(String... 配置文件)
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-03.xml");
        //方式2：先创建ioc容器对象，再指定配置文件，再刷新
        ClassPathXmlApplicationContext applicationContext2 = new ClassPathXmlApplicationContext();
        applicationContext2.setConfigLocation("spring-03.xml");
        applicationContext2.refresh();

        //根据bean id获取组件
        HappyComponent happyComponent = (HappyComponent) applicationContext.getBean("happyComponent");
        HappyComponent happyComponent2 = applicationContext.getBean("happyComponent",HappyComponent.class);
		//根据类获取组件
		//如果ioc容器中存在多个同类型的bean，会出现：NoUniqueBeanDefinitionException
        HappyComponent happyComponent3 = applicationContext.getBean(HappyComponent.class);
        happyComponent3.doWork();
        System.out.println(happyComponent == happyComponent2);
        System.out.println(happyComponent3 == happyComponent2);
```

##### 5.高级特性：组件作用域和周期方法

- 周期方法

  1.声明周期方法

  ```java
  public class JavaBean {
  
      /**
       * public void 无参数
       * 初始化
       */
      public void init(){
          System.out.println("JavaBean init");
      }
  
      public void destroy(){
          System.out.println("JavaBean destroy");
      }
  
  }
  
  ```

  2.xml配置

  ```xml
  <!--    init-method= 初始化方法名
          destroy-method= 销毁方法名-->
      <bean id="javaBean" class="com.cbliu.ioc_04.JavaBean" init-method="init" destroy-method="destroy"/>
  ```

  3.触发

  ```java
  		//1.创建ioc容器 组件就会进行初始化 -> init()
          ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-04.xml");
          //2.正常结束容器 ->destroy()
          applicationContext.close();
  ```

- 作用域

  ```xml
  <!--    默认是单例模式 scope="singleton",多次调用getBean()时只会获取同一个实例
              prototype 多例模式，多次调用getBean()时每次都获取新的实例-->
      <bean id="javaBean2" class="com.cbliu.ioc_04.JavaBean2" scope="prototype"/>
  ```

##### 5.FactoryBean的使用

```java
public class JavaBeanFactoryBean implements FactoryBean<JavaBean> {


    @Override
    public JavaBean getObject() throws Exception {
        //实例化对象
        return new JavaBean();
    }

    @Override
    public Class<?> getObjectType() {
        return JavaBean.class;
    }
}
<!--    id : getObject方法返回的对象的标识
        class: factoryBean标准化工厂类-->
    <bean id="javaBean" class="com.cbliu.ioc_05.JavaBeanFactoryBean"/>
        
      //读取组件
      JavaBean javaBean = applicationContext.getBean(JavaBean.class);
      System.out.println("javaBean="+javaBean);
      //factoryBean也会被实例化，name前加&
      Object bean = applicationContext.getBean("&javaBean");
      System.out.println("bean="+bean);
```

##### 6.XML配置方式总结

- 注入的属性必须添加setter方法、代码结构乱
- 配置文件和代码分离、编写不方便
- XML配置文件解析效率低
- XML配置方式已逐渐被淘汰，但可以用来加深理解IOC容器组件管理、依赖注入的流程

#### 基于注解方式管理组件

1.类上添加IOC注解(相当于<bean id="" class="类全限定符">)

2.在xml中配置生效包的信息(告诉SpringIOC哪些包下添加了IOC注解)

##### 组件添加标记注解

| 注解        | 说明                                                         |
| :---------- | :----------------------------------------------------------- |
| @Component  | 表示Spring中的Bean，它是一个泛化的概念，仅仅表示容器中的一个组件，并且可以作用在应用的任何层次 |
| @Repository | 用于数据库访问层                                             |
| @Service    | 用于业务层                                                   |
| @Controller | 通常用于controller层，如Spring MVC中的Controller             |

@Repository、@Service、@Controller三个注解本质上和@Component没有任何区别，仅仅是为了方便区分。

组件名默认为类名小写,@Component(value="xxx")可以指定组件名

##### 配置文件扫描范围

```xml
<!--    1.普通配置包扫描(最常用)
            base-package 指定ioc容器去哪些包下查找注解类
            一个包或多个包，指定包相当于指定了子包下的所有类-->
    <context:component-scan base-package="com.cbliu.ioc_01"/>
<!--    2.指定包，排除某些注解-->
    <context:component-scan base-package="com.cbliu.ioc_01">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
    </context:component-scan>
<!--    3.指定包，指定包含注解
            use-default-filters : 指定包的所有注解先不生效-->
    <context:component-scan base-package="com.cbliu.ioc_01" use-default-filters="false">
<!--        只扫描指定的注解-->
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
    </context:component-scan>
```

##### 周期方法/作用域

```java
	@PostConstruct
    public void init(){
        System.out.println("init ");

    }
    @PreDestroy
    public void destroy(){
        System.out.println("destroy");
    }
```

作用域：*@Scope(scopeName = ConfigurableBeanFactory.SCOPE_SINGLETON)*;**SCOPE_SINGLETON:单例，SCOPE_PROTOTYPE:多例**

##### 依赖注入

@Autowired 注解自动装配：ioc容器中查找符合类型的组件对象，设置给当前属性(添加该注解的属性)

可添加在字段、字段对应的set方法、构造函数上方(构造函数注入时可省略注解)。

**推荐使用构造函数装配,并将字段设为final**

##### Value注解

一般用于读取配置文件的值

```java
@Component
public class JavaBean {
	//:后面为默认值，若配置文件找不到则赋默认值
    @Value("${cbliu.name:admin}")
    private String name;
    @Value("${cbliu.password}")
    private String password;
}
```

```xml
	<context:component-scan base-package="com.cbliu.ioc_03"/>
    <context:property-placeholder location="classpath:jdbc.properties"/>
```

#### 基于配置类管理组件

替代xml配置 ==> 完全注解开发

```java
@ComponentScan({"com.cbliu","com.cbliu.ioc_01"})
@PropertySource("classpath:jdbc.properties")
@Configuration
public class JavaConfiguration {
    
 	@Value("${cbliu.url}")
    private String url;

    @Value("${cbliu.driver}")
    private String driver;

    @Value("${cbliu.dbusername}")
    private String name;

    @Value("${cbliu.password}")
    private String password;

    /**
     * <Bean />  ==> 一个方法
     * 方法的名字 ==> bean的id
     * 方法体可自定义实现过程
     * 添加@Bean注解使创建的组件加入IOC容器
     * @Bean(name ="xxx")指定bean的名字
     * 周期方法指定：PostConstruct/PreDestroy 注解指定；bean属性指定：@Bean(initMethod="",destroyMethod="")
     * 作用域：@Scope()
     */
    @Bean
    public DruidDataSource dataSource(){
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setUrl(url);
        druidDataSource.setDriverClassName(driver);
        druidDataSource.setUsername(name);
        druidDataSource.setPassword(password);
        return druidDataSource;
    }
    
    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource){
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        //依赖IOC其他组件
        //1.直接调用方法
        //jdbcTemplate.setDataSource(dataSource());
        //2.形参列表声明
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }

}
//配置引用
@Import(value = {JavaConfigurationA.class})
@Configuration
public class JavaConfigurationA {
}
```

```java
AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(JavaConfiguration.class);
StudentController bean = applicationContext.getBean(StudentController.class);
```

### 整合Spring5-Test5搭建测试环境

不需要每次测试都自己创建IOC容器；Bean可以在测试类中自动装配

- 导入相关依赖

  ```xml
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>6.0.13</version>
  </dependency>
  <dependency>
          <groupId>org.junit.jupiter</groupId>
          <artifactId>junit-jupiter</artifactId>
          <version>5.10.0</version>
  </dependency>
  ```

- 使用

  ```java
  //@SpringJUnitConfig(location = 指定配置文件xml，value = 指定配置类)
  @SpringJUnitConfig(value = JavaConfig.class)
  public class SpringIocTest {
  
      private ComponentA componentA;
  
      @Test
      public void test(){
          System.out.println(componentA);
      }
  
  }
  ```

### Spring AOP面向切面编程

代理方式可以解决附件功能代码干扰核心代码和不方便统一维护的问题。代理方式将附加功能代码提取到代理中执行，不干扰核心代码，但是不论使用静态代理还是使用动态代理(jdk,cglib),程序员的工作都比较繁琐，需要自己编写代理工厂。**Spring AOP框架可以简化动态代理的实现。**

#### 使用步骤

##### 1.引入依赖

```xml
<dependency>
<!-- 	包含于spring-context -->
   <groupId>org.springframework</groupId>
   <artifactId>spring-aop</artifactId>
   <version>6.0.13</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>6.0.13</version>
</dependency>
```

##### 2.创建增强类

```java
@Component
@Aspect
public class LogAdvice {

    /**
     * 1.定义方法存储增强代码
     *      具体定义几个方法，根据插入的位置决定
     * 2.使用注解配置，指定插入目标方法的位置
     *      前置：@Before
     *      后置：@AfterReturning
     *      异常：@AfterThrowing
     *      最后：@After
     *      环绕：@Around
     *      try{
     *          前置
     *          目标方法执行
     *          后置
     *      }catch(){
     *          异常
     *      }finally{
     *          最后
     *      }
     *  3.配置切点表达式 ("execution(* com.cbliu.*.*(..))")
     *  4.补全注解
     *      加入ioc容器 @Component
     *      配置切面 @Aspect = 切点 + 增强
     */
    
    /**
 	* 增强方法中获取目标方法信息
 	*  1.全部增强方法中，获取目标方法的信息(方法名，参数，访问修饰符，修饰的类的信息)
 	*      (JoinPoint joinPoint)joinPoint包含目标方法的信息
 	*  2.返回的结果  -@AfterReturning
 	*      (Object result)result接收返回的结果
 	*      @AfterReturning(value = "execution(* com.cbliu.*.*(..))",returning = "result")
 	*  3.异常的信息
 	*      (Throwable throwable)throwable接收返回的结果
 	*      @AfterThrowing(value = "execution(* com.cbliu.*.*(..))",throwing = "throwable")
	*/

    @Before("execution(* com.cbliu.*.*(..))")
    public void before(JoinPoint joinPoint){
        //类名
        String simpleName = joinPoint.getTarget().getClass().getSimpleName();
        //方法名
        String name = joinPoint.getSignature().getName();
        //访问修饰符
        int modifiers = joinPoint.getSignature().getModifiers();
        String s = Modifier.toString(modifiers);
        //获取参数列表
        Object[] args = joinPoint.getArgs();

    }

    @AfterReturning(value = "execution(* com.cbliu.*.*(..))",returning = "result")
    public void afterReturn(JoinPoint joinPoint,Object result){


    }

    @After("execution(* com.cbliu.*.*(..))")
    public void after(JoinPoint joinPoint){

    }

    @AfterThrowing(value = "execution(* com.cbliu.*.*(..))",throwing = "throwable")
    public void afterThrowing(JoinPoint joinPoint,Throwable throwable){

    }

}

```

##### 3.注解配置

*@EnableAspectJAutoProxy*注解配置

```java
@ComponentScan("com.cbliu")
@Configuration
@EnableAspectJAutoProxy
public class JavaConfig {
}
```

#### 切点表达式

固定语法：execution(1 2 3.4.5(6))

**1.访问修饰符** 

public/private

**2.方法的返回参数类型**

若不考虑访问修饰符和返回值，两位合在一起写*。不可以一个不考虑一个考虑

**3.包的位置**

具体包：com.cbliu.service.impl

单层模糊：com.cbliu.service.*		* 单层模糊

多层模糊：com..impl		.. 任意层的模糊(..不能作为开头)

**4.类的名称**

具体：XxxImpl，模糊：\*，部分模糊：\*Impl

**5.方法名**

语法同类名

**6.形参数列表**

没有参数：()，有具体参数(String)；(String,int)

模糊参数：(..)，部分模糊(String..),(..int),(String..int)

#### 切点表达式复用

方法1：

```java
@Pointcut("execution(* com.cbliu.*.*(..))")
public void pc(){

}

@Before("pc()")
public void before(JoinPoint joinPoint){
}
```

方法2：

**使用一个专门的类来统一维护（推荐方式）**

```java
@Component
public class MyPointCut {
    @Pointcut("execution(* com.cbliu.*.*(..))")
    public void pc(){}

    @Pointcut("execution(* com..impl.*.*(..))")
    public void myPc(){}
}

@Component
@Aspect
public class MyAdvice {
    
    @Before("com.cbliu.pointcut.MyPointCut.pc()")
    public void before(JoinPoint joinPoint){

    }
}
```

#### 环绕通知

```java
@Component
@Aspect
public class TxAroundAdvice {

    /**
     * 环绕通知，在通知中，自定义目标方法的执行
     * @param joinPoint (目标方法，多了个执行方法)
     * @return
     */
    public Object transaction(ProceedingJoinPoint joinPoint){

        Object[] args = joinPoint.getArgs();
        Object proceed = null;
        try {
            System.out.println("开启事务");
            proceed = joinPoint.proceed(args);
            System.out.println("事务提交");
        } catch (Throwable e) {
            System.out.println("事务回滚");
            throw new RuntimeException(e);//必须抛出去
        }finally {
    
        }
        return proceed;
    }

}
```

#### 切面优先级

@Order(int value)//value值越小优先级越高，优先级高的先执行

### Spring 声明式事务

引入依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>6.0.13</version>
</dependency>
```

类配置

```java
@EnableTransactionManagement//开启事务注解的支持
public class JavaConfig {
 	@Bean
    public TransactionManager transactionManager(DataSource dataSource){
        //内部要进行事务的操作，基于的连接池
        DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
        //需要连接池对象
        dataSourceTransactionManager.setDataSource(dataSource);
        return dataSourceTransactionManager;

    }
}
```

使用

```java
/**
 * 添加事务：
 * @Transactional
 *      位置：方法/类
 *      方法：当前方法事务
 *      类：类下所有方法事务
 *	1.只读模式 @Transactional(readOnly = true)
 *		只读模式可以提升查询事务的效率，若代码中只有查询代码，推荐使用只读模式
 *		默认为false
 *	2.超时时间 @Transactional(timeout = int)
 *		默认: -1，永不超时，timeout单位秒，超过时间，就会回滚事务和释放异常
 *	3.指定异常回滚，指定异常不回滚
 *		@Transactional(rollbackFor = Exception.class)默认指定运行时异常才会回滚
 *		@Transactional(noRollbackFor = XxxException.class)指定异常不回滚，默认null
 *	4.隔离级别设置
 *		推荐设置读已提交，可避免脏读@Transactional(isolation=Isolation.READ_COMMITTED)
 	5.事务的传播
 		propagation属性：
 			Popagation.REQUIRED:如果父方法有事务就加入，否则就新建自己独立
 			Popagation.REQUIRES_NEW:不论父方法有没有事务，都新建事务，自己独立
 *
 *
 */
@Transactional
public void changeInfo(){
    studentDao.updateAgeById(88,1);
    int i= 1/0;
    System.out.println("--------------");
    studentDao.updateNameById("test2",1);
}
```

### Spring 核心掌握总结

| 核心点         | 掌握目标                                           |
| -------------- | -------------------------------------------------- |
| spring框架理解 | spring家族和spring framework框架                   |
| spring核心功能 | ioc/di, aop, tx                                    |
| spring ioc/di  | 组件管理、ioc容器、ioc/di、三种配置方式            |
| spring aop     | aop和aop框架和代理技术、基于注解的aop配置          |
| spring tx      | 声明式和编程式事务、动态事务管理器、事务注解、属性 |

完结~
