# JDBC

JDBC是**Java Database Connectivity**的缩写，是标准的Java API，是一套客户端程序与数据库交互的规范。 JDBC提供了一套通过Java操纵数据库的完整接口。

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;

public class Main {
    public static void main(String[] args) throws Exception {
        //注册驱动
        Class.forName("com.mysql.jdbc.Driver");
        //获取连接对象
        String url = "jdbc:mysql://localhost:3306/db1";
        String username = "root";
        String password = "root";
        Connection conn = DriverManager.getConnection(url, username, password);
        //定义SQL语句
        String sql = "update tb_stu set name = '小白' where id = 1";
        //获取执行SQL的对象
        Statement statement = conn.createStatement();
        //执行SQL
        int count = statement.executeUpdate(sql);
        //处理结果
        System.out.println(count);
        //释放资源
        conn.close();
        statement.close();
    }
}
```

## `DriverManager`

* 注册驱动

  `Class.forName("com.mysql.jdbc.Driver");`

  ```java
  public class Driver extends NonRegisteringDriver implements java.sql.Driver {
      public Driver() throws SQLException {
      }
  
      static {
          try {
              DriverManager.registerDriver(new Driver());
          } catch (SQLException var1) {
              throw new RuntimeException("Can't register driver!");
          }
      }
  }
  ```

  `Tips`:

  * MySQL5之后的驱动包，可以省略注册驱动的步骤
  * 自动加载`jar`包中`META-INF/services/java.sql.Driver`文件中的驱动类

* 获取数据库连接

  ```java
  static Connection	getConnection(String url, String name, String password) //尝试建立与给定数据库 URL 的连接。
  ```

  参数：

  * `url`:连接路径

    `jdbc:mysql://主机地址:端口号/数据库名称?参数键值对1&参数键值对2`

  * `username`:用户名

  * `password`:密码

## `Connection`

* 获取执行SQL的对象

  ```java
  Statement	createStatement() //创建一个 Statement 对象，用于将 SQL 语句发送到数据库。
  ```

  ```java
  PreparedStatement	prepareStatement(String sql) //创建一个 `PreparedStatement` 对象，用于将参数化 SQL 语句发送到数据库。预编译，用来防止SQL注入
  ```

  ```java
  CallableStatement prepareCall(sql) //执行存储过程的对象
  ```

* 管理事务

  `MYSQL`:

  * 开启事务：`BEGIN;/START TRANSACTION;`

  * 提交事务：`COMMIT;`

  * 回滚事务：`ROLLBACK;`

    `MYSQL`默认自动提交事务

  `JDBC事务管理：Connection接口中定义了三个对应的方法`;

  * 开启事务：`setAutoCommit(boolean autoCommit); //true为制动提交事务， flase为手动提交事务，即为开启事务`
  * 提交事务：`commit()`
  * 回滚事务：`rollback()`

## `Statement`

* 用来执行SQL语句

  ```java
  int	executeUpdate(String sql) //执行给定的 SQL 语句，它可以是 `INSERT`、`UPDATE` 或 `DELETE` 语句或不返回任何内容的 SQL 语句，例如 SQL DDL和DML。返回值：影响的行数。
  ```

  ```java
  ResultSet execteQuery(String sql) //执行DQL语句 返回值：ResyltSet结果对象
  ```

  

## `ResultSet`

封装了DQL查询语句的结果

* 获取查询结果

  ```java
  boolean	next() //将光标从当前位置向前移动一行。判断当前行是否是有效行。
  ```

  ```java
  xxx getXxx(参数) // 获取数据
  xxx: 数据类型
  int 列的编号从1开始
  String： 列的名称
  ```

  

## `PreparedStatement`

预编译SQL防止注入

* 获取`PreparedStatement`对象

  ```java
  String sql = "select * from user where username = ? and password = ?";
  var pstmt = conn.prepareStatement(sql);
  ```

* 设置参数值

  `setXxx(参数位置, 值) //给?赋值`

* 执行SQL

  `executeUpdate(); //不需要再传递sql`

## 数据库连接池

数据库连接池（Database Connection Pool）是一种用于管理和复用数据库连接的技术。它解决了频繁创建和销毁数据库连接所带来的性能开销和资源浪费问题。连接池通过预先创建一定数量的数据库连接，并将它们保存在连接池中，以供应用程序使用。

连接池的主要目的是提高数据库访问的性能和效率。它通过以下方式解决了一些问题：

1. 连接的创建和销毁开销：数据库连接的创建和销毁是一项耗时的操作，涉及到网络通信和身份验证等过程。使用连接池可以避免频繁地创建和销毁连接，从而减少了这些开销，提高了数据库访问的效率。

2. 连接的复用：连接池会在应用程序初始化时创建一定数量的连接，并在需要时分配给应用程序使用。当应用程序使用完连接后，它将连接返回给连接池而不是真正关闭它。这样，连接可以被复用，避免了每次请求都需要重新创建连接的开销。

3. 连接的管理和控制：连接池可以对连接进行管理和控制，包括连接的分配、回收和超时等。它可以确保连接的可用性和稳定性，避免连接过多或过少的情况发生。

连接池的优点包括：

1. 提高性能：连接池通过复用连接和减少连接的创建和销毁开销，显著提高了数据库访问的性能和响应速度。

2. 资源管理：连接池可以有效地管理数据库连接资源，避免了连接资源的浪费和过度占用，提高了系统的资源利用率。

3. 并发处理：连接池可以处理多个并发请求，通过合理分配连接，提供给每个请求所需的连接，从而支持系统的并发处理能力。

4. 控制连接数量：连接池可以限制连接的数量，防止连接过多导致数据库性能下降或资源耗尽的问题。

总之，数据库连接池是一种有效的技术，通过管理和复用数据库连接，提高了数据库访问的性能和效率，同时有效地管理连接资源，提供了更好的并发处理能力和系统稳定性。

### Driud

Druid是一个开源的数据库连接池实现。它是由阿里巴巴开发的，旨在提供高性能、高可靠性和可扩展性的数据库连接池解决方案。

Druid不仅仅是一个传统的连接池，它还提供了一些额外的功能和特性，使其成为一个全面的数据库连接管理工具。以下是Druid的一些主要特点：

1. 高性能：Druid采用了一系列的优化策略和技术，如预处理语句、连接池复用、连接池扩展等，以提供高性能的数据库连接管理和访问。
2. 数据库监控：Druid内置了一套完整的数据库监控功能，可以实时监控数据库连接的使用情况、执行SQL的性能、连接池的状态等，并提供了可视化的监控界面。
3. 防御SQL注入：Druid提供了SQL防火墙功能，可以检测和防御常见的SQL注入攻击，保护数据库的安全性。
4. 扩展性：Druid支持连接池的动态扩展和收缩，可以根据应用程序的负载情况自动调整连接池的大小，以提供更好的性能和资源利用率。
5. 配置灵活：Druid提供了丰富的配置选项，可以根据应用程序的需求进行灵活的配置，包括最大连接数、最小连接数、连接超时时间、连接验证等。
6. 多种数据源支持：Druid不仅支持传统的关系型数据库，还支持一些NoSQL数据库和消息队列等数据源，如MySQL、Oracle、PostgreSQL、SQLite、H2、Redis、Kafka等。

总的来说，Druid是一个功能强大的数据库连接池实现，提供了高性能、可靠性和可扩展性的数据库连接管理解决方案，并具备数据库监控、防御SQL注入等附加功能。它被广泛应用于Java应用程序中，特别是在高并发、大数据量的场景下，以提供高效的数据库访问能力。

使用步骤：

1. 导入`jar`包
2. 定义配置文件
3. 加载配置文件
4. 获取数据库连接池对象
5. 获取连接