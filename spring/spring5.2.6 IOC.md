# spring5.2.6 IOC

## IOC

> IOC底层原理（简化代码，降低耦合）
> 
> 通过xml解析，工厂模式，反射
> 
> IOC解耦过程
> 
> 第一步 xml配置文件，配置创建的对象
> 
> ```xml
>     <bean id="user" class="com.atguigu.spring5.User">
> 
>     </bean>
> ```
> 
> 第二部 有service类和dao类 ，创建工厂类
> 
> ```java
> class UserFactory{
>     public static UserDao getDao(){
>     String classValue = class属性值; //1。xml解析
>     //通过反射创建对象
>     Class clazz = Class.forName(classValue); //2通过反射创建对象
>     return (UserDao)clazz.newInstance;
>     }
> }
> ```

##### IOC（接口）

> **1、IOC思想基于IOC容器完成，IOC容器底层就是对象工厂**
> 
> **2、spring提供IOC容器实现两种方式：（两个接口）**
> 
> （1）BeanFactory：IOC容器基本实现，是spring内部的使用接口，不提供开发人员使用
> 
> 加载配置文件的时候不会创建对象，在获取对象或者使用对象的时候才创建对象
> 
> （2）ApplicationContext：BeanFactory接口的子接口，提供更多更强大的功能，一般由开发人员进行使用
> 
> 加载配置文件时候就会把在配置文件对象进行创建
> 
> **3.Application接口有实现类**
> 
> ![](D:\typora\images\2022-11-02-14-12-49-image.png)

##### IOC操作Bean管理（概念）

> ##### 1.什么是Bean管理
>
> （1）Bean管理指的是两个操作
>
> （2）Spring创建对象
>
> （3）Spring注入属性
>
> ##### 2.Bean管理操作有两种方式
>
> （1）基于xml配置文件方式实现
>
> （2）基于注解方式实现
>
> IOC操作Bean管理（基于xml方式）
>
> **1、基于xml方式创建对象**
>
> ```xml
> <bean id="user" class="com.atguigu.spring5.User"></bean>
> ```
>
> id: 给对象起一个别名
>
> class: 类全路径（包路径）
>
> name：别名（基本不用）
>
> 创建对象的时候，默认执行无参构造方法完成对象创建
>
> ##### 3、基于xml方式注入属性
>
> （1）DI：依赖注入，就是注入属性
>
> （2）在spring配置文件配置对象创建，配置属性注入
>
> > 第一种注入方式：使用set方式进行注入
> >
> > ```xml
> >  <bean id="book"  class="com.atguigu.spring5.Book" >
> >      <property name="name" value="123"></property>
> >  </bean>
> > ```
> >
> > 第二种注入方式：使用有参有参构造进行注入
> >
> > ```xml
> >  <bean id="orders" class="com.atguigu.spring5.Orders">
> >      <constructor-arg name="oname" value="123"></constructor-arg>
> >      <constructor-arg name="address" value="456"></constructor-arg>
> >  </bean>
> > ```
> >
> > 第三种注入方式：P命名空间注入
> >
> > 添加一个P引入（配置文件中）
> >
> > ```xml
> > <!--1、添加p名称空间在配置文件头部-->
> > xmlns:p="http://www.springframework.org/schema/p"
> > 
> > <!-- 原理是通过set注入 -->
> > <bean id="orders" class="com.atguigu.spring5.Orders" p:oname="111" p:address="222"></bean> 
> > ```
>
> ##### 4.xml注入其他类型属性 null 和 特殊符号
>
> ```xml
>     <bean id="orders" class="com.atguigu.spring5.Orders">
>         <constructor-arg name="oname" value="123"></constructor-arg>
>         <constructor-arg name="address">
> <!--            <null></null>-->
>             <value><![CDATA[<<南京>>]]]></value>
>         </constructor-arg>
>     </bean>
> ```
>
> ##### 5.注入属性-外部bean
>
> ```xml
> <!--1 service和dao对象创建-->
> <bean id="userService" class="com.atguigu.spring5.service.UserService">
>     <!--注入userDao对象
>         name属性：类里面属性名称
>         ref属性：创建userDao对象bean标签id值
>     -->
>     <property name="userDao" ref="userDaoImpl"></property>
> </bean>
> <bean id="userDaoImpl" class="com.atguigu.spring5.dao.UserDaoImpl"></bean>
> ```
>
> 
>
>  ##### 6、基于XML方式注入内部bean和级联赋值
>
> a）注入属性-内部bean
>
> > （1）一对多关系：部门和员工
> > 一个部门有多个员工，一个员工属于一个部门（部门是一，员工是多）
> > （2）在实体类之间表示一对多关系，员工表示所属部门，使用对象类型属性进行表示
>
> ```xml
> <!--内部bean-->
>     <bean id="emp" class="com.atguigu.spring5.bean.Emp">
>         <!--设置两个普通属性-->
>         <property name="ename" value="Andy"></property>
>         <property name="gender" value="女"></property>
>         <!--设置对象类型属性-->
>         <property name="dept">
>             <bean id="dept" class="com.atguigu.spring5.bean.Dept"><!--内部bean赋值-->
>                 <property name="dname" value="宣传部门"></property>
>             </bean>
>         </property>
>     </bean>
> 
> ```
>
>  b）注入属性-级联赋值
>
> ```xml
> <!--方式一：级联赋值-->
>     <bean id="emp" class="com.atguigu.spring5.bean.Emp">
>         <!--设置两个普通属性-->
>         <property name="ename" value="Andy"></property>
>         <property name="gender" value="女"></property>
>         <!--级联赋值-->
>         <property name="dept" ref="dept"></property>
>     </bean>
>     <bean id="dept" class="com.atguigu.spring5.bean.Dept">
>         <property name="dname" value="公关部门"></property>
>     </bean>
> 
> 
>  <!--级联赋值-->
>     <bean id="emp" class="com.atguigu.spring5.bean.Emp">
>         <!--设置两个普通属性-->
>         <property name="ename" value="jams"></property>
>         <property name="gender" value="男"></property>
>         <!--级联赋值-->
>         <property name="dept" ref="dept"></property>
>         <property name="dept.dname" value="技术部门"></property>
>     </bean>
>     <bean id="dept" class="com.atguigu.spring5.bean.Dept">
>     </bean>
> 
> ```
>
>  ##### 7、IOC 操作 Bean 管理——xml 注入集合属性
>
> ```xml
> <!--（2）在 spring 配置文件进行配置-->
>     <bean id="stu" class="com.atguigu.spring5.collectiontype.Stu">
>         <!--数组类型属性注入-->
>         <property name="courses">
>             <array>
>                 <value>java课程</value>
>                 <value>数据库课程</value>
>             </array>
>         </property>
>         <!--list类型属性注入-->
>         <property name="list">
>             <list>
>                 <value>张三</value>
>                 <value>小三</value>
>             </list>
>         </property>
>         <!--map类型属性注入-->
>         <property name="maps">
>             <map>
>                 <entry key="JAVA" value="java"></entry>
>                 <entry key="PHP" value="php"></entry>
>             </map>
>         </property>
>         <!--set类型属性注入-->
>         <property name="sets">
>             <set>
>                 <value>MySQL</value>
>                 <value>Redis</value>
>             </set>
>         </property>
> </bean>
> ```
>
>  ##### 8、在集合里面设置对象类型值
>
> ```xml
> <!--第一种方式-->
>     <!--创建多个course对象-->
>     <bean id="course1" class="com.atguigu.spring5.collectiontype.Course">
>         <property name="cname" value="Spring5框架"></property>
>     </bean>
>     <bean id="course2" class="com.atguigu.spring5.collectiontype.Course">
>         <property name="cname" value="MyBatis框架"></property>
>     </bean>
>     
>    	<!--注入list集合类型，值是对象-->
>        <property name="courseList">
>            <list>
>                <ref bean="course1"></ref>
>                <ref bean="course2"></ref>
>            </list>
>        </property>
> 
> <!--第二种方式-->
> 
> <!--第一步：在 spring 配置文件中引入名称空间 util-->
> <?xml version="1.0" encoding="UTF-8"?>
> <beans xmlns="http://www.springframework.org/schema/beans"
>        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
>        xmlns:util="http://www.springframework.org/schema/util" <!--添加util名称空间-->
>        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
>                            http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">  <!--添加util名称空间-->
>     
> <!--第二步：使用 util 标签完成 list 集合注入提取-->
> <!--把集合注入部分提取出来-->
>  <!--1 提取list集合类型属性注入-->
>     <util:list id="bookList">
>         <value>易筋经</value>
>         <value>九阴真经</value>
>         <value>九阳神功</value>
>     </util:list>
> 
>  <!--2 提取list集合类型属性注入使用-->
>     <bean id="book" class="com.atguigu.spring5.collectiontype.Book" scope="prototype">
>         <property name="list" ref="bookList"></property>
>     </bean>
> 
> ```
>
> 





### Bean作用域

1. 在spring里面，设置创建bean实例是单实例还是多实例

   默认单实例对象（只创建一次）

2. 在spring里面，默认情况下，bean是一个单实例对象

   ==scope==

   ==第一个值 默认值，singleton ，表示单实例对象（单例模式）==

   ==第二个值 prototype，表示多实例对象==

3. singteton 和 prototype 区别

   a）singleton 单实例，prototype 多实例

    b）设置 scope 值是 singleton 时候，加载 spring 配置文件时候就会创建单实例对象 ；设置 scope 值是 prototype 时候，不是在加载 spring 配置文件时候创建对象，在调用 getBean 方法时候创建多实例对象

   

### Bean的生命周期

解释：从对象创建到对象销毁的过程

1. ==通过构造器创建bean实例（无参数构造）==
2. ==为bean的属性设置值和对其他bean引用（调用set方法）==
3. 把bean实力传递给bean前置处理器的方法
4. ==调用bean初始化方法（需要进行配置初始化的方法）==
5. 把bean实力传递给bean后置处理器的方法
6. ==bean可以使用了（对象获取到了）==
7. ==当容器关闭时候，调用bean的销毁的方法（需要进行配置销毁的方法）==



### bean xml自动装配

根据指定装配规则（属性名称或者属性类型），spring自动将匹配的属性值进行注入

byName 根据属性名称注入

byType 根据属性类型注入（有多个相同类型对象时候会找不到具体对象）

```xml
<bean id="emp" class="com.atguigu.spring5.autowire.Emp" autowire="byName">
<bean id="dept" class="com.atguigu.spring5.autowire.Dept" ></bean>
```

### bean xml引入外部属性文件

**方式一：直接配置数据库信息** ：（1）配置Druid（德鲁伊）连接池 （2）引入Druid（德鲁伊）连接池依赖 jar 包

```xml
<!--直接配置连接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql://localhost:3306/userDb"></property>
        <property name="username" value="root"></property>
        <property name="password" value="root"></property>
    </bean>
```

**方式二：引入外部属性文件配置数据库连接池**

（1）创建外部属性文件，properties 格式文件，写数据库信息（**jdbc.properties**）

```properties
prop.driverClass=com.mysql.jdbc.Driver
prop.url=jdbc:mysql://localhost:3306/userDb
prop.userName=root
prop.password=root
```

（2）把外部 properties 属性文件引入到 spring 配置文件中 —— 引入 context 名称空间

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"><!--引入context名称空间-->
    
        <!--引入外部属性文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--配置连接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${prop.driverClass}"></property>
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.userName}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>
    
</beans>
```

### Bean 管理(基于注解方式)

1、什么是注解

 （1）注解是代码特殊标记，格式：@注解名称(属性名称=属性值, 属性名称=属性值…)

 （2）使用注解，注解作用在类上面，方法上面，属性上面

 （3）使用注解目的：简化 xml 配置

2、Spring 针对 Bean 管理中创建对象提供注解

 下面四个注解功能是一样的，都可以用来创建 bean 实例

 （1）@Component

 （2）@Service

 （3）@Controller

 （4）@Repository

3、基于注解方式实现对象创建

 第一步 引入依赖 （引入**spring-aop jar包**）

 第二步 开启组件扫描

```xml
<!--开启组件扫描
 1 如果扫描多个包，多个包使用逗号隔开
 2 扫描包上层目录
-->
<context:component-scan base-package="com.atguigu"></context:component-scan>
```

 第三步 创建类，在类上面添加创建对象注解

```java
//在注解里面 value 属性值可以省略不写，
//默认值是类名称，首字母小写
//UserService -- userService
@Component(value = "userService") //注解等同于XML配置文件：<bean id="userService" class=".."/>
public class UserService {
 public void add() {
 System.out.println("service add.......");
 }
}
```

4、开启组件扫描细节配置

```xml
<!--示例 1
 use-default-filters="false" 表示现在不使用默认 filter，自己配置 filter
 context:include-filter ，设置扫描哪些内容
-->
<context:component-scan base-package="com.atguigu" use-defaultfilters="false">
 <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/><!--代表只扫描Controller注解的类-->
</context:component-scan>

<!--示例 2
 下面配置扫描包所有内容
 context:exclude-filter： 设置哪些内容不进行扫描
-->
<context:component-scan base-package="com.atguigu">
 <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/><!--表示Controller注解的类之外一切都进行扫描-->
</context:component-scan>
```

5、基于注解方式实现属性注入

1）@Autowired：根据属性类型进行自动装配

第一步 把 service 和 dao 对象创建，在 service 和 dao 类添加创建对象注解

第二步 在 service 注入 dao 对象，在 service 类添加 dao 类型属性，在属性上面使用注解

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
}

//Dao实现类
@Repository
//@Repository(value = "userDaoImpl1")
public class UserDaoImpl implements UserDao {
    @Override
    public void add() {
        System.out.println("dao add.....");
    }
}

```

（2）@Qualifier：根据名称进行注入，这个@Qualifier 注解的使用，和上面@Autowired 一起使用

```java
//定义 dao 类型属性
//不需要添加 set 方法
//添加注入属性注解
@Autowired //根据类型进行注入
//根据名称进行注入（目的在于区别同一接口下有多个实现类，根据类型就无法选择，从而出错！）
@Qualifier(value = "userDaoImpl1") 
private UserDao userDao;
```

（3）@Resource：可以根据类型注入，也可以根据名称注入（它属于javax包下的注解，不推荐使用！）

```java
//@Resource //根据类型进行注入
@Resource(name = "userDaoImpl1") //根据名称进行注入
private UserDao userDao;
```


（4）@Value：注入普通类型属性

```java
@Value(value = "abc")
private String name
```

6、完全注解开发

（1）创建配置类，替代 xml 配置文件

```java
@Configuration //作为配置类，替代 xml 配置文件
@ComponentScan(basePackages = {"com.atguigu"})
public class SpringConfig {
    
}
```

（2）编写测试类

```java
@Test
public void testService2(){
    //ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
    ApplicationContext context = new AnnotationConfigApplicationContext("com.atguigu");
    UserService userService = context.getBean("userService", UserService.class);
    System.out.println(userService);
    userService.add();
}
```



