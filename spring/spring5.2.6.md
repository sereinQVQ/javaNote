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

> **1.什么是Bean管理**
>
> （1）Bean管理指的是两个操作
>
> （2）Spring创建对象
>
> （3）Spring注入属性
>
> **2.Bean管理操作有两种方式**
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
> **2、基于xml方式注入属性**
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
> **IOC操作Bean管理（xml注入其他类型属性 null 和 特殊符号)**
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
> 
>
> 
>

