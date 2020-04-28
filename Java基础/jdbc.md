jdbc需要驱动

不同的数据库需要不同的驱动

jdbc的工作原理

jdbc API 定义了一系列的接口和雷，集成在java.sql,javax.sql中

```
Properties properties = new Properties();
InputStream in = new FileInputStream("E:\\IdeaProjects\\TestJDBC\\src\\main\\resources\\jdbc.properties");
properties.load(in);
driver = properties.getProperty("jdbc.driver");
url = properties.getProperty("jdbc.url");
username = properties.getProperty("jdbc.username");
password = properties.getProperty("jdbc.password");

Class.forName(driver);
```

```
conn =Utils.getConn();
try {
    statement = conn.createStatement();
    resultSet = statement.executeQuery(sql);
} catch (SQLException throwables) {
    throwables.printStackTrace();
}
```

```
int count = 0;
conn=Utils.getConn();
try {
    statement = conn.createStatement();
    count = statement.executeUpdate(sql);
    return count;
} catch (SQLException throwables) {
    throwables.printStackTrace();
}finally {
    try {
        statement.close();
        conn.close();
    } catch (SQLException throwables) {
        throwables.printStackTrace();
    }
}
```

PreparedStatement

sql预编译，性能高

不拼接字符，用占位符

继承于Statement接口



### DriverManager类

是驱动管理类

常用方法DriverManager.registerDriver(new Driver())

```java
//不推荐使用
//会被注册两次
DriverManager.registerDriver(new com.mysql.cj.jdbc.Driver());
```

### Connection类

```
//获得connection对象
DriverManager.getConnection(url,user,password)
```

常用方法：

- createStatement()创建向数据库发送SQL的statement对象
- prepareStatment(sql)创建像数据库发送预编译SQL的PrepareSatement对象
- prepareCall()创建执行存储过程的callableStament对象
- setAutoCommit(boolean)设置事务是否自动提交
- commit()在连接上提交事务
- rollback()在连接上回滚事务

### ResultSet类

resultSet对象维护了一个指向表格数据的游标，初始的时候游标在第一行之前，next()方法

常用方法：

getObject(列索引/列名)

getString()获得指定类型

next()

Prverious()前一行

absolute()

beforeFirstr()

afterFirst()

### 批处理

```java
conn.setAutoCommit(false);	//关闭自动提交

statement.addbatch(SQL);	//添加SQL

statement.executebatch()	//执行批处理

conn.commit();				//手动提交
```

### 事务的概念

