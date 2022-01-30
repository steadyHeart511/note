# Spring



---

 

## 一、spring概述

> spring 是轻量级的开源javaee框架，它可以解决企业应用开发的复杂性



**spring有两个核心部分：IOC和AOP**

1. IOC：控制反转，把对象的创建过程交给spring进行管理
2. AOP：面向切面编程，不修改源代码进行功能增强



**spring特点：**

1. 方便解耦，简化开发
2. 支持AOP
3. 方便程序测试
4. 方便和其他框架进行整合
5. 方便进行事务操作
6. 降低API开发难度





## 二、IOC



### 2.1	IOC概念

> 将对象创建与对象之间的调用过程交给spring进行管理。
>
> 好处：降低了耦合度



### 2.2	IOC原理



> 这种调用方法使userService与userdao的耦合度太高

![image-20220111153208954](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220111153208954.png)







> **引入工厂模式之后**，降低了userService与userdao的耦合度，但是userService和userfactory耦合度太高了

![image-20220111153321106](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220111153321106.png)



> IOC,当一些信息变动时可以在xml中进行改动，这就大大降低了类之间的耦合度

![image-20220111155119836](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220111155119836.png)





> IOC思想基于IOC容器完成，IOC容器底层就是对象工厂



**spring提供了IOC容器实现的两种方式：**

1. BeanFactory

   > IOC容器基本实现，是spring内部的使用接口，不提供开发人员使用
   >
   > **特点**：加载配置文件时不会创建对象，在获取对象（使用）才去创建对象

2. ApplicationContext

   > BeanFactory接口的子接口，提供更多强大的功能，一般由开发人员使用
   >
   > **特点**：加载配置文件时就会把配置文件里的对象进行创建



### 2.3	Bean管理



1. **spring创建对象**
2. **spring注入属性**



#### 2.3.1	实现方式



##### 2.3.1.1	基于xml文件



###### 2.3.1.1.1	创建对象



![image-20220112102709348](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220112102709348.png)



###### 2.3.1.1.2	注入属性



**set注入：**



> set注入创建对象时调用的是无参构造器



![image-20220112104831518](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220112104831518.png)





**有参构造器注入**：



> 有参构造器注入创建对象时调用的是对应的有参构造器



![image-20220112105000125](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220112105000125.png)





**注入其他属性**：

![image-20220112112128288](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220112112128288.png)



**注入外部bean：**



![image-20220112155044988](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220112155044988.png)





**注入内部bean：**



> 注入内部bean也是一种级联赋值，级联赋值就是给这个对象赋值的同时并给另一个对象赋值



![image-20220112160727731](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220112160727731.png)





**级联赋值**：



![image-20220112161814184](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220112161814184.png)



![image-20220112161837800](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220112161837800.png)



![image-20220112161858111](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220112161858111.png)







**注入集合：**   

> 将集合注入写到一个bean标签有缺陷：
>
> 	1. **不能共享这些集合**
> 	1. 况且**value**标签**不支持除String以外其他的引用类型**

```java
<bean id="stu" class="com.lts.Student">

        <!--注入集合-->
        
        <!--注入数组-->
        <property name="courses">
            <array>
                <value>夜曲</value>
                <value>花海</value>
            </array>
        </property>

        <!--注入list集合-->
        <property name="list">
            <list>
                <value>杨紫</value>
                <value>白鹿</value>
            </list>
        </property>

        <!--注入set集合-->
        <property name="sets">
            <set>
                <value>李沁</value>
                <value>赵丽颖</value>
            </set>
        </property>

        <!--注入map集合-->
        <property name="maps">
            <map>
                <entry key="JAVA" value="java"></entry>
                <entry key="PYTHON" value="python"></entry>
            </map>
        </property>
    </bean>
```



**解决value不支持除String以外的其他引用类型：**

> 用ref替代value



```java
	<bean id="stu1" class="com.lts.Student">
        <property name="courseList">
            
            <!--采用ref标签给集合注入引用类型对象-->
            <list>
                <ref bean="co1"></ref>
                <ref bean="co2"></ref>
            </list>
        </property>
    </bean>

    <bean id="co1" class="com.lts.Course">
        <property name="cname" value="Jike"></property>
    </bean>
    
    <bean id="co2" class="com.lts.Course">
        <property name="cname" value="dianshang"></property>
    </bean>
```



**把集合注入部分提取出来**

> 在 spring 配置文件中引入名称空间 util

```java
（1）在 spring 配置文件中引入名称空间 util
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns:util="http://www.springframework.org/schema/util"(要加的)
 xsi:schemaLocation="http://www.springframework.org/schema/beans 
http://www.springframework.org/schema/beans/spring-beans.xsd
 http://www.springframework.org/schema/util (要加的)
http://www.springframework.org/schema/util/spring-util.xsd"> (要加的)


（2）使用 util 标签完成 list 集合注入提取
    
<!--1 提取 list 集合类型属性注入--> 
  <util:list id="bookList">
 	<value>易筋经</value>
 	<value>九阴真经</value>
 	<value>九阳神功</value>
</util:list>
    
<!--2 提取 list 集合类型属性注入使用--> 
<bean id="book" class="com.atguigu.spring5.collectiontype.Book">
 <property name="list" ref="bookList"></property>
</bean>
```



###### 2.3.1.1.3	自动装配



**什么是自动装配**

>  根据指定装配规则（属性名称或者属性类型），Spring 自动将匹配的属性值进行注入
>
> bean 标签属性 autowire，配置自动装配, autowire 属性常用两个值：
>
>  *byName 根据属性名称注入 ，注入值 bean 的 id 值和类属性名称一样*
>
>  *byType 根据属性类型注入*



**根据名字**：

> byName 根据属性名称注入 ，**注入值 bean 的 id 值和类属性名称一样**



```java
<bean id="emp" class="com.atguigu.spring5.autowire.Emp" autowire="byName"></bean> 
<bean id="dept" class="com.atguigu.spring5.autowire.Dept"></bean>
```



**根据类型**：

> 只允许配置文件中只有这个类型的对象**只有一个**，否则会报错



```java
<bean id="emp" class="com.atguigu.spring5.autowire.Emp" autowire="byType"></bean> 
<bean id="dept" class="com.atguigu.spring5.autowire.Dept"></bean>
```







###### 2.3.1.1.4	外部属性文件

```java
1、直接配置数据库信息
（1）配置德鲁伊连接池
（2）引入德鲁伊连接池依赖 jar 包
    
<!--直接配置连接池--> 
 <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
 <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
 <property name="url" value="jdbc:mysql://localhost:3306/userDb"></property>
 <property name="username" value="root"></property>
 <property name="password" value="root"></property>
</bean>
     
2、引入外部属性文件配置数据库连接池
（1）创建外部属性文件，properties 格式文件，写数据库信息
（2）把外部 properties 属性文件引入到 spring 配置文件中
* 引入 context 名称空间
<beans xmlns="http://www.springframework.org/schema/beans" 
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
 xmlns:p="http://www.springframework.org/schema/p" 
 xmlns:util="http://www.springframework.org/schema/util" 
 xmlns:context="http://www.springframework.org/schema/context" 
 xsi:schemaLocation="http://www.springframework.org/schema/beans 
http://www.springframework.org/schema/beans/spring-beans.xsd 
 http://www.springframework.org/schema/util 
http://www.springframework.org/schema/util/spring-util.xsd 
 http://www.springframework.org/schema/context 
http://www.springframework.org/schema/context/spring-context.xsd"> 

⚫ 在 spring 配置文件使用标签引入外部属性文件
<!--引入外部属性文件--> 
  <context:property-placeholder location="classpath:jdbc.properties"/>
<!--配置连接池-->
 <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
 <property name="driverClassName" value="${prop.driverClass}"></property>
 <property name="url" value="${prop.url}"></property>
 <property name="username" value="${prop.userName}"></property>
 <property name="password" value="${prop.password}"></property>
</bean>
```



##### 2.3.1.2	注解

> 注解是代码特殊标记。格式：@注解名称（属性名=属性值，属性名=属性值）



###### 2.3.1.2.1	创建对象

> 下面四个注解功能都是一样的，在不同的情况下用不同的注解创建对象是便于程序员区分

1. @Component
2. @Service
3. @Controller
4. @Repository



**步骤**：



1. **导jar包**

   ![image-20220114103323889](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220114103323889.png)




2. **开启组件扫描**

   > 要使用注解你得告诉他该扫描哪个包
   >
   > 1. 如果扫描多个包，多个包使用逗号隔开
   >
   > 2. 扫描包上层目录

   ![image-20220114103601146](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220114103601146.png)




3. **在类上添加注解**

> 创建对象的这几个注解的value值就是bean标签里的id值
>
> 如果value值不写默认为类名称的首字母小写





**组件扫描的深入配置**：

> 组件扫描它的**默认filter是扫描包下全部注解**，如果不想让它扫描全部就可以通过以下配置

![image-20220114104919668](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220114104919668.png)







###### 2.3.1.2.2	属性注入



**相关注解**：

> 下面前三个针对引用类型，最后一个是针对基本类型和字符串



1. **@AutoWired**

   > 根据属性类型进行注入
   >
   > 缺点：同类型只能有一个对象，否则不知道把哪个对象注入到该属性中

   > 不需要声明set方法

   ```java
   @Service
   public class UserService {
    //定义 dao 类型属性
    //不需要添加 set 方法
    //添加注入属性注解
    @Autowired 
    private UserDao userDao;
    public void add() {
    System.out.println("service add.......");
    userDao.add();
    } 
   
   ```

   

2. **@Qualifier**

   > 根据属性名称进行注入
   >
   > 解决@AutoWired的问题，加上名字来识别。
   >
   > @Qualifier必须和@AutoWired使用

   > 不需要声明set方法

   ![image-20220114200610772](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220114200610772.png)

   

3. **@Resource**

   > 既可以根据属性类型注入也可以根据属性名称注入（默认为按类型注入）

   > 不需要声明set方法

   **默认：**

   ```java
   //根据类型注入
   @Resource
   StudentDao studentDao;
   ```

   

   **通过设置name属性来按名称注入：**

   ![image-20220114212837126](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220114212837126.png)

4. **@Value**

   > 用于基本类型/字符串注入

   > 不需要声明set方法



![image-20220114212906554](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220114212906554.png)



###### 2.3.1.2.3	纯注解开发

> 用一个配置类代替配置文件，实现纯注解开发



![image-20220114220545420](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220114220545420.png)



 









#### 2.3.2	普通bean与factoryBean

> 普通bean获得的对象与在配置文件中定义的类型一致
>
> factoryBean获得的对象可以与配置文件中定义的类型不一致



**让此类成为factoryBean：**

1. 实现factoryBean接口
2. 实现getObject方法，其返回值根据需要修改（返回值就是bean的类型）



```java
FacBean类:
public class FacBean implements FactoryBean<Course> {

    @Override
    public Course getObject() throws Exception {
        Course c = new Course();
        c.setCname("jike");
        return c;
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }

    @Override
    public boolean isSingleton() {
        return false;
    }
}

配置文件：
<bean id="fa" class="com.lts.FacBean"></bean>

```



#### 2.3.3	bean的作用域



> 在spring的bean标签里可以设置对象是单实例还是多实例



**spring默认情况对象是单例的：**

> 单例：引用的bean的id相同，那么引用的是同一对象，如果类型相同id不同，则不是同一对象
>
> 多例：引用的bean的id相同，那么引用的是不同对象

![image-20220113095518925](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220113095518925.png)





**如何设置单/多实例：**

> 通过bean标签里的scope属性，默认值为singleton（单例）。prototype（多例）

![image-20220113095711820](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220113095711820.png)



**singleton与prototype的区别：**

> singleton：加载配置文件时就创建了单例对象
>
> prototype：加载配置文件时不创建对象，在调用getbean方法时才创建多例对象





#### 2.3.4	bean的生命周期



1. 通过构造器创建bean实例
2. 为bean的属性设置值和对其他bean引用（调用set方法）
3. 调用bean的初始化方法（需要进行配置初始化的方法）
4. 使用对象（此时bean实例获取到了）
5. 容器关闭时，调用bean的销毁方法（需要进行配置销毁的方法）



```java
public class Computer {

    String name;

    public void setName(String name) {
        this.name = name;
    }

    /**
     * 初始化bean
     */
    public void init(){
        System.out.println("初始化....");
    }

    /**
     * 销毁bean时执行的方法
     */
    public void destroy(){
        System.out.println("销毁....");
    }
}


配置文件：
    
    <bean id="pc" class="com.lts.Computer" init-method="init" destroy-method="destroy">
        <property name="name">
            <value>DELL</value>
        </property>
    </bean>
        
        
 测试：
        
    @Test
    public void test4(){

        ConfigurableApplicationContext c = new ClassPathXmlApplicationContext("bean4.xml");
        Computer pc = c.getBean("pc", Computer.class);
        System.out.println("使用bean...");
        c.close();
    }

结果：
    初始化....
    使用bean...
    销毁....
```





**完整生命周期**：



> 创建后置处理器的对象后，它会对**每个创建的bean**都执行后置处理器的方法

（1）通过构造器创建 bean 实例（无参数构造）

（2）为 bean 的属性设置值和对其他 bean 引用（调用 set 方法）

**（3）把 bean 实例传递 bean 后置处理器的方法** postProcessBeforeInitialization 

（4）调用 bean 的初始化的方法（需要进行配置初始化的方法）

（5）把 **bean** **实例传递** **bean** **后置处理器的方法** postProcessAfterInitialization

（6）bean 可以使用了（对象获取到了）

（7）当容器关闭时候，调用 bean 的销毁的方法（需要进行配置销毁的方法）



![image-20220113202522033](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220113202522033.png)







## 三、AOP



> 面向切面编程，利用AOP可以**对业务逻辑的各个部分进行隔离**，从而使得业务逻辑各部分之间的耦合度降低
>
> 换句话说：不通过修改源代码方式，在主干功能里添加新功能



### 3.1	动态代理



> JDK动态代理是创建接口实现类作为代理对象
>
> CGLIB是创建子类并重写方法作为代理对象



> 代理对象调用方法时会调用invoke方法

![image-20220116094304359](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220116094304359.png)





### 3.2	AOP相关术语

![image-20220118180832791](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220118180832791.png)



### 3.3	基于AspectJ实现AOP操作



> AspectJ不是Spring组成部分，是个独立的AOP框架



**切入点表达式**：

![image-20220118194424899](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220118194424899.png)







#### 3.3.1	基于xml文件（了解）



```java
1、创建两个类，增强类和被增强类，创建方法
2、在 spring 配置文件中创建两个类对象
<!--创建对象--> 
<bean id="book" class="com.atguigu.spring5.aopxml.Book"></bean> 
<bean id="bookProxy" class="com.atguigu.spring5.aopxml.BookProxy"></bean> 
    
3、在 spring 配置文件中配置切入点
<!--配置 aop 增强--> 
 <aop:config>
     
 <!--切入点-->
 <aop:pointcut id="p" expression="execution(* com.atguigu.spring5.aopxml.Book.buy(..))"/>
     
 <!--配置切面-->
 <aop:aspect ref="bookProxy">
     
 <!--增强作用在具体的方法上-->
 <aop:before method="before" pointcut-ref="p"/>
     
 </aop:aspect>
</aop:config>
```



#### 3.3.2	基于注解



**1)进行通知配置**：



1. **开启注解扫描**

   ```java
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:context="http://www.springframework.org/schema/context" 
    xmlns:aop="http://www.springframework.org/schema/aop" 
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
   http://www.springframework.org/schema/beans/spring-beans.xsd 
    http://www.springframework.org/schema/context 
   http://www.springframework.org/schema/context/spring-context.xsd 
    http://www.springframework.org/schema/aop 
   http://www.springframework.org/schema/aop/spring-aop.xsd">
   
    <!-- 开启注解扫描 -->
    <context:component-scan basepackage="com.atguigu.spring5.aopanno"></context:component-scan>
   ```

   

2. **创建目标对象、切面类对象**

![image-20220118211533277](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220118211533277.png)



3. **在切面类上添加@Aspect注解**

   ```java
   //增强的类
   @Component
   @Aspect //告知是切面生成代理对象
   public class UserProxy {}
   ```

   

4. **开启生成代理对象**

```java
<!-- 开启 Aspect 生成代理对象-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```





**2)不同的通知使用**：

```java
（1）在增强类的里面，在作为通知方法上面添加通知类型注解，使用切入点表达式配置
    
//增强的类
@Component
@Aspect //生成代理对象
public class UserProxy {
    
     //前置通知
     //@Before 注解表示作为前置通知
     @Before(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
     public void before() {
     System.out.println("before.........");
     }
    
     //后置通知（返回通知）
     @AfterReturning(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
     public void afterReturning() {
     System.out.println("afterReturning.........");
     }
    
     //最终通知
     @After(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
     public void after() {
     System.out.println("after.........");
     }
    
     //异常通知
     @AfterThrowing(value = "execution(* 
   com.atguigu.spring5.aopanno.User.add(..))")
     public void afterThrowing() {
     System.out.println("afterThrowing.........");
     }
    
     //环绕通知
     @Around(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
     public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
     System.out.println("环绕之前.........");
     //被增强的方法执行
     proceedingJoinPoint.proceed();
     System.out.println("环绕之后.........");
 } }
```



**3)相同的切入点抽取**：

```java
//相同切入点抽取
@Pointcut(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
public void pointdemo() {
}

//前置通知
//@Before 注解表示作为前置通知
@Before(value = "pointdemo()")
public void before() {
 System.out.println("before.........");
}
```



**4)有多个增强类多同一个方法进行增强，设置增强类优先级**:

> 在增强类上面添加注解 @Order(数字类型值)，数字类型值越小优先级越高

```java
@Component
@Aspect
@Order(1)
public class PersonProxy
```



**5)完全使用注解开发** :

> **创建配置类，不需要创建 xml 配置文件**

```java
@Configuration
@ComponentScan(basePackages = {"com.atguigu"})
@EnableAspectJAutoProxy(proxyTargetClass = true)//开启生成代理对象
public class ConfigAop {
}
```





## 四、JdbcTemplate



**概念**：

> Spring 框架对 JDBC 进行封装，使用 JdbcTemplate 方便实现对数据库操作



### 4.1	配置操作步骤



**（1）在 spring 配置文件配置数据库连接池**

```java
<!-- 数据库连接池 -->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" 
 destroy-method="close">
 <property name="url" value="jdbc:mysql:///user_db" />
 <property name="username" value="root" />
 <property name="password" value="root" />
 <property name="driverClassName" value="com.mysql.jdbc.Driver" />
</bean>
```



**（2）配置 JdbcTemplate 对象，注入 DataSource**

```java
<!-- JdbcTemplate 对象 --> <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
 <!--注入 dataSource-->
 <property name="dataSource" ref="dataSource"></property>
</bean>
```



**（3）创建 service 类，创建 dao 类，在 dao 注入 jdbcTemplate 对象**

```java
配置文件
<!-- 组件扫描 --> 
   <context:component-scan base-package="com.atguigu"></context:component-scan> 
    
⚫ Service
@Service
public class BookService {
    
 //注入 dao
 @Autowired
 private BookDao bookDao; 
}

⚫ Dao
@Repository
public class BookDaoImpl implements BookDao {
    
 //注入 JdbcTemplate
 @Autowired
 private JdbcTemplate jdbcTemplate; 
}
```



### 4.2	增删改查



> 增删改都是update方法，批量增删改是batchUpdate， 查询是queryForObejct或query

![image-20220120102501699](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220120102501699.png)



#### 4.2.1	添加操作

![image-20220120102544204](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220120102544204.png)



```java
@Repository
public class BookDaoImpl implements BookDao {
    
 //注入 JdbcTemplate
 @Autowired
 private JdbcTemplate jdbcTemplate;
    
 //添加的方法
 @Override
 public void add(Book book) {
     
 //1 创建 sql 语句
 String sql = "insert into t_book values(?,?,?)";
     
 //2 调用方法实现
 Object[] args = {book.getUserId(), book.getUsername(), book.getUstatus()};
 int update = jdbcTemplate.update(sql,args);
 System.out.println(update);
 } }
```



##### 4.2.1.1	批量添加



![image-20220120103842071](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220120103842071.png)



```java
//批量添加
@Override
public void batchAddBook(List<Object[]> batchArgs) {
 String sql = "insert into t_book values(?,?,?)";
 int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
 System.out.println(Arrays.toString(ints));
}
//批量添加测试
List<Object[]> batchArgs = new ArrayList<>();
Object[] o1 = {"3","java","a"};
Object[] o2 = {"4","c++","b"};
Object[] o3 = {"5","MySQL","c"};
batchArgs.add(o1);
batchArgs.add(o2);
batchArgs.add(o3);
//调用批量添加
bookService.batchAdd(batchArgs);
```



#### 4.2.2	修改操作



```java
@Override
public void updateBook(Book book) {
 String sql = "update t_book set username=?,ustatus=? where user_id=?";
 Object[] args = {book.getUsername(), book.getUstatus(),book.getUserId()};
 int update = jdbcTemplate.update(sql, args);
 System.out.println(update);
}
```



##### 4.2.2.1	批量修改



```java
//批量修改
@Override
public void batchUpdateBook(List<Object[]> batchArgs) {
 String sql = "update t_book set username=?,ustatus=? where user_id=?";
 int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
 System.out.println(Arrays.toString(ints));
}
//批量修改
List<Object[]> batchArgs = new ArrayList<>();
Object[] o1 = {"java0909","a3","3"};
Object[] o2 = {"c++1010","b4","4"};
Object[] o3 = {"MySQL1111","c5","5"};
batchArgs.add(o1);
batchArgs.add(o2);
batchArgs.add(o3);
//调用方法实现批量修改
bookService.batchUpdate(batchArgs);
```



#### 4.2.3	删除操作

```java
@Override
public void delete(String id) {
 String sql = "delete from t_book where user_id=?";
 int update = jdbcTemplate.update(sql, id);
 System.out.println(update);
}
```



##### 4.2.3.1	批量删除

```java
//批量删除
@Override
public void batchDeleteBook(List<Object[]> batchArgs) {
 String sql = "delete from t_book where user_id=?";
 int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
 System.out.println(Arrays.toString(ints));
}
//批量删除
List<Object[]> batchArgs = new ArrayList<>();
Object[] o1 = {"3"};
Object[] o2 = {"4"};
batchArgs.add(o1);
batchArgs.add(o2);
//调用方法实现批量删除
bookService.batchDelete(batchArgs);
```



#### 4.2.4	查询操作

1. **查询返回某个值**

   ![image-20220120103225931](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220120103225931.png)

   ```java
   //查询表记录数
   @Override
   public int selectCount() {
    String sql = "select count(*) from t_book";
    Integer count = jdbcTemplate.queryForObject(sql, Integer.class);
    return count;
   }
   ```

   

2. **查询返回对象**

   ![image-20220120103320568](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220120103320568.png)

   ```java
   //查询返回对象
   @Override
   public Book findBookInfo(String id) {
    String sql = "select * from t_book where user_id=?";
    //调用方法
    Book book = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<Book>(Book.class), id);
    return book;
   }
   ```

   

3. **查询返回集合**

   ![image-20220120103401358](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220120103401358.png)

   ```java
   //查询返回集合
   @Override
   public List<Book> findAllBook() {
    String sql = "select * from t_book";
    //调用方法
    List<Book> bookList = jdbcTemplate.query(sql, new BeanPropertyRowMapper<Book>(Book.class));
    return bookList;
   }
   ```





## 五、事务



### 5.1	事务操作



> **spring事务管理有两种方式：**
>
> ​	 编程式事务管理和**声明式事务管理**，其中编程式事务管理不常用





#### 5.1.1	声明式事务管理

> 底层用的是aop



**Spring事务管理API：**

> spring提供一个接口，代表事务管理器，这个接口针对不同的框架提供不同的实现类
>
> ![image-20220121102209594](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220121102209594.png)





##### 5.1.1.1	基于xml



**在** **spring** **配置文件中进行配置**

1. 配置事务管理器

2. 配置通知

3. 配置切入点和切面

```xml
<!--1 创建事务管理器--> 
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
 <!--注入数据源-->
 <property name="dataSource" ref="dataSource"></property>
</bean>
    
<!--2 配置通知--> 
<tx:advice id="txadvice">
 <!--配置事务参数-->
 <tx:attributes>
 <!--指定哪种规则的方法上面添加事务-->
 <tx:method name="accountMoney" propagation="REQUIRED"/>
	 <!--支持account开头的方法名<tx:method name="account*"/>-->
 </tx:attributes>
</tx:advice>

<!--3 配置切入点和切面--> 
<aop:config>
 <!--配置切入点-->
 <aop:pointcut id="pt" expression="execution(* com.atguigu.spring5.service.UserService.*(..))"/>
 <!--配置切面-->
 <aop:advisor advice-ref="txadvice" pointcut-ref="pt"/>
</aop:config>
```



##### 5.1.1.2	基于注解



1. **配置事务管理器**

> datasource是数据库连接池，这里用的是druid

```java
<!--创建事务管理器--> 
 <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
 <!--注入数据源-->
 <property name="dataSource" ref="dataSource"></property>
</bean>
```



2. **开启事务注解**

   ```java
   （1）在 spring 配置文件引入名称空间 tx
   <beans xmlns="http://www.springframework.org/schema/beans" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:context="http://www.springframework.org/schema/context" 
    xmlns:aop="http://www.springframework.org/schema/aop" 
    xmlns:tx="http://www.springframework.org/schema/tx" 
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
   http://www.springframework.org/schema/beans/spring-beans.xsd 
    http://www.springframework.org/schema/context 
   http://www.springframework.org/schema/context/spring-context.xsd 
    http://www.springframework.org/schema/aop 
   http://www.springframework.org/schema/aop/spring-aop.xsd 
    http://www.springframework.org/schema/tx 
   http://www.springframework.org/schema/tx/spring-tx.xsd"> 
   
   （2）开启事务注解
   <!--开启事务注解-->
   <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
   ```

   

3. **在类/方法上添加事务注解**

   > （1）@Transactional，这个注解添加到类上面，也可以添加方法上面
   > （2）**如果把这个注解添加类上面，这个类里面所有的方法都添加事务**
   > （3）**如果把这个注解添加方法上面，为这个方法添加事务**

   ```java
   @Service
   @Transactional
   public class UserService {
   ```





###### 5.1.1.2.1	完全注解开发

```java
1、创建配置类，使用配置类替代 xml 配置文件
@Configuration //配置类
@ComponentScan(basePackages = "com.atguigu") //组件扫描
@EnableTransactionManagement //开启事务
public class TxConfig {
    
 //创建数据库连接池
 @Bean
 public DruidDataSource getDruidDataSource() {
     DruidDataSource dataSource = new DruidDataSource();
     dataSource.setDriverClassName("com.mysql.jdbc.Driver");
     dataSource.setUrl("jdbc:mysql:///user_db");
     dataSource.setUsername("root");
     dataSource.setPassword("root");
     return dataSource;
 	}
    
 //创建 JdbcTemplate 对象
 @Bean
 public JdbcTemplate getJdbcTemplate(DataSource dataSource) {
     //到 ioc 容器中根据类型找到 dataSource
     JdbcTemplate jdbcTemplate = new JdbcTemplate();
     //注入 dataSource
     jdbcTemplate.setDataSource(dataSource);
     return jdbcTemplate;
 	}
    
 //创建事务管理器
 @Bean
 public DataSourceTransactionManager {
    getDataSourceTransactionManager(DataSource dataSource) {
    DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
    transactionManager.setDataSource(dataSource);
    return transactionManager;
 	} 
 }
```



##### 5.1.1.3	@Transaction的参数



1. **传播行为**：



![image-20220121154829625](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220121154829625.png)



2. **隔离级别**：

> （1）事务有特性成为隔离性，多事务操作之间不会产生影响。不考虑隔离性产生很多问题
>
> （2）有三个读问题：脏读、不可重复读、虚（幻）读
>
> ​		**脏读：**一个未提交事务读取到另一个未提交事务的数据
>
> ![image-20220121172844785](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220121172844785.png)
>
> ​	**不可重复读**：一个未提交事务读取到另一提交事务修改数据
>
> ![image-20220121172919462](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220121172919462.png)
>
> ​	**虚读：**一个未提交事务读取到另一提交事务添加数据



> 通过设置isolation属性来给事务设置隔离级别

![image-20220121173007074](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220121173007074.png)





3. **timeout**

   > （1）事务需要在一定时间内进行提交，如果不提交进行回滚
   >
   > （2）默认值是 -1 ，设置时间以秒单位进行计算

   

4. **readOnly**

   > （1）读：查询操作，写：添加修改删除操作
   >
   > （2）readOnly 默认值 false，表示可以查询，可以添加修改删除操作
   >
   > （3）设置 readOnly 值是 true，设置成 true 之后，只能查询

   

5. **rollbackFor**

   > (1）设置出现哪些异常进行事务回滚



6. **noRollbackFor**

   > (1）设置出现哪些异常不进行事务回滚

   



## 六、Spring5框架新功能



### 6.1	整合log4j2框架

> **1、整个** **Spring5** **框架的代码基于** **Java8**，运行时兼容**JDK9**，许多不建议使用的类和方法在代码库中删除
>
> **2、Spring 5.0 框架自带了通用的日志封装** 
>
> （1）**Spring5 已经移除 Log4jConfigListener**，官方建议使用 Log4j2
>
> （2）Spring5 框架整合 Log4j2



![image-20220121231353711](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220121231353711.png)



**创建log4j2配置文件：**

> 配置文件只能叫log4j2.xml，名字是固定的

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
<!--Configuration 后面的 status 用于设置 log4j2 自身内部的信息输出，可以不设置，
当设置成 trace 时，可以看到 log4j2 内部各种详细输出--> 
<configuration status="INFO">
 <!--先定义所有的 appender-->
 <appenders>
 <!--输出日志信息到控制台-->
 <console name="Console" target="SYSTEM_OUT">
 <!--控制日志输出的格式-->
 <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
 </console>
 </appenders>
 <!--然后定义 logger，只有定义 logger 并引入的 appender，appender 才会生效-->
 <!--root：用于指定项目的根日志，如果没有单独指定 Logger，则会使用 root 作为默认的日志输出-->
 <loggers>
 <root level="info">
 <appender-ref ref="Console"/>
 </root>
 </loggers>
</configuration>
```



### 6.2	支持@Nullable注解



> @Nullable 注解可以使用在方法上面，属性上面，参数上面，表示方法返回可以为空，属性值可以
>
> 为空，参数值可以为空



![image-20220122101101166](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220122101101166.png)



![image-20220122100929145](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220122100929145.png)

![image-20220122100939005](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220122100939005.png)



### 6.3	支持函数式风格



> **Spring5** **核心容器支持函数式风格** **GenericApplicationContext**,支持lamada表达式相关的操作



```java
//函数式风格创建对象，交给 spring 进行管理
@Test
public void testGenericApplicationContext() {
 //1 创建 GenericApplicationContext 对象
 GenericApplicationContext context = new GenericApplicationContext();
 //2 调用 context 的方法对象注册
 context.refresh();
 context.registerBean("user1",User.class,() -> new User());
 //3 获取在 spring 注册的对象
 // User user = (User)context.getBean("com.atguigu.spring5.test.User");
 User user = (User)context.getBean("user1");
 System.out.println(user);
}
```



### 6.4	整合Junit5单元测试框架



![image-20220122104856821](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220122104856821.png)

![image-20220122104700557](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220122104700557.png)



### 6.5	WebFlux
