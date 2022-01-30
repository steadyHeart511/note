# mybatis



## 一、Mybatis基础





**为什么要使用MyBatis？** 

> • **MyBatis**是一个**半自动化的持久化**层框架。
>
> • **JDBC**
>
> ​	– **SQL夹在Java代码块里，耦合度高导致硬编码内伤**
>
> ​	– 维护不易且实际开发需求中sql是有变化，频繁修改的情况多见 
>
> • **Hibernate和JPA**
>
> ​	– 长难复杂SQL，对于Hibernate而言处理也不容易
>
> ​	– **内部自动生产的SQL，不容易做特殊优化**。
>
> ​	– **基于全映射的全自动框架，大量字段的POJO进行部分映射时比较困难**,导致数据库性能下降。 
>
> 
>
> • 对开发人员而言，核心sql还是需要自己优化
>
> • **mybatis使sql和java编码分开，功能边界清晰，一个专注业务、一个专注数据。**



**整体配置流程**：

1. 写全局配置文件

   > 记录一些数据源、sql映射文件位置等信息

2. 写sql映射文件

   > 在标签里编写sql语句

3. 将sql映射文件注册在全局配置文件中

4. 编写代码

   1. 根据全局配置文件获得sqlsessionFactory

   2. 使用sqlsessionFactory获得sqlsession

      > 一个sqlsession就是代表和数据库的一次会话，用完关闭

   3. 使用sql的唯一标识来告诉sqlsession执行哪个sql



**接口式编程**：

> sql映射文件通过namespace与接口实现绑定，通过id实现对接口方法的绑定。
>
> 虽然没有写实现类，但mybatis会根据对应的sql映射文件生成对应的代理对象



**接口**：

```java
public interface EmployeeDao {

    Employee selectEmployee(int id);
}
```



**sql映射文件：**



```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

	<!--namespace要与接口全路径一致-->
<mapper namespace="com.lts.mybatis.dao.EmployeeDao">
    
    <!--id要与接口中对应的方法一致-->
    <select id="selectEmployee" resultType="com.lts.mybatis.entity.Employee">
      select id,last_name lastname,gender,email from employee where id = #{id}
 </select>
</mapper>
```



**全局配置文件**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/pra"/>
                <property name="username" value="root"/>
                <property name="password" value="123"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="conf/employeeMapper.xml"/>
    </mappers>
</configuration>
```



**测试类**：

```java
@Test
    public void test1() throws IOException{

        String resource = "conf/mybatis-config.xml";
        InputStream in = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(in);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        EmployeeDao mapper = sqlSession.getMapper(EmployeeDao.class);
        System.out.println(mapper.selectEmployee(1));

    }
```





## 二、全局配置文件



### 2.1	常用标签



#### 2.1.1	properties

> **属性：**
>
> ​	resource：引入类路径下的资源
>
> ​	url：引入网络路径或磁盘路径下的资源



**properties文件：**

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/pra
username=root
password=123
```



**全局配置文件**：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    
    <!--引入外部properties文件-->
    <properties resource="jdbc.properties"></properties>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="employeeMapper.xml"/>
    </mappers>
</configuration>
```



#### 2.2.2	settings



> settings可以配置多个设置项



**map-underscore-to-camel-case：**

> **实现数据库下划线字段与实体中的驼峰属性映射**,如A/a_column---->aColumn

```xml
<settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```



**lazyLoadingEnabled**：

> 懒加载，延迟加载

```xml
<settings>
    <setting name="lazyLoadingEnabled" value="true"/>
</settings>
```



**aggressiveLazyLoading**：

> 它是控制具有懒加载特性的对象的属性的加载情况的。
> true表示如果对具有懒加载特性的对象的任意调用会导致这个对象的完整加载，false表示每种属性按照需要加载



```xml
<settings>
    <setting name="aggressiveLazyLoading" value="false"/>
</settings>
```



**cacheEnabled**：

> 开启二级缓存，默认为true



```xml
<settings>
    <setting name="cacheEnabled" value="true"/>
</settings>
```



#### 2.2.3	typeAliases



> 别名处理器，可以为我们的java类型起别名
>
> **子标签**：
>
> 1. **typeAlias：**
>
>    为某个java类型起别名
>
> > **属性**：
> >
> > ​	type:指要起别名的类型全类名，默认别名就是类名小写（别名不区分大小写）
> >
> > ​	alias：指定新的别名
>
> 2. **package:**
>
>    批量起别名操作
>
> >**属性**：
> >
> >​	name：指定包名，为当前包以及下面所有的后代包的每一个类都起一个默认别名（类名小写）
> >
> >**缺点**：
> >
> >​	子包下可能会有相同的类名，因此别名会相同，这时可以在类上加@Alias注解，为这个类指定新的别名



```xml
 <typeAliases>
     
     <!--别名不区分大小写-->
     <!--<typeAlias type="com.lts.mybatis.entity.Employee"></typeAlias>-->
     
     <!--默认别名为类名小写，employee-->
     <!--<typeAlias type="com.lts.mybatis.entity.employee"></typeAlias>-->
     
     <!--指定别名-->
     <!--<typeAlias type="com.lts.mybatis.entity.Employee" alias="emp"></typeAlias>-->
     
     <!--批量起别名-->
        <package name="com.lts.mybatis.entity"></package>
 </typeAliases>
```



#### 2.2.4	environments



> mybatis可以配置多种环境，default指定使用某种环境，可以达到快速切换的效果。



**子标签**：

​	**environment**：

> 配置一个具体的环境，其中**必须有transactionManager、dataSource**。id代表当前环境的唯一标识



```xml
<!--通过default切换环境-->
<environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
```



#### 2.2.5	databaseIdProvider

> 方便切换数据库。
>
> type="DB_VENDOR":
>
> 作用是得到数据库厂商的标识，如：MySQL，Oracle，SQL Server
>
> mysql会优先使用databaseId=“mysql”的<select>,如果没有则使用不带databaseId的<select>，oracle同理



![image-20220126154206426](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220126154206426.png)



**给sql语句绑定数据库：**

![image-20220126153711847](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220126153711847.png)



**声明mysql、oracle环境，并指明当前数据库环境：**

![image-20220126153440794](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220126153440794.png)





#### 2.2.6	mappers

> 将sql映射文件注册到全局配置文件中
>
> **属性**：
>
> 
>
> **注册配置文件**：
>
> ​	**resource**：引用类路径下的sql映射文件
>
> ​	**url**：引用网络路径或者磁盘路径下的sql映射文件
>
> 
>
> **注册接口**：
>
> ​	**class**：
>
> 1. 有sql映射文件，**映射文件名必须和接口同名**，**并且放在与接口同一目录下**
>
>    ![image-20220123225640658](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220123225640658.png)
>
>    ![image-20220123225714263](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220123225714263.png)
>
> 
>
> 2. 没有sql映射文件，**所有sql都是利用注解写在接口上**
>
>    ![image-20220123230053148](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220123230053148.png)
>
>    ![image-20220123230014519](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220123230014519.png)
>
> 
>
> **批量注册**：（规则和注册接口一致）
>
> ![image-20220123230851968](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220123230851968.png)



**小技巧**：

> **在批量注册且有sql映射文件的前提下**，sql映射文件必须与对应接口同名且在一个包里。为了好看结构清晰，这里可以取个巧，可以这么写，但最后编译完后会把它们放到一个目录下（**源码文件夹等resource会被放到类路径下**）

![image-20220123232949648](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220123232949648.png)



#### 2.2.7	注意

> 全局配置文件里的**标签要有顺序**，不然会报错。可以不写这些标签，但一定要按照以下顺序
>
> ![image-20220124092926960](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220124092926960.png)







## 三、sql映射文件



### 3.1	增删改

> 1. mybatis允许增删改直接定义以下类型返回值：
>
>    ​	Integer/int、Long/long、Boolean/boolean、void
>
> 2. 增删改中的parameterType写不写都行
>
> 3. 调用sqlsessionFactory.openSession（），事务为手动提交。



```xml
    <!--添加员工-->
    <insert id="addEmployee">
        insert into  employee values(#{id},#{lastname},#{gender},#{email})
    </insert>

    <!--修改员工-->
    <update id="updateEmployee" >
        update employee
          set last_name=#{lastname},email=#{email}
        where id=#{id}
    </update>

    <!--删除员工-->
    <delete id="deleteEmployee">
        delete from  employee where id = #{id}
    </delete>

```



**获取自增主键的值**：

> mybatis也是利用statement.getGenreatedKeys（）来获取自增主键的值，落实到属性上就是useGeneratedKeys=“true”。
>
> keyProperty：指将获取的主键值赋给哪个javaBean属性



```xml
	<!--添加员工-->
    <insert id="addEmployee" useGeneratedKeys="true" keyProperty="id">
        insert into  employee values(#{id},#{lastname},#{gender},#{email})
    </insert>
```



### 3.2	参数处理



**单个参数处理**：

> #{参数名}：取出参数值，因为就一个参数，参数名可以随便起



**多个参数处理**：

> 多个参数会被封装成一个map，
>
> **key**：param1...paramN，或者索引也可以
>
> **value**：传入的参数值
>
> 例如：#{param1}，#{param2}
>
> **缺点**：不能见名之意，全是paramN



**命名参数**：

> 明确指定封装参数时map的key：@Param（“key的名字”）
>
> #{指定的key}取出对应的参数值

```java
Employee selectEmployee(@Param("id") int id,@Param("lastname") String lastname);
```



**注意**：

> **POJO：**
>
> 1. 如果传的多个参数正好是我们业务逻辑的数据模型，那么我们可以直接传实体对象
>
>    ​	#{属性名}：取出实体属性
>
> **Map：**
>
> 2. 如果**多个参数不是业务模型中的数据，**没有对应的pojo，**不经常使用**，为了方便，我们也可以传入map
>
>    ​	#{key}：取出map中对应的值
>
> **TO:**
>
> 3. 如果**多个参数不是业务模型中的数据**，没有对应的pojo，**经常使用**，推荐编写一个TO（Transfer Object）数据传输对象，写一个类把这些参数作为属性



**总结**：

![image-20220124152136422](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220124152136422.png)



### 3.3	#{}与${}区别



> #{}:以预编译的形式将参数设置到sql语句中，可以防止sql注入
>
> ${}:取出的值直接拼接在sql语句中，会有安全问题
>
> **一般情况下，我们取参数的值都会用#{}。**
>
> **原生jdbc不支持占位符的地方我们就可以使用${}进行取值**，比如表名
>
> ![image-20220124162616717](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220124162616717.png)





### 3.4	查询



1. **当查询的返回结果是一个list集合时**

   > resultType为list集合里实体的类路径/别名

2. **当查询的返回结果是一个map集合时**

   > **返回一条记录的map：**
   >
   > ​	resultType为map；key是列名，value是对应的值
   >
   > ```java
   > 接口：
   > Map<String,Object> getEmp(int id);
   > 
   > sql映射文件：
   >     <select id="getEmp" resultType="map">
   >     	select * from employee where id = #{id}
   >     </select>
   > ```
   >
   > 
   >
   > **返回多条记录的map：**
   >
   > ​	resultType为map集合value对应的类型，并且要通过@MapKey注解在对应方法上指定map集合的key
   >
   > ```java
   > 接口：
   > 
   > 	//指定实体中哪个属性作为map集合的key
   > 	@MapKey("lastname")
   >  	Map<String,Employee> getEmp();
   > 
   >    
   > sql映射文件：
   > 
   >  <select id="getEmp" resultType="com.lts.mybatis.entity.Employee">
   >    	select * from employee
   > </select>



#### 3.4.1	resultMap



> 自定义结果集映射规则
>
> **属性**：
>
> ​	type：要自定义映射规则的java bean
>
> ​	id：唯一标识



**子标签**：

> ​	以下标签都有column、property属性。通过设置值来建立映射关系，**其他不指定的列会自动封装**，但既然写了resultMap就最好把全部的映射规则都写上，这样出错了也好找

<id>

> 定义主键列的封装规则

<result>

> 定义普通列的封装规则



```xml
	<!--自定义employee的映射规则-->
    <resultMap id="myEmp" type="com.lts.mybatis.entity.Employee">
        <id column="id" property="id"/>
        <result column="last_name" property="lastname"/>
        <result column="gender" property="gender"/>
        <result column="email" property="email"/>
    </resultMap>

    <!--查询员工-->
    <select id="selectEmployee" resultMap="myEmp">
      select id,last_name,gender,email from employee where last_name=#{lastname}
 </select>
```



##### 3.4.1.1	场景一

> 查询Employee的同时查询员工对应的部门：
>
> ​	方法一：多表查询：级联属性封装结果集
>
> ​	方法二：使用association定义单个对象的封装规则





**java bean:**

> 为了简洁set/get方法没写

```java
public class Employee {

    private int id;
    private String lastname;
    private char gender;
    private String email;
    private Department dept;

}

public class Department {

    private int id;
    private String deptName;

}


```



**方法一：级联属性封装结果集**

**sql映射文件：**

```xml
	<!--多表查询-->
    <resultMap id="myEmpAndDept" type="com.lts.mybatis.entity.Employee">
        <id column="id" property="id"/>
        <result column="last_name" property="lastname"/>
        <result column="gender" property="gender"/>
        <result column="email" property="email"/>
        
        <!--级联属性-->
        <result column="dept_id" property="dept.id"/>
        <result column="dept_name" property="dept.deptName"/>
    </resultMap>

    <!--查询员工及部门-->
    <select id="getEmpAndDept" resultMap="myEmpAndDept">
        select e.*,d.dept_name
        from  employee e
        inner join dept d
        on e.dept_id = d.id
        where e.id = #{id}
    </select>
```



**方法二：使用association**

> association可以指定联合的java bean对象
>
> property：指定哪个属性是联合的对象
>
> javaType：指定这个属性对象的类型（不能省略）

```xml
	<!--使用association-->
	<resultMap id="myEmpAndDept2" type="com.lts.mybatis.entity.Employee">
        <id column="id" property="id"/>
        <result column="last_name" property="lastname"/>
        <result column="gender" property="gender"/>
        <result column="email" property="email"/>

        <association property="dept" javaType="com.lts.mybatis.entity.Department">
            <id column="dept_id" property="id"/>
            <result column="dept_name" property="deptName"/>
        </association>
    </resultMap>

    <!--查询员工及部门-->
    <select id="getEmpAndDept" resultMap="myEmpAndDept2">
        select e.*,d.dept_name
        from  employee e
        inner join dept d
        on e.dept_id = d.id
        where e.id = #{id}
    </select>
```



> 使用association分步查询：
>
> select：表明当前属性是调用select指定的方法查出的结果
>
> column:指定将哪一列的值传给这个方法
>
> 整体流程：使用select指定的方法（传入column指定的这列参数的值）查出对象并封装给property指定的属性
>
> **分布查询支持延迟加载，即用的时候才加载：**
>
> ​	比如分布查询：查只用员工信息的话，那么就不会加载相关部门信息
>
> ![image-20220125114619312](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220125114619312.png)



```xml
EmployeeMaper.xml：

	<resultMap id="myEmpAndDept3" type="com.lts.mybatis.entity.Employee">
        <id column="id" property="id"/>
        <result column="last_name" property="lastname"/>
        <result column="gender" property="gender"/>
        <result column="email" property="email"/>
		
    	<!--使用association分步查询-->
        <association property="dept" select="com.lts.mybatis.dao.DepartmentDao.selectDepartment" column="dept_id">
        </association>
        
    </resultMap>

    <!--查询员工及部门-->
    <select id="getEmpAndDept" resultMap="myEmpAndDept3">
        select * from  employee
        where id = #{id}
    </select>

DepartmentMapper.xml：

	<select id="selectDepartment" resultType="com.lts.mybatis.entity.Department">
        select id,dept_name deptName
        from dept
        where id = #{id}
    </select>
```



##### 3.4.1.2	场景二

> 查询每个部门及相应的员工：
>
> ​	使用collection标签



**collection**：

> 定义关联集合类型的属性的封装规则
>
> ofType：指集合里面元素的类型

```xml
	<resultMap id="deptAndEmps" type="com.lts.mybatis.entity.Department">
        <id column="id" property="id"/>
        <result column="dept_name" property="deptName"/>

        <!--collection嵌套结果集的方式，定义关联的集合类型元素的封装-->
        <collection property="emps" ofType="com.lts.mybatis.entity.Employee">
            <id column="eid" property="id"/>
            <result column="last_name" property="lastname"/>
            <result column="gender" property="gender"/>
            <result column="email" property="email"/>
        </collection>
    </resultMap>

    <select id="getDeptAndEmps" resultMap="deptAndEmps">
        select d.*,e.id eid,e.last_name,e.gender,e.email
        from  employee e
        inner join dept d
        on e.dept_id = d.id
        where d.id = #{id}
    </select>
```



**collection也支持分步查询:**

> 也支持延迟加载
>
> select：表明当前属性是调用select指定的方法查出的结果
>
> column:指定将哪一列的值传给这个方法



```xml
departmentMapper.xml：

	<!--collection也支持分步查询-->
    <resultMap id="findByStep" type="com.lts.mybatis.entity.Department">
        <id column="id" property="id"/>
        <result column="dept_name" property="deptName"/>
        <collection property="emps" select="com.lts.mybatis.dao.EmployeeDaoPlus.getEmpById" column="id"/>
    </resultMap>
    
    <select id="getDeptStep" resultMap="findByStep">
      select * from dept
      where id=#{id}
    </select>

EmployeeMapper.xml:

	<select id="getEmpById" resultType="com.lts.mybatis.entity.Employee">
        select * from employee
        where dept_id = #{dept_id}
    </select>
```



##### 3.4.1.3	拓展

> association和collection拓展：
>
> **分布查询时传递的是多列的值：**
>
> ​	可以将多列的值封装map传递：column=“{key1=column1,key2=column2}”，引用时直接#{key}
>
> ![image-20220125162206526](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220125162206526.png)
>
> **fetchType有两种取值：**
>
> ​	-	lazy：延迟加载
>
> ​	-	eager：立即加载



##### 3.4.1.4	鉴别器



**discriminator**:

> 鉴别器：mybatis可以使用discriminator判断某列的值，然后根据某列的值改变封装行为
>
> **属性**：
>
> ​			column:指定判断的列名
> ​            javaType：列值对应的java类型



**例如**：

> 封装Employee：
>
> ​	如果查出来的是女生，就把部门信息查询出来，否则不查询
>
> ​	如果查出来的是男生，把last_name这一列的值赋值给email



```xml
<!--查询员工及部门-->
    <select id="getEmpAndDept" resultMap="myEmp4">
        select * from  employee
        where id = #{id}
    </select>


<resultMap id="myEmp4" type="com.lts.mybatis.entity.Employee">
        <id column="id" property="id"/>
        <result column="last_name" property="lastname"/>
        <result column="gender" property="gender"/>
        <result column="email" property="email"/>

        <!--
            设置鉴别器
            column:指定判断的列名
            javaType：列值对应的java类型
            -->
        <discriminator javaType="java.lang.Character" column="gender">

            <!--case中resultType/resultMap不能缺少-->
            <case value='女' resultType="com.lts.mybatis.entity.Employee">

                <!--如果是女生才查出部门信息-->
                <association property="dept" javaType="com.lts.mybatis.entity.Department">
                    <id column="dept_id" property="id"/>
                    <result column="dept_name" property="deptName"/>
                </association>
            </case>

            <!--case中resultType/resultMap不能缺少-->
            <case value='男' resultType="com.lts.mybatis.entity.Employee">
                <id column="id" property="id"/>
                <result column="last_name" property="lastname"/>
                <result column="gender" property="gender"/>
                <result column="last_name" property="email"/>
            </case>
        </discriminator>
    </resultMap>
```









## 四、动态sql



> 动态sql就是在普通sql基础上，通过标签去进行拼接。以达到把普通sql拼接成新的sql的效果



**OGNL**：

### 4.1	if

> <if test="判断表达式">（OGNL）
>
> 遇见特殊符号要写转义字符



```xml
	<!--携带了哪个字段查询条件就带上这个字段的值-->
    <select id="getEmpsByCondition" resultType="com.lts.mybatis.entity.Employee">

        select * from employee
        where
        <if test="id != null">
          id = #{id}
        </if>

        <!--遇见特殊字符应该写转义-->
        <!--特殊字符：&amp;(&) &quot;(")-->
        <if test="lastname != null &amp;&amp; lastname != &quot;&quot;">
            and last_name like #{lastname}
        </if>
        <if test="email != null and email.trim() !=''">
          and email = #{email}
        </if>

        <!-- OGNL会进行字符串与数字的转换判断 "0" == 0是ok的-->
        <if test="gender == 0 or gender == 1">
          and gender = #{gender}
        </if>
    </select>
```



### 4.2	where

> 查询的时候如果某些条件没带可能sql拼装会有问题
>
> 解决方法：
>
> 1. where后面加1=1，以后的条件都and xxx
> 2. 可以使用where标签将所有的查询条件包括在内。mybatis就会将where标签中拼装的sql多出来的and或者or去掉（**and放在条件的后面无效**）



```xml
<!--携带了哪个字段查询条件就带上这个字段的值-->
    <select id="getEmpsByCondition" resultType="com.lts.mybatis.entity.Employee">

        select * from employee
        
    <!--使用where标签-->
        <where>
            <if test="id != null">
              and id = #{id}
            </if>
    
          <!--遇见特殊字符应该写转义-->
          <!--特殊字符：&amp;(&) &quot;(")-->
            <if test="lastname != null &amp;&amp; lastname != &quot;&quot;">
                and last_name like #{lastname}
            </if>
            <if test="email != null and email.trim() !=' '">
              and email = #{email}
            </if>
    
            <!-- OGNL会进行字符串与数字的转换判断 "0" == 0是ok的-->
            <if test="gender == 0 or gender == 1">
              and gender = #{gender}
            </if>
        </where>
    </select>
```



### 4.3	trim



> 可以解决where标签的缺点。
>
> prefix:给拼串后的整个字符串加一个前缀
>
> prefixOverrides：去掉整个字符串前面多余的字符
>
> suffix：给拼串后的整个字符串加一个后缀
>
> suffixOverrides：去掉整个字符串后面多余的字符



```xml
	<!--携带了哪个字段查询条件就带上这个字段的值-->
    <select id="getEmpsByCondition" resultType="com.lts.mybatis.entity.Employee">

        select * from employee

    <!--使用trim标签-->
        <trim prefix="where" prefixOverrides="and">
            <if test="id != null">
              and id = #{id}
            </if>

          <!--遇见特殊字符应该写转义-->
          <!--特殊字符：&amp;(&) &quot;(")-->
            <if test="lastname != null &amp;&amp; lastname != &quot;&quot;">
                and last_name like #{lastname}
            </if>
            <if test="email != null and email.trim() !=' '">
              and email = #{email}
            </if>

            <!-- OGNL会进行字符串与数字的转换判断 "0" == 0是ok的-->
            <if test="gender == 0 or gender == 1">
              and gender = #{gender}
            </if>
        </trim>
    </select>
```



### 4.4	choose

> 分支语句，相当于带了mysql的case”表达式"when...then



```xml
<!--携带了哪个字段查询条件就带上这个字段的值-->
    <select id="getEmpsByCondition" resultType="com.lts.mybatis.entity.Employee">

        select * from employee
        <where>
    <!--使用choose标签-->
        <choose>
          <when test="id != null">
              and id = #{id}
          </when>

          <!--遇见特殊字符应该写转义-->
          <!--特殊字符：&amp;(&) &quot;(")-->
            <when test="lastname != null &amp;&amp; lastname != &quot;&quot;">
                and last_name like #{lastname}
            </when>
            <when test="email != null and email.trim() !=' '">
              and email = #{email}
            </when>

            <!-- OGNL会进行字符串与数字的转换判断 "0" == 0是ok的-->
            <when test="gender == 0 or gender == 1">
              and gender = #{gender}
            </when>

            <otherwise>
                1=1
            </otherwise>
        </choose>
        </where>
    </select>
```



### 4.5	set

> 针对于update语句

```xml
<!--哪个字段有值就更新员哪个工信息-->
    <update id="updateEmp">
        update employee
        <set>
            <if test="lastname!=null">
                last_name = #{lastname},
            </if>
            <if test="email != null">
                email = #{email},
            </if>
            <if test="gender != ' '">
                gender=#{gender}
            </if>
        </set>
        <where>
            id=#{id}
        </where>
    </update>
```



**trim代替set：**

```xml
<!--哪个字段有值就更新员哪个工信息-->
    <update id="updateEmp">
        update employee
        <trim prefix="set" suffixOverrides=",">
            <if test="lastname!=null">
                last_name = #{lastname},
            </if>
            <if test="email != null">
                email = #{email},
            </if>
            <if test="gender != ' '">
                gender=#{gender}
            </if>
        </trim>
        <where>
            id=#{id}
        </where>
    </update>
```





### 4.6	foreach

> collection:方法参数的类型，数组用array，list集合用list（参数为list/数组会封装在map中，因此key是list/array）
>
> item：将当前遍历出的元素赋值给指定的变量
>
> separator：每个元素的分隔符
>
> open：遍历出所有结果拼接一个开始的字符
>
> close：遍历出所有结果拼接一个结束的字符
>
> index：
>
> > 遍历list的时候，index就是索引，item就是当前值
> >
> > 遍历map的时候index表示的就是map的key，item就是map的值
>
> #{变量名}就能取出变量的值也就是当前遍历出的元素



```xml
接口：
List<Employee> getEmpsByConditionForeach(List<Integer> ids);
    
xml：

	<select id="getEmpsByConditionForeach" resultType="com.lts.mybatis.entity.Employee">
        select * from employee
        where id in
        <foreach collection="list" item="item_id" separator="," open="(" close=")">
            #{item_id}
        </foreach>
    </select>
```



**批量插入**：



**方法一**：

```xml
接口：

	//批量插入员工
    void insertEmps(@Param("emps") List<Employee> employees);
        
xml：
        <!--批量插入-->
    <insert id="insertEmps">

      insert into employee values
      <!--给List<Employee>employees@Param为emps-->
      <foreach collection="emps"  item="emp" separator=",">
          (#{emp.id},#{emp.lastname},#{emp.gender},#{emp.email},#{emp.dept.id})
      </foreach>

    </insert>
```



**方法二**：

> 这种操作可以用于其它批量操作（删除，修改）

**需要设置allowMultiQueries=true**

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/pra?allowMultiQueries=true
username=root
password=123
```



```xml
<!--批量插入-->
    <insert id="insertEmps">

        <!--给List<Employee>employees@Param为emps-->
        <foreach collection="emps"  item="emp" separator=";">
           insert into employee values
          (#{emp.id},#{emp.lastname},#{emp.gender},#{emp.email},#{emp.dept.id})
        </foreach>

    </insert>
```



### 4.7	两个内置参数



> 内置参数也可以用在判断里
>
> mybatis的默认两个内置参数：
>
> 1. **_parameter:代表整个参数**
>
> ​		单个参数：_parameter就是这个参数
>
> ​		多个参数：参数会被封装为一个map，_parameter就是代表这个map
>
> ![image-20220126152302865](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220126152302865.png)
>
> 判断employee是否为空：
>
> ![image-20220126152224852](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220126152224852.png)
>
> 
>
> 2. **_databaseId:如果配置了databaseIdProvider标签**
>
>    ​	_databaseId就是代表当前数据库的别名
>
>    ![image-20220126152127736](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220126152127736.png)



### 4.8	bind

> 可以将OGNL表达式的值绑定到一个变量中，方便后来引用这个变量的值

![image-20220126160041383](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220126160041383.png)



### 4.9	sql

> sql标签：抽取可重用的sql片段，方便后面引用
>
> include标签用来引用外部定义的sql



![image-20220126161308128](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220126161308128.png)



**引用：**

![image-20220126161356873](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220126161356873.png)









## 五、缓存



> mybatis有一级缓存、二级缓存



### 5.1	一级缓存



> ​	又称本地缓存，sqlsession级别的缓存，一级缓存是一直开启的。一级缓存是map
>
> ​	与数据库同一期会话期间查询到的数据会放在本地缓存中。以后如果需要获取相同的数据，直接从缓存中拿，没必要再去查询数据库



**一级缓存失效的四种情况**：



> 一级缓存失效情况（没有使用到当前一级缓存的情况，效果就是还需要再向数据库发出查询）



1. sqlsession不同
2. sqlsession相同，传递给查询的参数不同
3. sqlsession相同，两次查询之间执行了增删改操作（这次增删改可能对当前数据有影响）
4. sqlsession相同，手动清除缓存



### 5.2	二级缓存



> 又称全局缓存，基于namespace级别的缓存（一个namespace对应一个二级缓存）



​	**工作机制**：

1. 一个会话，查询一条数据，这个数据会被放在当前会话的一级缓存中
2. 如果会话关闭，一级缓存中的数据会被保存到二级缓存中，这时新的会话查询信息时就可以参照二级缓存中的内容
3. 不同namespace查询出的数据会放在自己对应的缓存中（map）



**总结**：

> 查出的数据都会被默认先放在一级缓存中，只有会话提交或者关闭以后，一级缓存中的数据才会转移到二级缓存中



**使用**：

1. 开启二级缓存（默认开启）

   ![image-20220127091836794](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220127091836794.png)

   

2. 去xxxmapper.xml中配置使用二级缓存

   > cache标签中不设置属性，属性就用默认值

   ```xml
   <cache eviction="FIFO" flushInterval="60000" readOnly="false" size="1024"></cache>
   	
   	eviction:缓存的回收策略：
   		• LRU – 最近最少使用的：移除最长时间不被使用的对象。
   		• FIFO – 先进先出：按对象进入缓存的顺序来移除它们。
   		• SOFT – 软引用：移除基于垃圾回收器状态和软引用规则的对象。
   		• WEAK – 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。
   		• 默认的是 LRU。
   
   	flushInterval：缓存刷新间隔
   		缓存多长时间清空一次，默认不清空，设置一个毫秒值
   
   	readOnly:是否只读：
   		true：只读；mybatis认为所有从缓存中获取数据的操作都是只读操作，不会修改数据。
   				 mybatis为了加快获取速度，直接就会将数据在缓存中的引用交给用户。不安全，速度快
   		false：非只读：mybatis觉得获取的数据可能会被修改。
   				mybatis会利用序列化&反序列的技术克隆一份新的数据给你。安全，速度慢
   
   	size：缓存存放多少元素；
   
   	type=""：指定自定义缓存的全类名；
   			实现Cache接口即可；
   ```

3. 实体类需要实现序列化接口





### 5.3	和缓存有关的设置/属性



1. cacheEnabled=true

   > 默认为true，false只会关闭二级缓存，一级缓存一直开启

2. select标签中有useCache=“true”

   > 默认为true，false：一级缓存依然使用，二级缓存不使用

3. 每个增删改标签有flushCache=“true”

   > 默认为true，增删改执行完成后就会清除缓存
   >
   > 一二级缓存都会被清空
   >
   > 查询标签也有flushCache但默认值为true
   >
   
4. sqlsession.clearCache()

   > 只是清除当前session的一级缓存

5. localCacheScope

   > 本地缓存作用域，默认为session，当前会话所有数据保存在一级缓存中
   >
   > statement：禁用一级缓存



### 5.4	缓存图解

![image-20220127111455165](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220127111455165.png)



![image-20220127113158783](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220127113158783.png)







### 5.5	mybatis整合第三方缓存

> 这里拿EhCache举例，其他的同理



![image-20220127113008001](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220127113008001.png)









## 六、mybatis逆向工程



**mbg.xml**:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>

	<!-- 
		targetRuntime="MyBatis3Simple":生成简单版的CRUD
		MyBatis3:豪华版
	
	 -->
  <context id="DB2Tables" targetRuntime="MyBatis3">
  	<!-- jdbcConnection：指定如何连接到目标数据库 -->
    <jdbcConnection driverClass="com.mysql.jdbc.Driver"
        connectionURL="jdbc:mysql://localhost:3306/mybatis?allowMultiQueries=true"
        userId="root"
        password="123456">
    </jdbcConnection>

	<!--  -->
    <javaTypeResolver >
      <property name="forceBigDecimals" value="false" />
    </javaTypeResolver>

	<!-- javaModelGenerator：指定javaBean的生成策略 
	targetPackage="test.model"：目标包名
	targetProject="\MBGTestProject\src"：目标工程
	-->
    <javaModelGenerator targetPackage="com.atguigu.mybatis.bean" 
    		targetProject=".\src">
      <property name="enableSubPackages" value="true" />
      <property name="trimStrings" value="true" />
    </javaModelGenerator>

	<!-- sqlMapGenerator：sql映射生成策略： -->
    <sqlMapGenerator targetPackage="com.atguigu.mybatis.dao"  
    	targetProject=".\conf">
      <property name="enableSubPackages" value="true" />
    </sqlMapGenerator>

	<!-- javaClientGenerator:指定mapper接口所在的位置 -->
    <javaClientGenerator type="XMLMAPPER" targetPackage="com.atguigu.mybatis.dao"  
    	targetProject=".\src">
      <property name="enableSubPackages" value="true" />
    </javaClientGenerator>

	<!-- 指定要逆向分析哪些表：根据表要创建javaBean -->
    <table tableName="tbl_dept" domainObjectName="Department"></table>
    <table tableName="tbl_employee" domainObjectName="Employee"></table>
  </context>
</generatorConfiguration>

```



**运行mybatisGenerator：**

```java
@Test
	public void testMbg() throws Exception {
		List<String> warnings = new ArrayList<String>();
		boolean overwrite = true;
		File configFile = new File("mbg.xml");
		ConfigurationParser cp = new ConfigurationParser(warnings);
		Configuration config = cp.parseConfiguration(configFile);
		DefaultShellCallback callback = new DefaultShellCallback(overwrite);
		MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config,callback, warnings);
		myBatisGenerator.generate(null);
	}
```







## 七、运行原理



**总体流程**：

> 1. 获取sqlsessionFactory对象
>
>    ​	解析文件的每一个信息保存在configuration中，返回包含configuration的defaultsqlsesionFactory。
>
>    ​	注意：mappedStatement：代表一个增删改查的详细信息
>
> 2. 获取sqlsession对象
>
>    ​	返回一个包含executor和configuration的defaultSqlsession对象。
>
>    ​	这一步会创建executor对象
>
> 3. 获取接口的代理对象（MapperProxy）
>
>    代理对象有defaultsqlsession（executor）
>
> 4. 执行增删改查方法



### 7.1	创建sqlsessionFactory的流程



![image-20220127215415956](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220127215415956.png)





**重要属性**：



![image-20220127215943194](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220127215943194.png)



![image-20220127220013126](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220127220013126.png)





### 7.2	创建sqlsession的流程



![image-20220128100458115](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220128100458115.png)





### 7.3	获取接口的代理对象

> 创建MapperProxy的代理对象指的是MapperProxy.mapperInterface的代理对象

![image-20220128104859478](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220128104859478.png)



### 7.4	查询流程

![image-20220128155210707](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220128155210707.png)



**查询流程总结**：

![image-20220128155503297](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220128155503297.png)





### 7.5	整体总结

> 总结：
>
> 1. 根据配置文件（全局，sql映射）初始化出Configuration对象
>
> 2. 创建一个DefaultSqlSession对象:
>
>    ​	他里面包含Configuration以及Executor（根据全局配置文件中的defaultExecutorType创建出对应的Executor）
>
> 3. DefaultSqlSession.getMapper（）：拿到Mapper接口对应的MapperProxy；
>
> 4. MapperProxy里面有（DefaultSqlSession）；
>
> 5. 执行增删改查方法：
>    	1）、调用DefaultSqlSession的增删改查（Executor）；
>    	2）、会创建一个StatementHandler对象。（同时也会创建出ParameterHandler和ResultSetHandler）
>    	3）、调用StatementHandler预编译参数以及设置参数值; 使用ParameterHandler来给sql设置参数
>        4）、调用StatementHandler的增删改查方法；
>       5）、ResultSetHandler封装结果
>    
>      注意：四大对象每个创建的时候都有一个interceptorChain.pluginAll(parameterHandler);
>







## 八、插件开发



**插件原理**：

![image-20220128222916557](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220128222916557.png)



### 8.1	插件编写的步骤



> 在四大对象目标方法执行之前进行拦截，修改一些参数，再执行目标方法，这就会达到介入mybatis内部的效果



1. 编写Interceptor的实现类

2. 使用@Interceptor注解完成插件签名

   > 指明拦截哪个对象哪个方法

3. 将写好的插件注册到全局配置文件中

> ​	四大对象创建时都会调用插件的plugin方法，plugin再调用wrap方法。如果是在插件签名中声明了的对象则会被生成代理对象，否则不会生成代理对象，调用方法时，如果是插件签名中声明的方法则调用插件的interceptor方法，否则调用目标对象自己的方法



```java
/**
 * 完成插件签名：
 *          告诉mybatis当前插件用来拦截哪个对象的哪个方法
 */

@Intercepts(
        {
                @Signature(type = StatementHandler.class,method = "parameterize",args = Statement.class)
        }
)
public class MyFirstPlugin implements Interceptor {

    /**
     * 拦截目标对象的目标方法的执行
     * @param invocation
     * @return
     * @throws Throwable
     */
    @Override
    public Object intercept(Invocation invocation) throws Throwable {

        System.out.println("MyFirstPlugin intercept:"+invocation.getMethod());

//        放行，执行目标对象的方法
        Object proceed = invocation.proceed();
        return proceed;
    }


    /**
     * 为目标对象创建一个代理对象
     * @param target
     * @return
     */
    @Override
    public Object plugin(Object target) {

        System.out.println("plugin:"+target);
//        借助Plugin的wrap方法来使用当前Interceptor包装目标对象
        Object wrap = Plugin.wrap(target, this);

//        返回代理对象
        return wrap;
    }

    /**
     * 将插件注册时的property属性设置进来
     * @param properties
     */
    @Override
    public void setProperties(Properties properties) {
        System.out.println("插件的配置信息："+properties);
    }
```



**注册**：

```xml
<plugins>
        <!--注册插件-->
        <plugin interceptor="com.lts.mybatis.dao.MyFirstPlugin">

            <!--注册插件时设置的信息，可以在插件的setProperties获取-->
            <property name="username" value="root"/>
            <property name="password" value="123"/>
        </plugin>
    </plugins>
```



**结果**：

![image-20220129110511162](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220129110511162.png)



**配置多个插件**：

> 如果配置的多个插件，插件签名是一样的，则会产生多层代理。
>
> 进行动态代理的时候是按照插件配置顺序创建层层代理对象。执行方法是按逆向顺序执行

![image-20220129110639978](https://gitee.com/steadyHeart/drawingBed/raw/master/photos/image-20220129110639978.png)









### 8.2	编写简单插件



```java
package com.atguigu.mybatis.dao;

import java.util.Properties;

import org.apache.ibatis.executor.parameter.ParameterHandler;
import org.apache.ibatis.executor.statement.StatementHandler;
import org.apache.ibatis.plugin.Interceptor;
import org.apache.ibatis.plugin.Intercepts;
import org.apache.ibatis.plugin.Invocation;
import org.apache.ibatis.plugin.Plugin;
import org.apache.ibatis.plugin.Signature;
import org.apache.ibatis.reflection.MetaObject;
import org.apache.ibatis.reflection.SystemMetaObject;

/**
 * 完成插件签名：
 *		告诉MyBatis当前插件用来拦截哪个对象的哪个方法
 */
@Intercepts(
		{
			@Signature(type=StatementHandler.class,method="parameterize",args=java.sql.Statement.class)
		})
public class MyFirstPlugin implements Interceptor{

	/**
	 * intercept：拦截：
	 * 		拦截目标对象的目标方法的执行；
	 */
	@Override
	public Object intercept(Invocation invocation) throws Throwable {
		
		//动态的改变一下sql运行的参数：以前1号员工，实际从数据库查询3号员工
		Object target = invocation.getTarget();
		System.out.println("当前拦截到的对象："+target);
		//拿到：StatementHandler==>ParameterHandler===>parameterObject
		//拿到target的元数据
		MetaObject metaObject = SystemMetaObject.forObject(target);
		//修改完sql语句要用的参数
		metaObject.setValue("parameterHandler.parameterObject", 3);
		//执行目标方法
		Object proceed = invocation.proceed();
		//返回执行后的返回值
		return proceed;
	}

	/**
	 * plugin：
	 * 		包装目标对象的：包装：为目标对象创建一个代理对象
	 */
	@Override
	public Object plugin(Object target) {
		// TODO Auto-generated method stub
		//我们可以借助Plugin的wrap方法来使用当前Interceptor包装我们目标对象
		System.out.println("MyFirstPlugin...plugin:mybatis将要包装的对象"+target);
		Object wrap = Plugin.wrap(target, this);
		//返回为当前target创建的动态代理
		return wrap;
	}

	/**
	 * setProperties：
	 * 		将插件注册时 的property属性设置进来
	 */
	@Override
	public void setProperties(Properties properties) {
		// TODO Auto-generated method stub
		System.out.println("插件配置的信息："+properties);
	}

}
```









## 九、拓展



### 9.1	PageHelper插件的使用



[PageHelper的说明文档](https://pagehelper.github.io/docs/howtouse/)



```java
	@Test
	public void test01() throws IOException {
		// 1、获取sqlSessionFactory对象
		SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
		// 2、获取sqlSession对象
		SqlSession openSession = sqlSessionFactory.openSession();
		try {
			EmployeeMapper mapper = openSession.getMapper(EmployeeMapper.class);
			Page<Object> page = PageHelper.startPage(5, 1);
			
			List<Employee> emps = mapper.getEmps();
			//传入要连续显示多少页
			PageInfo<Employee> info = new PageInfo<>(emps, 5);
			for (Employee employee : emps) {
				System.out.println(employee);
			}
			/*System.out.println("当前页码："+page.getPageNum());
			System.out.println("总记录数："+page.getTotal());
			System.out.println("每页的记录数："+page.getPageSize());
			System.out.println("总页码："+page.getPages());*/
			///xxx
			System.out.println("当前页码："+info.getPageNum());
			System.out.println("总记录数："+info.getTotal());
			System.out.println("每页的记录数："+info.getPageSize());
			System.out.println("总页码："+info.getPages());
			System.out.println("是否第一页："+info.isIsFirstPage());
			System.out.println("连续显示的页码：");
			int[] nums = info.getNavigatepageNums();
			for (int i = 0; i < nums.length; i++) {
				System.out.println(nums[i]);
			}
		} finally {
			openSession.close();
		}

	}
```



### 9.2	批量操作



> 设置executor为batchExecutor



```java
	@Test
	public void testBatch() throws IOException{
		SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
		
		//可以执行批量操作的sqlSession
		SqlSession openSession = sqlSessionFactory.openSession(ExecutorType.BATCH);
		long start = System.currentTimeMillis();
		try{
			EmployeeMapper mapper = openSession.getMapper(EmployeeMapper.class);
			for (int i = 0; i < 10000; i++) {
				mapper.addEmp(new Employee(UUID.randomUUID().toString().substring(0, 5), "b", "1"));
			}
			openSession.commit();
			long end = System.currentTimeMillis();
			//批量：（预编译sql一次==>设置参数（10000次）===>执行（1次））
			//Parameters: 616c1(String), b(String), 1(String)==>4598
			//非批量：（预编译sql=设置参数=执行）==》10000    10200
			System.out.println("执行时长："+(end-start));
		}finally{
			openSession.close();
		}
		
	}
```





### 9.3	自定义类型处理器



1. 实现TypeHandler接口。或者继承BaseTypeHandler
2. 实现方法
3. 在全局配置文件中声明自定义类型处理器



**举例：做一个自定义枚举的类型处理器**



**相关方法**：

> 这俩个方法都涉及了枚举类型

```xml
	<!--添加方法-->
	<!--public Long addEmp(Employee employee);  -->
	<insert id="addEmp" useGeneratedKeys="true" keyProperty="id">
		insert into tbl_employee(last_name,email,gender,empStatus) 
		values(#{lastName},#{email},#{gender},#{empStatus})
	</insert>

	<!--查询方法-->
	<!--public Employee getEmpById(Integer id);-->
	<select id="getEmpById" resultType="com.atguigu.mybatis.bean.Employee">
		select id,last_name lastName,email,gender,empStatus from tbl_employee where id = #{id}
	</select>
```



**枚举类型处理器**：

```java
/**
 * 1、实现TypeHandler接口。或者继承BaseTypeHandler
 * @author lfy
 *
 */
public class MyEnumEmpStatusTypeHandler implements TypeHandler<EmpStatus> {

	/**
	 * 定义当前数据如何保存到数据库中
	 */
	@Override
	public void setParameter(PreparedStatement ps, int i, EmpStatus parameter,
			JdbcType jdbcType) throws SQLException {
		// TODO Auto-generated method stub
		System.out.println("要保存的状态码："+parameter.getCode());
		ps.setString(i, parameter.getCode().toString());
	}

	@Override
	public EmpStatus getResult(ResultSet rs, String columnName)
			throws SQLException {
		// TODO Auto-generated method stub
		//需要根据从数据库中拿到的枚举的状态码返回一个枚举对象
		int code = rs.getInt(columnName);
		System.out.println("从数据库中获取的状态码："+code);
		EmpStatus status = EmpStatus.getEmpStatusByCode(code);
		return status;
	}

	@Override
	public EmpStatus getResult(ResultSet rs, int columnIndex)
			throws SQLException {
		// TODO Auto-generated method stub
		int code = rs.getInt(columnIndex);
		System.out.println("从数据库中获取的状态码："+code);
		EmpStatus status = EmpStatus.getEmpStatusByCode(code);
		return status;
	}

	@Override
	public EmpStatus getResult(CallableStatement cs, int columnIndex)
			throws SQLException {
		// TODO Auto-generated method stub
		int code = cs.getInt(columnIndex);
		System.out.println("从数据库中获取的状态码："+code);
		EmpStatus status = EmpStatus.getEmpStatusByCode(code);
		return status;
	}

}
```



**配置**：

```xml
<typeHandlers>
		<!--1、配置我们自定义的TypeHandler  -->
		<typeHandler handler="com.atguigu.mybatis.typehandler.MyEnumEmpStatusTypeHandler" javaType="com.atguigu.mybatis.bean.EmpStatus"/>
		<!--2、也可以在处理某个字段的时候告诉MyBatis用什么类型处理器（sql映射文件中）
				保存：#{empStatus,typeHandler=xxxx}
				查询：
					<resultMap type="com.atguigu.mybatis.bean.Employee" id="MyEmp">
				 		<id column="id" property="id"/>
				 		<result column="empStatus" property="empStatus" typeHandler=""/>
				 	</resultMap>
				注意：如果在参数位置修改TypeHandler，应该保证保存数据和查询数据用的TypeHandler是一样的。
		  -->
	</typeHandlers>
```

