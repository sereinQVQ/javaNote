# spring5.2.6

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

1. 通过构造器创建bean实例（无参数构造）
2. 为bean的属性设置值和对其他bean引用（调用set方法）
3. 调用bean初始化方法（需要进行配置初始化的方法）
4. bean可以使用了（对象获取到了）
5. 当容器关闭时候，调用bean的销毁的方法（需要进行配置销毁的方法）
