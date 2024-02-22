# MyBaits

MyBatis是一个开源的持久层框架，用于将Java对象与关系型数据库之间进行映射和交互。它提供了一种简单且灵活的方式来访问数据库，并将数据库操作与Java代码解耦。

MyBatis最初是在2002年由Clinton Begin开发的，后来由Apache软件基金会接手并成为一个独立的开源项目。它的目标是提供一种优雅且高效的数据库访问解决方案，同时保持简单性和易用性。

MyBatis的核心思想是通过XML或注解来描述数据库操作，包括查询、插入、更新和删除等。开发人员可以使用简单的SQL语句或存储过程来执行数据库操作，并将结果映射到Java对象中。

以下是MyBatis的一些主要特点：

1. 灵活的SQL映射：MyBatis使用XML或注解来定义SQL映射，开发人员可以根据需要编写自定义的SQL语句，包括动态SQL和复杂的查询。

2. 对象关系映射（ORM）：MyBatis提供了强大的对象关系映射功能，可以将查询结果自动映射到Java对象中，简化了数据的处理和转换过程。

3. 缓存支持：MyBatis支持一级缓存和二级缓存，可以提高查询性能，并减少对数据库的访问次数。

4. 事务管理：MyBatis提供了事务管理的支持，可以通过配置或编程方式管理数据库事务，确保数据的一致性和完整性。

5. 插件扩展：MyBatis允许开发人员编写自定义的插件来扩展其功能，例如添加拦截器、修改SQL语句等。

总的来说，MyBatis是一个简单、灵活且功能强大的持久层框架，它提供了一种优雅的方式来访问数据库，并将数据库操作与Java代码解耦。通过使用MyBatis，开发人员可以更加高效地进行数据库操作，提高开发效率和代码质量。

## 使用步骤

1. 导入坐标

   ```xml
   <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
       <dependency>
         <groupId>org.mybatis</groupId>
         <artifactId>mybatis</artifactId>
         <version>3.5.13</version>
       </dependency>
   
       <!-- https://mvnrepository.com/artifact/com.mysql/mysql-connector-j -->
       <dependency>
         <groupId>com.mysql</groupId>
         <artifactId>mysql-connector-j</artifactId>
         <version>8.2.0</version>
       </dependency>
   ```

   

2. 编写`MyBatis`核心配置文件

   ```xml
   <!--mybatis-config.xml-->
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "https://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
       <environments default="development">
           <environment id="development">
               <transactionManager type="JDBC"/>
               <dataSource type="POOLED">
                   <property name="driver" value="com.mysql.jdbc.Driver"/>
                   <property name="url" value="jdbc:mysql:///test?useSSL=false"/>
                   <property name="username" value="root"/>
                   <property name="password" value="root"/>
               </dataSource>
           </environment>
       </environments>
       <mappers>
           <mapper resource="CustomerMapper.xml"/>
       </mappers>
   </configuration>
   ```

   

3. 编写`SQL`映射文件

   ```xml
   <!--CustormerMapper.xml-->
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="test">
       <select id="selectCustomer" resultType="com.nanak.Customer">
           SELECT cust_id, cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country FROM customers WHERE cust_id = 10001;
       </select>
   </mapper>
   ```

4. 编码

   1. 定义`POJO`类

      ```java
      package com.nanak;
      
      import java.util.Objects;
      
      public class Customer {
          public final int cust_id;
          public final String cust_name;
          public final String cust_address;
          public final String cust_city;
          public final String cust_state;
          public final String cust_zip;
          public final String cust_country;
      
          public Customer(int cust_id, String cust_name, String cust_address, String cust_city, String cust_state, String cust_zip, String cust_country) {
              this.cust_id = cust_id;
              this.cust_name = cust_name;
              this.cust_address = cust_address;
              this.cust_city = cust_city;
              this.cust_state = cust_state;
              this.cust_zip = cust_zip;
              this.cust_country = cust_country;
          }
      
          public int getCust_id() {
              return cust_id;
          }
      
          public String getCust_name() {
              return cust_name;
          }
      
          public String getCust_address() {
              return cust_address;
          }
      
          public String getCust_city() {
              return cust_city;
          }
      
          public String getCust_state() {
              return cust_state;
          }
      
          public String getCust_zip() {
              return cust_zip;
          }
      
          public String getCust_country() {
              return cust_country;
          }
      
          @Override
          public String toString() {
              return "Customer{" +
                      "cust_id=" + cust_id +
                      ", cust_name='" + cust_name + '\'' +
                      ", cust_address='" + cust_address + '\'' +
                      ", cust_city='" + cust_city + '\'' +
                      ", cust_state='" + cust_state + '\'' +
                      ", cust_zip='" + cust_zip + '\'' +
                      ", cust_country='" + cust_country + '\'' +
                      '}';
          }
      
          @Override
          public boolean equals(Object o) {
              if (this == o) return true;
              if (o == null || getClass() != o.getClass()) return false;
              Customer customer = (Customer) o;
              return cust_id == customer.cust_id && Objects.equals(cust_name, customer.cust_name) && Objects.equals(cust_address, customer.cust_address) && Objects.equals(cust_city, customer.cust_city) && Objects.equals(cust_state, customer.cust_state) && Objects.equals(cust_zip, customer.cust_zip) && Objects.equals(cust_country, customer.cust_country);
          }
      }
      ```

   2. 加载核心配置文件，获取`SqlSessionFactory`对象

      ```java
      String resource = "mybatis-config.xml";
              InputStream inputStream = Resources.getResourceAsStream(resource);
              SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
      ```

   3. 获取`SqlSession`对象，执行`SQL`语句

      ```java
      SqlSession sqlSession = sqlSessionFactory.openSession();
      Customer customer = sqlSession.selectOne("test.selectCustomer");
      ```

   4. 释放资源

      ```java
      sqlSession.close();
      ```

## `Mapper`代理开发

Mapper代理开发是MyBatis框架中的一种开发方式，用于简化数据库操作的编写和调用。在Mapper代理开发中，开发人员只需编写接口，而无需编写具体的SQL语句，MyBatis会根据接口的定义自动生成对应的SQL语句和实现。

Mapper代理开发解决了传统的SQL编写和调用的痛点，包括以下几个方面：

1. 简化SQL编写：传统的SQL编写需要手动拼接SQL语句，容易出错且不易维护。而使用Mapper代理开发，开发人员只需定义接口方法，MyBatis会根据方法的名称和参数自动生成SQL语句，大大简化了SQL的编写过程。
2. 减少重复代码：在传统的SQL编写中，相同的SQL语句可能需要在多个地方重复编写，增加了代码的冗余和维护的难度。而使用Mapper代理开发，可以将常用的SQL语句封装在接口方法中，多个地方可以直接调用该方法，避免了重复编写SQL的问题。
3. 提高可维护性：使用Mapper代理开发，将SQL语句与Java代码解耦，使代码更加清晰和可维护。开发人员可以将SQL语句集中管理，方便修改和调整，同时也降低了代码的耦合性。
4. 支持面向对象的开发：Mapper代理开发支持面向对象的开发模式，开发人员可以通过定义接口和实体类来描述数据库表和操作，更符合Java开发的习惯和思维方式。

除了解决上述痛点外，Mapper代理开发还具有以下一些优点：

- 简化了数据库操作的编写和调用，提高了开发效率。
- 避免了手动拼接SQL语句的错误和安全隐患。
- 提供了更好的代码组织和结构，易于维护和扩展。
- 支持动态SQL和复杂查询，灵活性更高。
- 可以与MyBatis的其他特性（如缓存、事务管理等）无缝集成。

总的来说，Mapper代理开发是MyBatis框架中一种简化数据库操作的方式，通过定义接口来描述数据库操作，解决了传统SQL编写和调用的痛点，提高了开发效率和代码的可维护性。

### 步骤

1. 定义与`SQL`映射文件同名的`Mapper`接口，并且将`Mapper`接口文件和SQL映射文件放置在同一目录下
2. 设置`SQL`映射文件的`namespace`属性为`Mapper`接口全限定名
3. 在`Mapper`接口中定义方法，方法名就是SQL映射文件中`sql`语句的`id`，并保持参数类型和返回值类型一致
4. 编码
   1. 通过`SqlSession`的`getMapper`方法获取`Mapper`接口的代理对象
   2. 调用对应方法完成`sql`的执行

`Tips`：如果`Mapper`接口名称和`SQL`映射文件名相同，并且在同一目录下，则可以使用包扫描的方式简化`SQL`映射文件的加载

```xml
<!--        <mapper resource="com\nanak\mapper\CustomerMapper.xml"/>-->
        <package name="com.nanak.mapper"/>
```

## `MyBaits`配置

* `environments`:配置数据库源， 修改`environments`的`defalut`属性可以完成切换

* 别名

  ```xml
  <typeAliases>
       <package name="com.nanak.mapper"/>
   </typeAliases>
  ```

注意：编写配置需要按照顺序

## `MyBaits`结果映射

MyBatis提供了灵活而强大的结果映射功能，用于将查询结果映射到Java对象中。通过结果映射，可以方便地将数据库中的数据转换为Java对象，简化了数据处理的过程。

在MyBatis中，结果映射可以通过以下两种方式实现：

1. 基于XML的结果映射：通过在XML文件中定义结果映射规则，将查询结果的列与Java对象的属性进行映射。在XML文件中，可以使用<resultMap>元素来定义结果映射，指定列名和属性名之间的映射关系。可以根据需要进行灵活的配置，包括嵌套映射、关联映射等。
2. 注解方式的结果映射：除了XML配置方式，MyBatis还支持使用注解来定义结果映射。通过在Java对象的属性上添加注解，指定与数据库列的映射关系。常用的注解包括@Results、@Result和@Column等，可以在接口或实体类中使用。

无论是XML配置方式还是注解方式，MyBatis支持以下几种常见的结果映射类型：

- 简单映射：将查询结果的列直接映射到Java对象的属性，要求列名与属性名一致或通过配置进行映射。
- 嵌套映射：将查询结果的多个表的列映射到多个Java对象中，并建立对象之间的关联关系。可以通过<association>和<collection>元素进行嵌套映射的配置。
- 关联映射：将查询结果的多个表的列映射到一个Java对象中，通过关联关系进行组装。可以通过<resultMap>元素的继承和引用来实现关联映射。

在结果映射的配置中，还可以进行一些其他的处理，如类型转换、字段别名、自动映射等。MyBatis提供了丰富的配置选项和灵活的映射规则，以满足不同的需求。

总的来说，MyBatis的结果映射功能允许将查询结果映射到Java对象中，通过XML配置或注解方式进行映射规则的定义。结果映射提供了灵活的配置选项，支持简单映射、嵌套映射和关联映射等多种映射类型，简化了数据处理的过程。

### 注解方式

在MyBatis中，使用注解方式进行结果映射可以更直接地在Java对象上定义与数据库列的映射关系，而无需使用XML进行配置。以下是一些常用的注解及其用法：

1. `@Results`：该注解用于在接口或类级别上定义多个结果映射规则。可以在`@Results`注解中使用`@Result`注解来指定具体的映射规则。

```java
@Results({
    @Result(column = "id", property = "userId"),
    @Result(column = "name", property = "userName")
})
```

2. `@Result`：该注解用于定义单个属性与列的映射关系。可以指定列名、属性名、类型处理器等。

```java
@Result(column = "id", property = "userId", jdbcType = JdbcType.INTEGER)
```

3. `@Column`：该注解用于指定属性与列的映射关系，可以用于简化`@Result`注解的配置。

```java
@Column(name = "id")
private int userId;
```

4. `@One`和`@Many`：这两个注解用于定义嵌套映射和关联映射的关系。`@One`注解用于一对一关系，`@Many`注解用于一对多关系。

```java
@One(select = "getUserAddress", fetchType = FetchType.EAGER)
private Address address;

@Many(select = "getUserOrders", fetchType = FetchType.LAZY)
private List<Order> orders;
```

在使用注解方式进行结果映射时，需要确保在MyBatis的配置文件中开启了注解的支持。可以通过在`<configuration>`标签中添加以下配置来启用注解扫描：

```xml
<configuration>
    <settings>
        <setting name="useGeneratedKeys" value="true"/>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
    <mappers>
        <mapper class="com.example.UserMapper"/>
    </mappers>
</configuration>
```

其中，`com.example.UserMapper`为包含注解映射的接口类。

使用注解方式进行结果映射可以更直观地在Java对象上定义映射关系，减少了XML配置的工作量。然而，注解方式相对于XML配置方式来说，灵活性稍弱，适用于简单的映射场景。对于复杂的映射需求，XML配置方式可能更为适合。

### XML配置

XML配置方式是MyBatis中较为传统和常用的结果映射方式，通过在XML配置文件中定义结果映射规则来将查询结果映射到Java对象中。以下是XML配置方式的一些常见配置元素和用法：

1. `<resultMap>`：该元素用于定义结果映射规则，指定查询结果的列与Java对象的属性之间的映射关系。可以在`<resultMap>`元素中使用`<id>`、`<result>`和`<association>`等子元素来指定具体的映射规则。

```xml
<resultMap id="userResultMap" type="com.example.User">
    <id column="id" property="userId"/>
    <result column="name" property="userName"/>
</resultMap>
```

2. `<id>`：该元素用于定义主键列的映射规则。

```xml
<id column="id" property="userId"/>
```

3. `<result>`：该元素用于定义普通属性列的映射规则。

```xml
<result column="name" property="userName"/>
```

4. `<association>`：该元素用于定义嵌套映射规则，将查询结果的多个表的列映射到多个Java对象中，并建立对象之间的关联关系。

```xml
<association property="address" resultMap="addressResultMap"/>
```

5. `<collection>`：该元素用于定义一对多关系的映射规则，将查询结果的多个表的列映射到一个Java对象中，并通过集合属性来关联多个子对象。

```xml
<collection property="orders" resultMap="orderResultMap"/>
```

6. `<discriminator>`：该元素用于定义鉴别器映射规则，根据指定的列值来决定使用哪个子结果映射规则。

```xml
<discriminator javaType="java.lang.Integer" column="type">
    <case value="1" resultMap="resultMap1"/>
    <case value="2" resultMap="resultMap2"/>
</discriminator>
```

在XML配置方式中，可以通过`<resultMap>`元素的继承和引用来实现结果映射的复用和组合。可以使用`<resultMap>`元素的`extends`属性来继承其他结果映射，使用`<resultMap>`元素的`refid`属性来引用其他结果映射。

```xml
<resultMap id="userResultMap" type="com.example.User" extends="baseResultMap">
    <!-- Additional mapping configurations -->
</resultMap>
```

在SQL映射语句中，可以通过`resultMap`属性引用定义好的结果映射规则。

```xml
<select id="getUser" resultMap="userResultMap">
    SELECT id, name FROM user WHERE id = #{userId}
</select>
```

XML配置方式相对于注解方式来说，灵活性更高，适用于复杂的映射场景。可以通过组合和继承来实现复杂的嵌套映射和关联映射。同时，XML配置方式也提供了更多的配置选项，如类型转换、字段别名、自动映射等。

## 参数占位符

在MyBatis中，可以使用参数占位符来传递参数值给SQL语句。参数占位符的使用方式与传参方式有关，以下是几种常见的传参方式及其对应的参数占位符的使用：

1. 位置占位符（?）：使用位置占位符时，参数值的顺序与SQL语句中占位符的位置一一对应。

```java
@Select("SELECT * FROM user WHERE id = ? AND name = ?")
User getUser(int userId, String userName);
```

2. 命名占位符（#{paramName}）：使用命名占位符时，参数值通过参数名进行传递，可以在SQL语句中使用参数名作为占位符。

```java
@Select("SELECT * FROM user WHERE id = #{userId} AND name = #{userName}")
User getUser(@Param("userId") int userId, @Param("userName") String userName);
```

3. Map传参：可以将参数值封装到一个Map对象中，通过键值对的方式传递参数。

```java
@Select("SELECT * FROM user WHERE id = #{userId} AND name = #{userName}")
User getUser(Map<String, Object> paramMap);
```

在XML配置文件中，使用参数占位符的方式与注解方式类似。可以在SQL语句中使用`#{paramName}`来引用参数值，其中`paramName`为传递参数时的参数名。

```xml
<select id="getUser" resultType="com.example.User">
    SELECT * FROM user WHERE id = #{userId} AND name = #{userName}
</select>
```

对于位置占位符和命名占位符，可以在SQL语句中使用`?`和`#{paramName}`来表示占位符，参数值的传递顺序和参数名需要与方法参数一致。

对于Map传参，可以通过键值对的方式将参数值放入Map对象中，并在SQL语句中使用`#{paramName}`来引用参数值。

需要注意的是，使用命名占位符和Map传参时，需要在方法参数上使用`@Param`注解或在Map中使用相应的键名来指定参数名，以便正确地匹配参数值和占位符。

以上是常见的参数占位符和传参方式，你可以根据具体的需求选择合适的方式来传递参数给MyBatis的SQL语句。

## 动态SQL

动态SQL是MyBatis中一个强大的特性，它允许根据不同的条件动态生成SQL语句，以满足不同的查询需求。MyBatis提供了一些标签和表达式来实现动态SQL，以下是一些常用的动态SQL技术：

1. `<if>`标签：用于条件判断，根据条件决定是否包含某段SQL语句。

```xml
<select id="getUser" resultType="com.example.User">
  SELECT * FROM user
  <where>
    <if test="userId != null">
      AND id = #{userId}
    </if>
    <if test="userName != null">
      AND name = #{userName}
    </if>
  </where>
</select>
```

2. `<choose>`, `<when>`, `<otherwise>`标签：用于实现类似于Java中的`switch`语句的逻辑判断。

```xml
<select id="getUser" resultType="com.example.User">
  SELECT * FROM user
  <where>
    <choose>
      <when test="userId != null">
        AND id = #{userId}
      </when>
      <when test="userName != null">
        AND name = #{userName}
      </when>
      <otherwise>
        AND status = 1
      </otherwise>
    </choose>
  </where>
</select>
```

3. `<trim>`, `<set>`, `<where>`标签：用于处理SQL语句的前缀、后缀以及中间的空白字符，可以根据条件动态添加或删除这些字符。

```xml
<update id="updateUser" parameterType="com.example.User">
  UPDATE user
  <set>
    <if test="userName != null">
      name = #{userName},
    </if>
    <if test="email != null">
      email = #{email},
    </if>
  </set>
  WHERE id = #{userId}
</update>
```

4. `<foreach>`标签：用于循环遍历集合或数组，生成重复的SQL片段。

```xml
<select id="getUsersByIds" resultType="com.example.User">
  SELECT * FROM user
  WHERE id IN
  <foreach collection="userIds" item="userId" open="(" separator="," close=")">
    #{userId}
  </foreach>
</select>
```

5. `<sql>`标签：用于定义可重用的SQL片段，可以在其他SQL语句中引用。

```xml
<sql id="userColumns">
  id, name, email
</sql>

<select id="getUser" resultType="com.example.User">
  SELECT <include refid="userColumns" /> FROM user WHERE id = #{userId}
</select>
```

通过使用这些动态SQL技术，你可以根据不同的查询条件来动态生成SQL语句，从而实现更灵活和可复用的查询逻辑。这些标签和表达式可以根据具体的需求进行组合和嵌套，以满足复杂的动态SQL需求。

## 单例工具类

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MyBatisUtil {
    private static SqlSessionFactory sqlSessionFactory;
    private static final String CONFIG_FILE = "mybatis-config.xml";

    private MyBatisUtil() {
    }

    public static SqlSession getSqlSession() {
        if (sqlSessionFactory == null) {
            synchronized (MyBatisUtil.class) {
                if (sqlSessionFactory == null) {
                    try {
                        InputStream inputStream = Resources.getResourceAsStream(CONFIG_FILE);
                        sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
        return sqlSessionFactory.openSession();
    }
}

```

