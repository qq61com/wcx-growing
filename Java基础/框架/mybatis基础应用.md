### ORM对象关系映射

### mybatis-config配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--引入外部配置文件-->
    <properties resource="jdbc.properties"></properties>
    <!--取别名-->
    <typeAliases>
        <!--指定实体类取别名-->
        <typeAlias type="com.wcx.bean.Testoa" alias="Tetsoa"/>
        <!--直接给所有的实体类取别名-->
        <package name="com.wcx.bean"/>
    </typeAliases>
    <!--环境-->
    <environments default="development">
        <!--环境-JDBC -->
        <environment id="development">
            <!--使用jdbc的事务-->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置连接信息 底层用到连接池-->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <!--引入映射文件-->
    <mappers>
        <mapper resource="com/wcx/mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```

### 映射文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.wcx.mapper.UserMapper">
    <!--查询，指定返回值类型-->
    <select id="findList" resultType="com.wcx.bean.Testoa">
        select * from testoa
    </select>
    <!--新增，指定参数类型，可以省略-->
    <insert id="add" parameterType="com.wcx.bean.Testoa">
        insert into testoa values(#{id},#{name})
    </insert>

</mapper>
```

### 实现类

```java
SqlSession session = null;

//配置文件路径
    String res = "mybatis-config.xml";
    //加载配置文件，获得一个输入流
    InputStream inputStream = Resources.getResourceAsStream(res);
    //获取session工程
    SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
    //开启会话
    session = factory.openSession();//添加参数是否自动提交事务
    //执行sql获得结果
    List<Testoa> testoas = session.selectList("com.wcx.mapper.UserMapper.findList");
```

实体类保留无参构造

mybatis不自动提交事务

mybatis在底层使用动态代理（反射，自动生成Mapper实现类）

```java
session.getMapper(UserMapper.class)
```

省略实现类：

```java
	@Before
    public void init(){
        session=MyBatisUtils.getSession();
        mapper = MyBatisUtils.getSession().getMapper(UserMapper.class);
    }
```

Mapper类要与映射文件名相同

```xml
<mappers>
    <!--<mapper resource="com/wcx/mapper/UserMapper.xml"/>-->
    <!--<mapper class="com.wcx.mapper.UserMapper"/>-->
    <!--直接映射报名，包下的所有Mapper接口都被映射-->
    <package name="com.wcx.mapper"/>
</mappers>
```

### 模糊查询

1.不能在mapper.xml里拼接#{}在处理阶段拼接

2.${%xxx%}拼接 '%${value}%' @Parm

```xml
<select id="findByName" resultType="com.wcx.pojo.User" parameterType="string">
    select * from testoa where `name` like '%${value}%'
</select>
```

### #和$的区别

#{}安全=？用于参数传递，防止SQL注入

${}用于SQL拼接

### 新增之后获取主键值,开启自动增长映射

```xml
方式一：
<insert id="addUser" parameterType="user" useGeneratedKeys="true" keyProperty="id">
    insert into testoa values(#{id},#{name})
</insert>
方式二：
<insert id="addUser" parameterType="User">
        <selectKey keyProperty="id" order="AFTER" resultType="int">
            <!--获取最后一次新增成功的id值-->
            select last_insert_id()
        </selectKey>
        insert into testoa
        values (null, #{name})
</insert>
```

### 查询参数Map

```xml
<select id="findUserByBirthday" resultType="com.wcx.pojo.User">
    <!--#中填写map的Key-->
    select * from testoa where id between #{start} and #{end};
</select>
```

返回值是Map

```xml
<select id="getResult" resultType="java.util.Map">
    select max(id) `maxid` from testoa
</select>
```

```xml
<!--方法二-->
<select id="getResult1" resultMap="map1">
    select max(id) from testoa
</select>
<resultMap id="map1" type="map">
    <result property="maxid" column="max(id)"/>
</resultMap>
```

### 实体类属性与数据库字段不一致

查询语句中取别名=类属性

```xml
select * from xx 
select id ,uname as 'username ' from xx
```

```xml
<!--映射字段-->
<resultMap id="map1" type="map">
    <id column="id" property="id"/>
    <result column="name" property="name"/>
</resultMap>
```

### 动态SQL

基于OGNL表达式

if语句 简单判断

choose 相当于Java中的switch

trim

where

set更新

foreach