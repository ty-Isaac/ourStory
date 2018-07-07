---
title: Hibernate Study
date: 2017-08-23 15:55:58
categories: 编程
tags: [dao,Hibernate框架]
---

# Hibernate入门

## 搭建hibernate环境

#### 第一步 导入hibernate的jar包

1. hibernate 文件中的required中的jar包要导入
2. jpa 中的 jar 包
3. 因为使用 hibernate 时候，会有日志信息输出。hibernate 本身没有日志输出 jar 包，导入其他日志jar包
4. mysql驱动 jar 包

#### 第二步 创建实体类

hibernate要求实体类中有一个属性是唯一的

#### 第三步  配置实体类和数据库表一一对应关系（映射关系）

使用配置文件实现映射关系

1. 创建xml格式的配置文件
   - 映射配置文件和位置没有固定要求
   - 建议：**在实体类所在包里面创建，\<classname>.hbm.xml**
2. 配置是xml格式，在配置文件中首先引入 xml 约束
   - 学过约束 dtd 、 schema 在hibernate 都是 dtd文件

```xml-dtd
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
```

完整的配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>
    <!-- 1. 配置类和表对应
            class 标签
            name 属性： 实体类全路径
            table 属性： 数据库表名称
    -->
    <class name="User.User" table="t_user" >
        <!-- 2. 配置实体类 id 和 表 id 对应
                hibernate 要求在实体类有一个属性唯一值
                hibernate 要求表有字段作为唯一值
        -->
        <!-- id 标签
                name属性: 实体类里面id属性名称
                column属性: 生成的表字段名称
        -->
        <id name="uid" column="uid">
            <!-- 设置数据库表id增长策略
                 native: 生成id值主键自动增长-->
            <generator class="native"></generator>
        </id>
        <!--配置其他的属性和表字段对应
            name属性: 实体类属性名称
            column属性: 生成表的字段名
        -->
        <property name="username" column="username"></property>
        <property name="password" column="password"></property>
        <property name="address" column="address"></property>
    </class>
</hibernate-mapping>
```

- 映射文件是一个以 \<hibernate-mapping> 为根元素的 XML 文件，里面包含所有 \<class> 标签
- \<class>标签是用来定义从一个 Java 类到数据库表的特定映射。Java 的类名使用 **name** 属性表示，数据库表名用**table**属性表示
- \<meta> 标签是一个可选元素，被用来修饰类
- \<id> 标签将类中的唯一属性与数据库表中主键关联。id 元素中的 **name** 属性引用类的性质， **column** 属性引用数据库表的列。 **type** 属性保存 hibernate 映射的类型，这个类型会将从 Java 转换成 SQL 数据类型
- \<id> 标签下的\<generator> 标签用来自动生成主键值。设置 generator 标签中的 **class** 属性可以设置 **native**
- \<property> 标签将Java 类的属性与数据库表的列匹配。标签中 **name** 属性引用的是类的元素，**column**属性引用的是数据库表的列。 **type** 属性保存Hibernate映射的类型，这个类型会将从Java转换为SQL数据类型

#### 第四步  创建hibernate的核心配置文件

1. 核心配置文件格式 xml ，但是核心配置文件名称和位置式固定的

   - 位置：必须 src 下面
   - 名称：必须hibernate.cfg.xml

2. 引入dtd约束

   ```xml-dtd
   <!DOCTYPE hibernate-configuration PUBLIC
           "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
           "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
   ```

3. hibernate操作过程中，只会加载核心配置文件，其他配置文件不会进行加载


##### 第一部分 配置数据库信息

```Xml
 <!--第一部分：配置数据库信息-->
        <property name="hibernate.connection.driver_class" >com.mysql.jdbc.Driver</property>
        <property name="hibernate.connection.url">jdbc:mysql:///hibernate_day01</property>
        <property name="hibernate.connection.username">root</property>
        <property name="hibernate.connection.password">YourPassword</property>
```



##### 第二部分 配置hibernate信息

```xml
     <!--第二部分：配置hibernate信息 (可选的)-->
        <!--输出低层sql语句-->
        <property name="hibernate.show_sql">true</property>
        <!--输出低层sql语句格式-->
        <property name="hibernate.format_sql">true</property>
        <!--hibernate帮创建表，需要配置之后
            update: 如果已经有表，更新，如果没有，创建
        -->
        <property name="hibernate.hbm2ddl.auto">update</property>
        <!-- 配置数据库方言，数据库中特有的设置关键字-->
        <property name="hibernate.dialect">org.hibernate.dialect.Mysql</property>
```



##### 第三部分 把映射文件放到核心配置文件中

```Xml
<!--第三部分：把映射文件放到配置文件中-->
        <mapping resource="User/User.hbm.xml" />
```

注意路径中文件的位置，与当前的核心配置文件的相对路径



## 实现添加操作

#### 第一步 加载hibernate核心配置文件

#### 第二步 创建SessionFactory对象

#### 第三步 使用SessionFactory 创建Session对象

#### 第四步 开启事务

#### 第五步 写具体逻辑 crud操作

#### 第六步 提交事务

#### 第七步 关闭资源

测试文件，使用单元测试

```java
package hibernateTest;

import User.User;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;
import org.junit.Test;

/**
 * Created by isaac on 2017/9/3.
 */
public class HibernateDemo {
    @Test
    public void TestADD(){
//    第一步 加载hibernate核心配置文件
        //在src下面找到名称是hibernate.cfg.xml
        //在hibernate里面封装对象
        Configuration cfg = new Configuration();
        cfg.configure();
//    第二步 创建SessionFactory对象
        //读取hibernate中核心配置文件内容，创建sessionFactory
        //在此过程中，根据映射关系，在配置数据库里面把表创建
        SessionFactory sessionFactory = cfg.buildSessionFactory();
//    第三步 使用SessionFactory 创建Session对象
        //类似于连接
        Session session = sessionFactory.openSession();
//    第四步 开启事务
        Transaction tx = session.beginTransaction();
//    第五步 写具体逻辑 crud操作
        User user = new User();
        user.setUsername("Isaac");
        user.setPassword("10086");
        user.setAddress("China");
        //调用session方法实现添加
        session.save(user);
//    第六步 提交事务
        tx.commit();
//    第七步 关闭资源
        session.close();
        sessionFactory.close();
    }
}

```



# Hibernate配置文件详解

## Hibernate映射配置文件

1. 映射配置文件名称和位置没有固定要求
2. 映射配置文件中，标签name属性值写实体相关内容
   - class 标签 name 属性值实体类全路径
   - id 标签和 property 标签 name 属性值 实体属性值
3. id 标签和 property标签，column属性可以省略
   - 不写值，column和name属性值一样
4. property标签type属性设置生成字段类型

## Hibernate核心配置文件

1. 配置写位置要求 ，在session-factory下
2. 配置三部分要求
   - 数据库部分必须
   - hibernate部分可选的
   - 映射文件必须的
3. 核心配置文件名称和位置固定的
   - 位置：src下面
   - 名称：hibernate.cfg.xml

# Hibernate核心API

## Configuration

1. 代码

   ```java
   	Configuration cfg = new Configuration();
       cfg.configure();
   ```

2. 到src下面找到名称hibernate.cfg.xml配置文件，创建对象，把配置文件放到对象里面（加载核心配置文件）

## SessionFactory（重点）

1. 使用configuration对象创建SessionFactory对象

   1. 创建SessionFactory过程中坐的事情：

      - 根据核心配置文件中，有数据库配置，有文件映射部分，到数据库里面根据映射关系把表创

      - ```Xml
        <property name="hibernate.hbm2ddl.auto">update</property>
        ```

2. 创建sessionFactory过程中，整个过程特别消耗资源

   1. 在Hibernate操作中，建议一个项目一般创建一个SessionFactory对象

3. 具体实现

   1. 写工具类，写静态代码块实现

      静态代码块在类加载时候执行，执行一次

      ```Java
      public class HibernateUtils {
          private static Configuration cfg = null;
          private static SessionFactory sessionFactory = null;
          static {
              cfg = new Configuration();
              cfg.configure();
              sessionFactory = cfg.buildSessionFactory();
          }
          public static SessionFactory getSessionFactory(){
              return sessionFactory;
          }
      }
      ```

## Session(重点)

1. Session 类似于 JDBC 中connection
2. 调用session里不同的方法实现 crud 操作
   - 添加 save()
   - 修改 update()
   - 删除 delelte()
   - 根据 id 查询 get()
3. session对象单线程对象
   - session对象不能共用，只能自己使用

## Transaction

1. 事务对象

   ```Java
   Transaction tx = session.beginTransaction();
   ```

2. 事务提交和回滚

   ```java
   tx.commit();
   tx.rollback();
   ```

# 实体类编写规则

1. 实体类里面属性私有的

2. 私有属性使用公开的 set 和 get 方法

3. 要求实体属性有唯一值(一般使用id值)

4. 实体类属性建议不使用基本数据类型，使用基本数据类型对应的包装类

   1. 八个基本数据类型对应的包装类
      - int - Integer
      - char - Character
      - 其他的都是首字母大写 比如 double - Double
   2. 比如 表示学生分数，假如 int score
      - 学生得了 0 分 ， int score = 0 ；
      - 学生没参加考试 ， int score = 0；不能准确表示学生是否参加考试

   解决：使用包装类就可以，Integer score = 0, 表示学生得了 0 分；Integer score = null 表示学生没有参加考试

# 主键生成规则

# 实体类crud操作

### 添加操作

1. 调用session里面的save方法实现

   ```java
    User user = new User();
    user.setUsername("小马");
    user.setPassword("10080");
    user.setAddress("Panda");
    //调用session方法实现添加
    session.save(user);
   ```

### 根据id进行操作 

1. 调用session里面的get方法实现

   ```java
   //第一个参数：实体类的class
   //第二个参数：id值
    User user = session.get(User.class,1);
    System.out.println(user);
   ```

2. ```sql
   select
           user0_.uid as uid1_0_0_,
           user0_.username as username2_0_0_,
           user0_.password as password3_0_0_,
           user0_.address as address4_0_0_ 
       from
           t_user user0_ 
       where
           user0_.uid=?
   ```

   执行结果如上

### 修改操作

首先查询，再修改值

1. 根据 id 查询，返回对象
2. 根据uid修改对象

```java
        //根据 id 查询
        User user = session.get(User.class,2);
        //向返回的user对象里面设置修改以后的值
        user.setUsername("Utoly");
        //调用session方法修改
        //执行过程：到user对象中，找到Uid值，根据uid进行修改
        session.update(user);
```



### 删除操作

调用session里面 delete方法实现

```java
        //第一种 根据id查询对象
        User user = session.get(User.class,2);
        session.delete(user);
        //第二种
        User user1 = new User();
        user1.setUid(1);
        session.delete(user1);
```

# 实体类对象状态（概念）

1. 实体类对象有三种
   1. 瞬时态：对象里面没有 id 值，对象与 session 也没有关系
   2. 持久态：对象里面有 id  值，对象与session 有关联
   3. 托管态：对象有 id 值，对象与 sessinon 没有关联
2. 演示操作实体类对象的方法
   1. saveOrUpdate 方法：实现添加、修改

# Hibernate的一级缓存

## 什么是缓存

 数据存到数据库里面，数据库本身是文件系统，使用流方式操作文件效率不高
- 把数据存到内存中，不需要使用流方式，可以直接读取内存中数据
- 把数据放到内存中，提供读取效率

## Hibernate缓存

1. hibernate框架中提供很多优化方式，hibernate的缓存就是一个优化方式
2. hibernate缓存特点：
   1. hibernate的一级缓存
      - hibernate 的一级缓存默认打开
      - hibernate 的一级缓存使用范围，是 session 范围，从 session 创建到session关闭
      - hibernate的一级缓存中，存储数据必须 持久态数据
   2. hibernate的二级缓存
      - 目前已经不使用了，替代技术 redis 
      - 二级缓存默认不是打开的，需要配置
      - 二级缓存使用范围，是 SessionFactory 范围
3. 验证一级缓存存在

# Hibernate 绑定 session

1. 在hibernate核心配置文件中配置

   ```xml
    <property name="hibernate.current_session_context_class">thread</property>
   ```

   ​

2. 调用sessionFactory里面的方法得到

   ```Java
   //提供返回与本地线程绑定的session的方法
       public static Session getSession(){
           return sessionFactory.getCurrentSession();
       }
   ```



# Hibernate的 API 使用

## Query 对象

1. 使用query对象，不需要写 sql 语句，但是写 hql 语句
   - hql: hibernate query language , hibernatenate 提供查询语句，这个hql语句与普通SQL语句很相似
   - hql 与 sql 的区别：
     - 使用 sql 操作表和表字段
     - 使用 hql 操作实体类和属性


2. 查询所有 hql 语句：

   from 实体类名称

3. Query 对象使用

   1. 创建 query 对象
   2. 调用 query 对象里面的方法

   ```Java
    //1. 创建query对象
    //方法里写hql语句
    Query query = session.createQuery("from User");
    //2. 调用query对象的方法得到结果
    List<User> list = query.list();
    for(User user:list){
      System.out.println(user);
    }
   ```

   ​

## Criteria 对象

1. 这个对象查询操作，但是使用这个对象时候，不需要写语句，直接调用方法实现。

2. 实现过程

   - 创建 criteria 对象
   - 调用方法得到结果

   ```Java
    //1.创建criteria对象
    Criteria criteria = session.createCriteria(User.class);
    //2.调用方法得到结果
    List<User> list = criteria.list();
    for(User user : list){
       System.out.println(user);
    }
   ```

   ​

## SQL Query 对象

1. 使用 hibernate 时候，调用低层 sql 实现

2. 实现过程

   - 创建对象
   - 调用对象的方法得到结果
   - 返回list集合每部分是数组

   ```java
    //1.创建对象
    SQLQuery sqlQuery = session.createSQLQuery("select * from t_user");
    List<Object[]> list = sqlQuery.list();
    //返回每部分都是数组
    for(Object[] objects:list){
       System.out.println(Arrays.toString(objects));
    }
   ```

   - 返回list中每部分是对象形式

# 表与表之间关系回顾

## 一对多

- 分类和商品关系，一个分类里有多个商品，一个商品只有一个分类
- 客户和联系人是一对多关系
  - 客户：与公司有业务往来，百度、新浪、360...
  - 联系人：

## 多对多

- 订单和商品，一个订单里有多个商品，一个商品可以属于多个订单
- 用户和角色多对多关系
  - 用户：小王、小马、小宋
  - 角色：总经理、秘书、司机、保安
  - 一个用户可以有多个角色，一个角色有多个用户

## 一对一



# Hibernate的一对多操作（重点）

## 一对多映射配置

以客户和联系人为例，客户是一，联系人是多

### 第一步 创建实体类

### 第二步 让两个实体类之间相互表示

1. 在客户实体类里面表示多个联系人
   - 一个客户里面有多个联系人 
   - Hibernate要求使用集合表示多的数据 set
2. 在联系人实体类里面表示所属客户



### 第三步 配置映射关系

在客户映射文件中，表示所有联系人

使用 set 标签表示所有联系人

set标签里面有name属性，属性值写在客户实体类里面表示联系人的 set 集合名称







## 一对多级联操作

