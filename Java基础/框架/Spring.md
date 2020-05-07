### 基础认识

#### 1.是什么

Spring是一个开放源代码的设计层面框架，解决业务层和其他各层的松耦合问题

- 开源：开放式源代码
- 轻量：jar包少，单独使用
- 一站式：在三层结构中，在每一层都提供解决方案
  - web层SpringMVC
  - service层Spring IOC
  - dao层jdbc Template

方便解耦

aop



#### 2、核心：

- IOC：控制反转
- AOP：面向切面编程--拦截器

spring版本4.X

### IOC的底层原理

1. xml配置文件
2. xml解析
3. 设计模式：工厂模式
4. 反射

![过程图](https://nsaimg.com/2020/05/06/6f161a61f046e.jpg)

 

### Spring的bean管理

什么是bean管理的两个操作

- 创建对象
- 注入属性

两种方式

1. 通过xml配置文件方式实现
2. 通过注解方式实现



#### bean管理(创建对象)

```
<bean name="user" class="com.wcx.sp.User"></bean>
使用类中的无参构造创建对象
使用bean标签创建对象，在bean标签里又常用的属性
id属性：根据id值获取到对象
name属性：和id类似，过时
class属性：类的全路径
scope属性：创建对象是单例还是多例
	scope值固定
	常用两个
	默认值singleton，单例
	<bean name="user" class="com.wcx.sp.User" scope="singleton"></bean>
	prototype，多例
	<bean name="user" class="com.wcx.sp.User" scope="prototype"></bean>
jdk中常见注解
@Overrid
@SupperrssWarnings("all")
```

#### bean管理（注入属性）

1. 封装set设置
2. 有参构造

spring配置文件实现set方法注入属性

```xml
<bean name="user" class="com.wcx.sp.User" scope="singleton">
        <property name="name" value="qq"/>
</bean>
property配置属性，name=属性名称
```

spring配置文件实现有参构造注入属性

```xml
<bean name="user" class="com.wcx.sp.User">
    <constructor-arg name="name" value="ww"></constructor-arg>
</bean>
```

### 重要概念

#### IOC和DI的区别：

1. IOC：控制反转，不通过new来创建对象
2. DI：依赖注入，就是注入属性
   - di要在ioc基础上完成。

#### ApplicatContext和BeanFactory的区别：

都是用来Spring的配置文件

使用ApplicatContext只要一加载，所有的bean都会创建

BeanFactory加载，不会直接创建对象，获取时才会创建bean

#### Spring注入属性的方式

set和构造参数

#### 一站式框架

在各个层都提供解决技术

#### ioc底层原理和好处

- ioc底层使用工厂模式和反射实现
- 好处是解耦合-工厂模式

### 注入其他类型

注入数组、list集合、map集合、properties

```xml
<bean name="test1" class="com.wcx.sp.User">
        <!--数组/lisi集合-->
        <property name="arr">
            <array><!--list-->
                <value>aa</value>
                <value>bb</value>
                <value>cc</value>
            </array>
        </property>
        <!--map集合-->
        <property name="map">
            <map>
                <entry key="aa" value="bb"></entry>
                <entry key="aa" value="bb"></entry>
                <entry key="aa" value="bb"></entry>
            </map>
        </property>
        <!--properties集合-->
        <property name="properties">
            <props>
                <prop key="aa">bb</prop>
                <prop key="aa">bb</prop>
                <prop key="aa">bb</prop>
            </props>
        </property>
    </bean>
```

### Spring的bean管理（注解）

#### 使用注解的目的：简化配置，使用注解之后省略很多配置

为了替代配置文件，没有完全替代

#### 注解

- 注解：代码中的特殊标记，通过注解实现一些特定的功能
- 写法：@注解名称(属性名称1=value,属性名称2=value)
- 使用注解的目的是为了简化配置
- 注解可以用在类、方法、属性上面

#### 用注解方式创建对象和注入属性



- componen
- controller
- service
- repository

```xml
<!--扫描包-->
<context:component-scan base-package="com.wcx.sp"></context:component-scan>
```

```java
//先创建对象注入值
@Value(value = "aa")
private String name;
```

注解不需要set方法

```java
@AutoWired//自动装配，判断类型 spring提供
//Resource Resource(name = )由javax包提供jdk自带
private UserDao userDao;
```

spring注入属性

普通类型value，对象类型AutoWired/Resource

### 注解和配置文件混合使用

需要调用的对象由框架提供，无法只使用注解完成

配置文件创建对象，注解注入值

### AOP

面向切面编程，不改变源代码增强类的方法

横向抽取机制

通知/增强：实际增强的部分的内容成为通知

通知的五种类型：

- 前置 方法之前执行
- 后置 方法之后执行
- 环绕 方法前后都执行
- 异常 方法运行异常时执行
- 最终 类似finally，总会被执行

切面：是操作过程

把增强用到切入点过程

把增加的部分写到方法的过程

AOP场景：事务

### Spring的事务管理

#### 事务传播行为

多事务操作之间相互调用的时候，事务处理的方式

- required 支持当前事务，如果不存在，就新建一个
- requires_new如果有事务存在挂起当前事务，创建一个新的事物
- nested如果当前事物存在，则嵌套事务执行

#### spring事务管理api

spring进行事务管理，提供通用的接口

PlatformTransactionManager事务管理器

spring针对不同的dao层框架，提供接口的实现类

#### 添加事务

在spring中进行事务管理有两种方式

1. 编程式事务管理
2. 声明式事务管理（基于配置、基于注解）

声明式事务（注解）

1. 创建事务管理器
2. 配置中开启事务注解
3. 事务添加到三层架构中的service层

```xml
<!--配置事务-->
<!--配置事务管理器-->
<bean id="transactionManager" class="">
    <property name="dataSource" ref="dataSource"></property>
</bean>
<!--开启事务的注解驱动-->
<tx:annotation-driven ></tx:annotation-driven>
```

@Transaction

### 带接口的注入方式

接口有多个实现类如何注入

1. @Resource(name = "xxx")
2. @AutoWired
   @Qulifier("xxx")

### 分模块开发

分开写配置文件

总配置文件引入其他配置

```xml
<import resource="classpath:lsorinf-cfg.xml"/>
```

### 总结

概念