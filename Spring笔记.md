最近在学一些后端的技术，零零散散记了一些相关的知识点，有关Spring的内容基本就集中在这一篇。
看的教程网址：<a href="https://www.bilibili.com/video/BV1Fi4y1S7ix/?spm_id_from=333.1391.0.0&p=42">教程链接</a>
****
### Spring核心概念

#### IoC(Inversion of Control)控制反转

+ 使用对象时，由主动的new来产生对象转变为外部提供对象，此过程中对象创建控制权由程序转移到外部
+ Spring提供一个容器IoC容器来充当“外部"
+ 这个容器复杂对象的创建、初始化等一系列工作，被创建或被管理的对象在IoC容器中统称为Bean

**目标：充分解耦**

+ 使用IoC管理Bean(IoC)
+ 在IoC容器中将有依赖关系的bean进行关系绑定(DI)



#### IoC思路分析

+ IoC管理Service和Dao

+ 用配置将被管理的对象告知IoC容器
+ 用接口获取IoC容器
+ 用接口方法从容器中获取Bean

1）导入spring坐标

2）定义Spring管理的类(接口)

3）创建Spring配置文件，配置对应的类作为Spring管理的Bean 

*注意：bean定义时id属性在同一个上下文中不能重复*



#### DI入门案例

依赖注入，全称 Dependency Injection，简称 DI

依赖关系的维护就称为依赖注入

+ 基于IoC管理Bean
+ Service中使用new形式创建的对象不能保留
+ Service中需要的Dao对象通过方法进入Service中

1）删除使用new形式创建对象的代码

2）提供依赖对象对应的set方法

3）配置service和dao之间的关系

```xml
<property name="现在的属性名称" ref="容器中对应bean的名称">
```



### Bean

#### Bean基础配置

标签

**id:**bean的id，使用容器可以通过id值获取对应的bean，在一个容器中id值唯一

**class:**bean的类型，即配置的bean的店路径名

**name:**bean的别名，可以使用空格分隔多个别名

**scope**:作用范围，可以选择单例或者多例，默认单例

***适合交给容器进行管理的Bean***

+ 表现层对象
+ 业务层对象
+ 数据层对象
+ 工具对象



Controller:控制层，接受前端发送的请求，对请求进行处理，并响应数据

Service:业务逻辑层，处理具体的业务逻辑

Dao:数据访问层，复杂数据访问操作，包括数据的增删改查



#### Bean实例化

实例化Bean的方式

1. 用构造方法(无参)创建对象，如果没有无参构造方法将抛出异常BeanCreationException
2. 静态工厂实例化Bean，

```xml
<bean id="类名" class="工厂类路径" factory-method="构造对象的方法"/>
```

3. 实例工厂初始化Bean

```xml
<bean id="userFactory" class="工厂类路径"/>
<bean id="类名" factory-method="方法" factory-bean="userFactory"/>
```

可以使用接口FactoryBean<>来代替

```java
public class UseDaoFactoryBean implements FactoryBean<UserDao>{
    public UserDao getObject() throws Exception{
        return new UserDaoImpl();
    }
    public Class<> getObjectType(){
        reuturn UserDao.class;
    }
}
```

配置

```xml
<bean id="类名" class="FactoryBean名称路径"/>
```



#### Bean的生命周期

生命周期：从创建到销毁的完整过程

初始化容器

+ 创建对象

+ 执行构造方法

+ 执行属性注入(set)

+ 执行bean初始化方法

  使用bean

  关闭/销毁容器

+ 执行bean销毁方法

bean的生命周期控制：在bean创建后到销毁前做一些事情



#### 加载properties文件

+ 开启context命名空间

```xml
<? xml version="1.0" encoding="UTF-8"?>
< beans xmlns=" http://www.springframework.org/schema/bean s"
     xmlns: xsi=" http://www.w3.org/2001/XMLSchema-instance "
     xmlns: context=" http://www.springframework.org/schema/context "
     xsi:schemaLocation="
    http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd ">
</ beans>
```



+ 使用contex命名空间

```xml
<context:property-placeholder locaion=" "/>
```



+ 使用${}读取加载的属性值

```xml
<property name="username" value="${jdbc.username}"/>
```

+ 不加载系统属性

```xml
<context:property-placeholder location="" system-properties-mode="NEVER"/>
```

+ 加载多个properties文件

```xml
<context:property-placeholder location="jdbc.properties.msg.properties"/>
```

+ 加载所有properties文件

```xml
<context:property-placeholder location="*.properties"/>
```

+ 标准格式

```xml
<context:property-placeholder location="classpath:*.properties"/>
```

+ 从类路径或jar包中搜索并加载properties文件

```xml
<context:property-placeholder location="classpath*:*.properties"/>
```



### 依赖注入

向一个类传递数据的方式

+ 普通方法(set方法)
+ 构造方法

bean运行需要的数据类型可以简单地分成两大类

+ 引用类型
+ 简单类型(基本数据类型与String)



#### Setter注入

引用类型

+ 在bean中定义引用类型属性并提供可访问地set方法

```java
public class BookServiceImpl implements BookService{
    private BookDao bookDao;
    public void setBookDao(BookDao bookDao){
        this.bookDao = bookDao;
    }
}
```



+ 配置中使用property标签ref属性注入引用对象

```xml
<bean id="bookService" class="...BookServiceImpl">
	<property name="bookDao" ref="bookDao"/>
</bean>
<bean id="bookDao" class="....BookDaoImpl"/>
```

简单类型

+ 在bean中定义引用类型属性并提供可访问地set方法

```java
public class BookDaoImpl implements BookDao{
    private int connectionNumber;
    public void setConnectionNumber(int connectionNumber){
        this.connectionNumber = connectionNumber;
    }
}
```



+ 配置中使用property标签value属性注入引用对象

```xml
<bean id="bookDao" class="...BookDaoImpl">
	<property name="connectionNumber" value="10"/>
</bean>
```



#### 构造方法注入

引用类型

```java
public class BookServiceImpl implements BookService{
    private int connectionNumber;
   	public BookServiceImpl(BookDao bookDao){
        this.bookDao=bookDao;
    }
}
```

```xml
<bean id="bookService" class="...BookServiceImpl">
	<constructor-arg name="形参的名称" ref="bookDao"/>
</bean>
```

简单类型

```xml
<bean id="bookService" class="...BookServiceImpl">
	<constructor-arg name="形参的名称" value="xxx"/>
</bean>
```

但是依然有耦合，另外一种写法

type中写对应参数的类型，解决形参名的问题

```xml
<bean id="bookService" class="...BookServiceImpl">
	<constructor-arg type="java.lang.String" value="xxx"/>
</bean>
```

index写参数的位数，第0个，第1个...解决参数类型重复的问题

```xml
<bean id="bookService" class="...BookServiceImpl">
	<constructor-arg index="0" type="java.lang.String" value="xxx"/>
</bean>
```



**依赖注入方式的选择**

强制依赖：bean运行必须的依赖

1. 强制依赖使用构造器进行，使用setter注入又改了不进行注入导致null对象出现
2. 可选以来用setter注入，灵活
3. Spring框架倡导使用构造器，第三方框架内部大多采用构造器注入的形式进行初始化
4. 如有必要可以两者同时使用，使用构造器注入完成强制依赖输入，setter注入完成可选依赖注入
5. 实际开发时要根据实际情况分析，如果受控对象没有提供setter方法就必须使用构造器注入
6. 自己开发的模块推荐使用setter注入



#### 依赖自动装配

IoC容器根据bean所以来的资源在容器中自动查找并注入到bean中的过程称为自动装配

自动装配方式

+ 按类型(常用)
+ 按名称
+ 按构造方法

autowire标签后面可以选择不同的方式

使用byType，如果存在多个bean类型相同会报错，此时可以使用byName

```xml
<bean id="bookService" class="...BookServiceImpl" autowire="byType"/>
```

提示

+ 自动装配用于引用类型依赖注入，不能对简单类型进行操作
+ 使用按类型装配(byType)必须保证容器中相同类型的bean唯一
+ 使用按名称装配(byName)必须保证容器中右指定名称的bean，但是资源变量名与配置会耦合
+ 自动装配优先级低于setter注入和构造器注入，提示出现时自动装配配置失效



#### 集合注入

数组 List Set Map Properties

name属性后面是变量名，和类型无关

```xml
<bean id="bookDao" class="...BookDaoImpl">
	<property name="array">
    	<array>
            <value>100</value>
            <value>200</value>
        </array>
    </property>
    <property name="list">
    	<list>
            <value>ahsoi</value>
            <value>sakjahfkj</value>
        </list>
    </property>
    <property name="set">
    	<set>
            <value>ahsoi</value>
            <value>sakjahfkj</value>
        </set>
    </property>
    <property name="map">
    	<map>
           <entry key="country" value="china"/>
        </map>
    </property>
    <property name="properties">
    	<props>
           <prop key="country">china</prop>
        </props>
    </property>
</bean>
```





### 注解开发

 使用注解配置bean

@Component("bean名称")

要在核心配置文件中通过组件扫描加载bean

```xml
<context:component-scan base-package=" "/>
```

业务层的bean建议使用@Service

数据层使用@Respository

表现层使用@Controller



#### 纯注解开发

用类来代替配置文件，使用注解@Configuration(设定当前类为配置类)

用注解@ComponentScan("")来设定扫描路径，次注解只能加一次

```java
//转换前
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml")
//转换后，写的是配置类的名称
ApplicationContext ctx = new ClassPathXmlApplicationContext(SpringConfig.class)
```



#### bean管理

**bean作用范围**

@Scope注解

**bean生命周期**

@PostConstruct注解，表示在构造方法后运行

@PreDestroy注解，表示在销毁前运行



#### 注解开发依赖注入

自动装配

@Autowired注解，可以把set方法去掉

如果有两个相同类型bean的数据层实现要用名称指定@Qualifier("名称")+@Autowired(前者必须配合后者使用)

注：自动装配基于反射设计创建对象并暴力发射对应属性为私有属性初始化数据，无需提供setter方法

自动装配建议使用无参构造方法创建对象

简单类型注入使用@Value("值")注解

```java
@Value("sjdh")
private String name;
```

可以从properties文件中加载配置，需要在对应的Config类里面加上@PropertySource注解

```java
@Configuration
@ComponentScan("...")
//不能使用通配符*
@PropertySource("xxx.properties")
public class Config(){
    
}
```



#### 第三方bean管理

需要手动获得管理对象

```java
public class SpringConfig{
    //定义一个方法要管理的对象
    //添加注解，表示返回值是bean
    @Bean
    public DataSource datasource(){
        DruidDataSource ds=new DruidDataSource();
        ds.setDriverClassName(" ");
        return ds;
    }
}
```

方式一：导入式

```java
public class SpringConfig{
    //定义一个方法要管理的对象
    //添加注解，表示返回值是bean
    @Bean
    public DataSource datasource(){
       
    }
}
```

使用@Import主角儿手动加入配置类到核心配置，只能添加一次

```java
@Configuration
@Import()
public class SpringConfig{
   
}
```

方式二：扫描式(不建议)



#### 第三方bean依赖注入

引用类型注入只需要为bean定义方法设置形参即可，容器会根据类型自动装配对象

```java
@Bean
public DataSource dataSource(BookService bookService){
    System.out.println(bookService);
    DruidDataSource ds = new DruidDataSource();
    return ds;
}
```



### AOP

#### AOP核心概念

AOP(Aspect Oriented Programming)面向切面编程，一种编程范式，直到开发者如何组织程序结构

作用：在不动原始设计的基础上增强功能

Spring理念：无入侵式

抽取出来的方法称为连接点，要追加功能的方法称为切入点，共性功能称为通知，用切面描述通知与切入点的关系

还要有通知类，连接点式所有方法，切入点是匹配某些方法

连接点(JoinPoint)：程序执行过程中的任意位置

在SpringAOP中理解为方法的执行

切入点(Pointcut)：匹配连接点的式子

通知(Advice)：在切入点处执行的操作，也就是共性功能

切面(Aspect)：描述通知与切入点的对应关系



#### AOP思路

1. 将坐标导入pom.xml文件

```xml
<dependency>
	<groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```



2. 制作连接点方法（原始操作，Dao接口与实现类）

```java
public interface BookDao{
    public void save();
    public void update();
}
```

```java
@Repository
public class BookDaoImpl implements BookDao{
    public void save(){
        System.out.println(System.currentTimeMillis());
        System.out.println("book dao save ... ");
    }
    
    public void update(){
        System.out.println("book dao update ... ");
    }
}
```



3. 定义通知类

```java
public void MyAdvice{
    public void before(){
        System.out.println(System.currentTimeMillis());
    }
}
```



4. 定义切入点@Pointcut注解

```java
public void MyAdvice{
    @Pointcut("executio()")
    private void pt(){}
    
    public void before(){
      
    }
}
```



5. 绑定切入点与通知关系（切面）

```
public void MyAdvice{
    @Pointcut("execution()")
    
    @Before("pt()")
    public void before(){
      
    }
}
```



6. 定义通知类受Spring管理，并定义当前类为切面类

要使用@Aspect和@Component注解



####  AOP工作流程

1. Spring容器启动
2. 读取所有切面配置中的切入点
3. 初始化bean，判顶对应的类中的方法是都撇皮到任意切入点

	如果匹配失败则创建对象

	匹配成功，创建目标对象的代理对象

4. 获取bean执行方法

	获取bean，调用方法并执行，完成操作

	获取的bean式代理对象时，根据代理对象的运行模式运行原始方法与增强的内容，完成操作

**目标对象**

原始功能去掉共性功能对应的类产生的对象，这种对象无法直接完成最后的工作

**代理**

目标对象无法直接完成工作，需要对其进行功能回填，通过原始对象的代理对象实现



#### AOP切入点表达式

切入点：要进行增强的方法

切入点表达式：要进行增强的方法的描述方式

描述方式一：执行该路径下BookDao接口中的无参数update方法

```java
execution(void.com.it.dao.BookDao.update())
```

描述方法二：执行impl包下的BookDaoImpl类中的无参数update方法

```java
execution(void com.it.dao.impl.BookDaoImpl.update())
```



**切入点表达式标准**

动作关键字（访问修饰符 返回值 包名.类/接口名.方法名(参数)异常名）



可以使用通配符描述切入点

*：单个独立的任意符号，可以独立出现，也可以作为前缀或者后缀的匹配符出现

```java
execution (public * com. itheima.*. UserService. find* (*))
```

匹配 com. itheima包下的任意包中的UserService类或接口中所有 find开头的带有一个参数的方法



..：多个连续的任意符号，可以独立出现，常用于简化包名与参数的书写

```java
execution (public User com.. UserService. findById (..))
```

匹配 com包下的任意包中的UserService类或接口中所有名称为findByld的方法



+：专用于匹配子类型

```java
execution(*..* Service+.*(..))
```



**书写技巧**

+ 描述切入点通常描述接口，而不描述实现类
+ 包名书写尽量不使用..匹配
+ 方法名书写档次以进行精准匹配
+ 通常不使用异常作为匹配
+ 接口名/类名书写名称与模块相关的采用*匹配



#### AOP通知类型

分类

+ 前置通知@Before("")
+ 后置通知@After("")
+ 环绕通知@Around("")
+ 返回后通知@AfterReturning
+ 抛出异常后通知@AfterThrowing

@Around注意事项

环绕通知必须依赖形参ProceedingJoinPoint才能实现对原始方法的调用，进而实现原始方法调用前后同时添加通知如果未使用ProceedingJoinPoint对原始方法进行调用将跳过原始方法的执行

如果对原始方法接受返回值，设置成Object类型

环绕通知方法必须抛出Throwable对象



### AOP通知获取数据

#### 获取切入点方法的参数

JoinPoint：适用于前置，后置，返回后、抛出异常后通知

```java
@Berfore()
public void before(JoinPoint jp){
    Object[] args=jp.getArgs();
}
```

ProceedJointPoint：适用于环绕通知，是JoinPoint的子类

#### 获取切入点方法返回值

返回后通知

```java
@AfterReturning(value="",retyrning="形参名")
```

环绕通知

#### 获取切入点方法运行异常异常

抛出异常后通知

环绕通知



### Spring事务

作用：在数据层或业务层保障一系列的数据库操作痛失败痛成功

通过PlatformTransactionManager接口实现

里面有commit和rollback方法

+ 在业务层接口上添加Spring事务管理@Transactional注解(通常添加在业务层接口而不是业务层实现类)

+ 设置事务管理器(JDBC事务，根据实际情况选择)

```java
@Bean
public PlatformTransactionManager transactionManager(DataSource dataSource){
    DataSourceTransactionManager ptm = new DataSourceTransactionManager();
    ptm.setDataSource(dataSource);
    return ptm;
}
```

+ 开启注解式事务驱动

在SpringConfig类上添加@EnableTransactionManagement注解

#### 事务角色

事务管理员：发起事务方，在Spring中通常指代业务层开启事务的方法

事务协调员：加入事务方，在Spring中通常指代数据层方法，也可以是业务层方法

#### 事务属性

事务相关配置

readOnly，rollbackFor等