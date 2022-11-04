# spring5.2.6 事务

1、事务概念
 a）什么是事务

 （1）事务是数据库操作最基本单元，逻辑上一组操作，要么都成功，如果有一个失败所有操 作都失败

 （2）典型场景：银行转账 张三 转账 100 元 给 李四； 张三少 100，李四多 100

 b）事务四个特性（ACID） （1）原子性 （2）一致性 （3）隔离性 （4）持久性 可查看这篇文章描述

2、事务操作（模拟事务操作环境）
 a）创建 service，搭建 dao，完成对象创建和注入关系

```java
//（1）service 注入 dao，在 dao 注入 JdbcTemplate，在 JdbcTemplate 注入 DataSource
@Service
public class UserService {
 //注入 dao
 @Autowired
 private UserDao userDao;
}


@Repository
public class UserDaoImpl implements UserDao {
 @Autowired
 private JdbcTemplate jdbcTemplate;
}
```

 b）在 dao 创建两个方法：多钱和少钱的方法， 在 service 创建方法（转账的方法）





#### 3、事务操作（声明式事务管理参数配置）

##### propagation  Transactional（事务传播行为）
 a）在 service 类上面添加注解@Transactional，在这个注解里面可以配置事务相关参数

 b）propagation（事务传播行为）：多事务方法直接进行调用，这个过程中事务 是如何进行管理的

 c）spring框架事务传播行为有七种：下面只介绍常用的两种传播行为
```java
@Transactional(propagation = Propagation.REQUIRED,)			//事务一
public void add(){
    //调用update方法
    update();
}

					  
public void update(){ 	//事务二
    
}
```

（1）REQUIRED：如果add方法本身有事务，调用update方法后，update使用当前add方法里面的事务；

 如果add方法本身没有事务，调用update方法后，创建新的事务

（2）REQUIRED_NEW：使用add调用update方法，不论add方法是否有事务，都会创建新的事务。

> 还可以加上：@Transactional(propagation=Propagation.NOT_SUPPORTED,readOnly=true)，这样就做成一个只读事务，可以提高效率。 
>
>    各种属性的意义： 
>
>    <font color=red>REQUIRED</font>:业务方法需要在一个容器里运行。如果方法运行时，已经处在一个事务中，那么加入到这个事务，否则自己新建一个新的事务。 
>
>    <font color=red>NOT_SUPPORTED</font>:声明方法不需要事务。如果方法没有关联到一个事务，容器不会为他开启事务，如果方法在一个事务中被调用，该事务会被挂起，调用结束后，原先的事务会恢复执行。 
>
>    <font color=red>REQUIRESNEW</font>:不管是否存在事务，该方法总汇为自己发起一个新的事务。如果方法已经运行在一个事务中，则原有事务挂起，新的事务被创建。 
>
>    <font color=red>MANDATORY</font>：该方法只能在一个已经存在的事务中执行，业务方法不能发起自己的事务。如果在没有事务的环境下被调用，容器抛出例外。 
>
>    <font color=red>SUPPORTS</font>:该方法在某个事务范围内被调用，则方法成为该事务的一部分。如果方法在该事务范围外被调用，该方法就在没有事务的环境下执行。 
>
>    <font color=red>NEVER</font>：该方法绝对不能在事务范围内执行。如果在就抛例外。只有该方法没有关联到任何事务，才正常执行。 
>
>    <font color=red>NESTED</font>:如果一个活动的事务存在，则运行在一个嵌套的事务中。如果没有活动事务，则按REQUIRED属性执行。它使用了一个单独的事务，这个事务拥有多个可以回滚的保存点。内部事务的回滚不会对外部事务造成影响。它只对DataSourceTransactionManager事务管理器起效。 

##### ioslation（事务隔离级别）

a）事务有特性成为隔离性，多事务操作之间不会产生影响。不考虑隔离性产生很多问题

b）有三个读问题：脏读、不可重复读、虚（幻）读

**脏读：** 一个未提交事务读取到另一个未提交事务的数据

**不可重复读：** 一个未提交事务读取到另一提交事务修改数据

**虚读：** 一个未提交事务读取到另一提交事务添加数据

3)解决：通过设置事务隔离级别，解决读问题

@Transactional(propagation = Propagation.REQUIRED,isolation = Isolation.REPEATABLE_READ)

##### timeout：超时时间

（1）事务需要在一定时间内进行提交，如果不提交进行回滚 （2）默认值是 -1（不超时） ，设置时间以秒单位进行计算

##### readOnly：是否只读

（1）读：查询操作，写：添加修改删除操作

（2）readOnly 默认值 false，表示可以查询，可以添加修改删除操作

（3）设置 readOnly 值是 true，设置成 true 之后，只能查询

##### rollbackFor：回滚

 设置出现哪些异常进行事务回滚

##### noRollbackFor：不回滚

 设置出现哪些异常不进行事务回滚
