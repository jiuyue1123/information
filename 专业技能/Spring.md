# 核心概念

## IOC

IOC代表"控制反转"（Inversion of Control），是一种软件设计模式，用于解决软件组件之间的依赖关系管理问题。

传统的软件开发中，组件之间的依赖关系通常是硬编码在代码中的。这种紧耦合的设计使得组件难以独立测试、重用和替换。当一个组件需要使用另一个组件时，它必须直接创建或获取依赖的实例。这种紧耦合会导致代码的复杂性增加，可维护性降低。

IOC的目标是通过反转依赖关系的创建和管理，解决这些痛点。它提供了一种机制，使得组件不再负责自己的依赖关系的创建和管理，而是将这些责任委托给外部容器或框架。

IOC的主要优势包括：

1. 松耦合：IOC通过解耦组件之间的依赖关系，使得它们能够独立开发、测试和维护。组件只需要定义自己的接口，而不需要关心依赖的具体实现。

2. 可测试性：IOC使得组件的依赖关系可以通过模拟或替代实现进行测试。测试时可以轻松地替换真实的依赖对象，以便进行单元测试或集成测试。

3. 可扩展性：IOC使得组件的依赖关系可以在运行时进行配置和更改。通过配置依赖关系，可以轻松地替换、添加或删除组件，从而实现系统的灵活性和可扩展性。

4. 代码重用：通过将依赖关系的创建和管理委托给外部容器，可以实现组件的可重用性。多个组件可以共享同一个依赖对象，避免了重复创建和管理的工作。

总之，IOC通过解耦组件之间的依赖关系，提高了代码的灵活性、可测试性和可维护性，从而改善了软件开发过程中的痛点。

### `Spring`实现`IoC`

`Spring`技术对`IoC`思想进行了实现

* `Spring`提供一个容器，称为`IoC`容器，用来充当`IoC`思想中的"外部"
* `Ioc`容器负责对象的创建、初始化等一系列工作，被创建或管理的对象在`Ioc`中统称为`Bean`

### DI

DI代表"依赖注入"（Dependency Injection），是一种实现IOC（控制反转）的具体技术。

依赖注入是一种设计模式，用于解耦组件之间的依赖关系。它通过将一个对象的依赖关系从该对象本身移除，而是由外部容器负责创建和注入依赖对象，从而实现了控制反转。

在依赖注入中，一个组件（被称为依赖的消费者）不再负责创建或获取它所依赖的对象（被称为依赖的提供者）。相反，依赖的提供者由外部容器负责创建，并通过构造函数、方法参数或属性注入到依赖的消费者中。

依赖注入的主要目的是减少组件之间的耦合度，提高代码的可测试性和可维护性。它使得组件的依赖关系变得可配置，可以在运行时动态地注入不同的依赖对象，而不需要修改组件的代码。

依赖注入有以下几种常见的实现方式：

1. 构造函数注入（Constructor Injection）：依赖对象通过组件的构造函数参数传递进来。

2. 方法注入（Method Injection）：依赖对象通过组件的方法参数传递进来。

3. 属性注入（Property Injection）：依赖对象通过组件的属性进行注入。

依赖注入框架（如Spring Framework、Dagger、Guice等）可以自动管理组件之间的依赖关系，根据配置信息自动创建和注入依赖对象。

总之，依赖注入是一种实现IOC的技术，通过将对象的依赖关系交由外部容器管理，提高了代码的灵活性、可测试性和可维护性。

# Bean

在软件开发中，"Bean"是指在IOC容器中管理的一个对象实例。Bean是IOC容器中的基本构建块，它代表了一个特定的组件或对象。

在Java中，Bean通常是一个普通的Java类，但它需要符合一些特定的规范，以便能够被IOC容器管理。这些规范包括：

1. 无参构造函数：Bean类必须有一个无参构造函数，以便IOC容器能够实例化它。

2. 属性和对应的getter/setter方法：Bean类通常包含一些属性，并提供相应的getter和setter方法，以便IOC容器可以通过这些方法来设置和获取属性的值。

3. 可选的生命周期回调方法：Bean类可以实现一些特定的接口（如InitializingBean和DisposableBean），以提供初始化和销毁的生命周期回调方法。

常见的配置方式有以下几种：

1. XML配置：使用XML文件来配置Bean的定义和依赖关系。在XML配置中，可以定义Bean的名称、类名、属性值以及依赖关系等信息。

2. 注解配置：使用注解来标记Bean的定义和依赖关系。常用的注解包括@Component、@Service、@Repository、@Controller等。通过注解配置，可以将Bean的定义直接放在Java类中，而不需要额外的XML配置文件。

3. Java配置：使用Java代码来配置Bean的定义和依赖关系。通常使用@Configuration注解标记一个类，并在该类中使用@Bean注解来定义Bean的创建和依赖关系。

这些配置方式可以单独使用，也可以结合使用。例如，可以使用XML配置来定义Bean的基本信息，然后使用注解或Java配置来定义Bean的依赖关系。

总之，Bean是IOC容器中管理的对象实例，它需要符合一些规范以便能够被IOC容器管理。常见的配置方式包括XML配置、注解配置和Java配置。这些配置方式可以根据具体需求选择使用。

## Bean四种实例化方式

![image-20231106203422636](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231106203422636.png)

## 使用工厂`Bean`实例化

```java
package com.nanak.factory;

import com.nanak.dao.BookDao;
import org.springframework.beans.factory.FactoryBean;


public class BookDaoFactoryBean implements FactoryBean<BookDao> {
    private String msg;
    @Override
    public BookDao getObject() throws Exception {
        System.out.println(msg);
        return new BookDao();
    }

    @Override
    public Class<?> getObjectType() {
        return BookDao.class;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bookDao" class="com.nanak.factory.BookDaoFactoryBean">
        <property name="msg" value="hello-world"/>
    </bean>



</beans>
```

## Bean的生命周期

在IOC容器中，Bean的生命周期可以分为以下几个阶段：

1. 实例化（Instantiation）：在这个阶段，IOC容器会根据Bean的定义创建Bean的实例。通常情况下，IOC容器会使用Java的反射机制调用Bean的无参构造函数来实例化对象。

2. 属性赋值（Property Assignment）：在实例化后，IOC容器会根据配置信息或注解，将Bean的属性值设置到相应的属性上。这可以通过调用Bean的setter方法或直接设置属性值来完成。

3. 初始化（Initialization）：在属性赋值完成后，如果Bean实现了特定的接口（如InitializingBean），IOC容器会调用相应的初始化方法来完成Bean的初始化操作。此时，Bean已经准备好被使用了。

4. 使用（In Use）：在初始化完成后，Bean可以被其他组件使用。它可以被注入到其他Bean中，或者通过IOC容器的getBean()方法获取。

5. 销毁（Destruction）：当IOC容器关闭或销毁时，如果Bean实现了特定的接口（如DisposableBean），IOC容器会调用相应的销毁方法来完成Bean的清理操作。在销毁阶段，可以释放资源、关闭连接等。

需要注意的是，不同的IOC容器和框架可能有不同的生命周期管理机制。例如，Spring框架提供了更细粒度的生命周期管理，可以通过配置方法、注解或接口来定义初始化和销毁的回调方法。

总之，Bean的生命周期包括实例化、属性赋值、初始化、使用和销毁几个阶段。通过合理配置和使用IOC容器，可以灵活地管理Bean的生命周期，确保Bean在需要时被正确地创建、初始化、使用和销毁。

![image-20231106210502588](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231106210502588.png)

![image-20231106210526791](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231106210526791.png)

![image-20231106210639493](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231106210639493.png)

# 依赖注入方式

## `setter`注入——引用类型

在Bean中定义引用类型属性并提供可访问的set方法

```java
package com.nanak.dao;

public class UserDao {
    private BookDao bookDao;
    public void save() {
        System.out.println("UserDao save");
    }

    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
}

```



配置中使用property标签ref属性引入注入引用类型对象

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bookDao" class="com.nanak.factory.BookDaoFactoryBean">
        <property name="msg" value="hello-world"/>
    </bean>

    <bean id="userBook" class="com.nanak.dao.UserDao">
        <property name="bookDao" ref="bookDao"/>
    </bean>



</beans>
```

## `setter`注入——基础类型

在Bean中定义引用类型属性并提供可访问的set方法

```java
package com.nanak.dao;

public class UserDao {
    private String bookDao;
    public void save() {
        System.out.println("UserDao save");
    }

    public void setBookDao(String bookDao) {
        this.bookDao = bookDao;
    }
}

```

使用`property`标签`value`属性注入简单类型数据

## 构造器注入——引用类型

在Bean中定义引用类型属性并提供可访问的构造方法

```java
package com.nanak.dao;

public class UserDao {
    private BookDao bookDao;
    public void save() {
        System.out.println("UserDao save");
    }

    public UserDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
}

```

使用`constructor-arg`标签`ref`属性注入引用类型数据

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bookDao" class="com.nanak.factory.BookDaoFactoryBean">
        <property name="msg" value="hello-world"/>
    </bean>

    <bean id="userBook" class="com.nanak.dao.UserDao">
        <constructor-arg name="bookDao" ref="bookDao"/>
    </bean>



</beans>
```



## 构造器注入——基础类型

在Bean中定义引用类型属性并提供可访问的构造方法

```java
package com.nanak.dao;

public class UserDao {
    private String bookDao;
    public void save() {
        System.out.println("UserDao save");
    }

    public UserDao(String bookDao) {
        this.bookDao = bookDao;
    }
}

```

使用`constructor-arg`标签`value`属性注入基本类型数据

![image-20231109112829450](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231109112829450.png)

## 依赖自动装配

* 按名称

  `<bean id="userBook" class="com.nanak.dao.UserDao" autowire="byName"/>`

* 按类型

  `<bean id="userBook" class="com.nanak.dao.UserDao" autowire="byType"/>`

![image-20231109113551940](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231109113551940.png)

## 集合注入

```java
package com.nanak.dao;

import java.util.List;
import java.util.Map;
import java.util.Properties;
import java.util.Set;

public class UserDao {
    private BookDao bookDao;

    private int[] array;
    private List<String> list;
    private Set<String> set;
    private Map<String, String> map;
    private Properties properties;
    public void save() {
        System.out.println("UserDao save");
    }

    public UserDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }

    public void setArray(int[] array) {
        this.array = array;
    }

    public void setList(List<String> list) {
        this.list = list;
    }

    public void setSet(Set<String> set) {
        this.set = set;
    }

    public void setMap(Map<String, String> map) {
        this.map = map;
    }

    public void setProperties(Properties properties) {
        this.properties = properties;
    }

    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bookDao" class="com.nanak.factory.BookDaoFactoryBean">
        <property name="msg" value="hello-world"/>
    </bean>

    <bean id="userBook" class="com.nanak.dao.UserDao">
        <property name="bookDao" ref="bookDao"/>
        <property name="array">
            <array>
                <value>1</value>
                <value>2</value>
                <value>3</value>
            </array>
        </property>
        <property name="list">
            <list>
                <value>1</value>
                <value>2</value>
                <value>3</value>
            </list>
        </property>
        <property name="set">
            <set>
                <value>1</value>
                <value>2</value>
                <value>3</value>
                <value>3</value>
<!--                自动去重-->
            </set>
        </property>
        <property name="map">
            <map>
                <entry key="1" value="1"></entry>
                <entry key="2" value="2"></entry>
                <entry key="3" value="3"></entry>
            </map>
        </property>
        <property name="properties">
            <props>
                <prop key="1">1</prop>
                <prop key="2">2</prop>
                <prop key="3">3</prop>
            </props>
        </property>
    </bean>
</beans>
```

# 加载`properties`文件

1. 开启`context`命名空间

2. 使用`context`空间加载`properties`

3. 使用属性占位符`${}`读取属性

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:context="http://www.springframework.org/schema/context"
   
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="
          http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd
   ">
   
       <context:property-placeholder location="classpath*:*.properties" system-properties-mode="NEVER"/>
   
       <bean id="bookDao" class="com.nanak.factory.BookDaoFactoryBean">
           <property name="msg" value="hello-world"/>
       </bean>
   
       <bean id="userDao" class="com.nanak.dao.UserDao">
           <property name="bookDao" ref="bookDao"/>
           <property name="msg" value="${msg}"/>
       </bean>
   
   </beans>
   ```

   ![image-20231109121051304](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231109121051304.png)

   ```xml
   ```

# 注解开发

   ## 使用注解开发定义bean

   1. 使用@Component定义bean

      ```java
      package com.nanak.dao;
      
      import org.springframework.stereotype.Component;
      
      @Component("bookBao")
      public class BookDao {
          public void save() {
              System.out.println("BookDao...");
          }
      }
      ```

   2. 核心配置文件中通过组件扫描加载bean

      ```xml
      <context:component-scan base-package="com.nanak"/>
      ```

   ![image-20231109163954044](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231109163954044.png)

## 使用纯注解开发模式

Spring3.0中开启了纯注解开发模式，使用Java类代替配置文件，开启了Spring快速开发赛道

Java类代替了Spring核心配置文件

```java
package com.nanak.spring;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("com.nanak")
public class SpringConfig {
}

```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"

       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
">

    <context:component-scan base-package="com.nanak"/>

</beans>
```

加载配置文件初始化容器发生改变

```java
package com.nanak;
import com.nanak.dao.BookDao;
import com.nanak.spring.SpringConfig;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
/**
 * Hello world!
 *
 */
public class App {
    public static void main(String[] args) {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);

        BookDao bean = ctx.getBean(BookDao.class);
        bean.save();
    }
}

```

* `@Scope`用来定义是否单例
* `@PostConstruct`和`@PreDestory`用来定义生命周期函数

注意：使用以上注解需添加以下依赖

```xml
<dependency>
      <groupId>javax.annotation</groupId>
      <artifactId>javax.annotation-api</artifactId>
      <version>1.3.2</version>
</dependency>
```

## 使用注解依赖注入

```java
package com.nanak.service;

import com.nanak.dao.BookDao;
import org.springframework.stereotype.Service;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;


@Service("bookService")
public class BookService {
    private BookDao bookDao;

    @PostConstruct
    public void init() {
        System.out.println("bookService init");
    }

    @PreDestroy
    public void destroy() {
        System.out.println("bookService destroy");
    }

    public void save() {
        bookDao.save();
    }
}

```

注意：自动装配可以不提供`setter`

使用`@Qualifier`注解开启指定名称装配bean，比如配合`@Autowired`注解使用

使用`@Value`注解提供基本类型依赖注入

在配置类使用`@PropertySource("classpath:msg.properties")`可以加载属性文件，不支持通配符

## 管理第三方Bean

![image-20231109175543951](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231109175543951.png)

![image-20231109175818840](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231109175818840.png)

# 整合MyBatis

1. 写好pom文件

   ```xml
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
     <modelVersion>4.0.0</modelVersion>
   
     <groupId>com.nanak</groupId>
     <artifactId>Spring</artifactId>
     <version>1.0-SNAPSHOT</version>
     <packaging>jar</packaging>
   
     <name>Spring</name>
     <url>http://maven.apache.org</url>
   
     <properties>
       <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
     </properties>
   
     <dependencies>
       <dependency>
         <groupId>junit</groupId>
         <artifactId>junit</artifactId>
         <version>3.8.1</version>
         <scope>test</scope>
       </dependency>
   
       <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-context</artifactId>
         <version>5.3.20</version>
       </dependency>
   
       <dependency>
         <groupId>javax.annotation</groupId>
         <artifactId>javax.annotation-api</artifactId>
         <version>1.3.2</version>
       </dependency>
   
         <dependency>
             <groupId>com.mysql</groupId>
             <artifactId>mysql-connector-j</artifactId>
             <version>8.2.0</version>
         </dependency>
   
         <dependency>
             <groupId>org.springframework</groupId>
             <artifactId>spring-jdbc</artifactId>
             <version>5.3.20</version>
         </dependency>
   
         <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
         <dependency>
             <groupId>org.mybatis</groupId>
             <artifactId>mybatis-spring</artifactId>
             <version>3.0.2</version>
         </dependency>
   
         <dependency>
             <groupId>org.mybatis</groupId>
             <artifactId>mybatis</artifactId>
             <version>3.5.13</version>
         </dependency>
   
   
     </dependencies>
   </project>
   
   ```

2. 配置数据源：在Spring的配置文件中，配置数据源以供MyBatis使用。你可以使用Spring提供的数据源实现，如`BasicDataSource`、`HikariDataSource`等。以下是一个使用`HikariDataSource`的示例：

   ```java
   @Bean
   public DataSource dataSource() {
       HikariDataSource dataSource = new HikariDataSource();
       dataSource.setDriverClassName("com.mysql.jdbc.Driver");
       dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/mydatabase");
       dataSource.setUsername("your-username");
       dataSource.setPassword("your-password");
       return dataSource;
   }
   ```

   这里使用了`@Bean`注解将数据源配置为Spring的Bean。

3. 配置SqlSessionFactoryBean：使用`@MapperScan`注解扫描Mapper接口，并配置`SqlSessionFactoryBean`。`SqlSessionFactoryBean`将作为MyBatis的`SqlSessionFactory`实例。以下是一个示例配置：

   ```java
   @Configuration
   @MapperScan("com.example.dao")
   public class MyBatisConfig {
       @Autowired
       private DataSource dataSource;
   
       @Bean
       public SqlSessionFactoryBean sqlSessionFactoryBean() throws Exception {
           SqlSessionFactoryBean sessionFactoryBean = new SqlSessionFactoryBean();
           sessionFactoryBean.setDataSource(dataSource);
           return sessionFactoryBean;
       }
   }
   ```

   这里使用了`@Configuration`注解将类标记为Spring的配置类，`@MapperScan`注解指定了要扫描的Mapper接口的包名。

4. 编写Mapper接口和映射文件：与之前相同，在指定的包下编写Mapper接口和对应的映射文件。Mapper接口使用`@Mapper`注解进行标记，映射文件中的SQL语句使用注解进行定义。以下是一个示例：

   ```java
   @Mapper
   public interface UserMapper {
       @Select("SELECT * FROM users WHERE id = #{id}")
       User getUserById(int id);
   }
   ```

   这里使用了`@Mapper`注解将接口标记为MyBatis的Mapper，`@Select`注解定义了SQL查询语句。

5. 使用MyBatis的Mapper：在你的代码中，使用注入的Mapper接口进行数据库操作。你可以通过`@Autowired`或`@Resource`注解将Mapper接口注入到你的服务类中，然后调用其中的方法进行数据库操作。以下是一个示例：

   ```java
   @Service
   public class UserService {
       @Autowired
       private UserMapper userMapper;
   
       public User getUserById(int id) {
           return userMapper.getUserById(id);
       }
   }
   ```

   这里的`UserMapper`是你编写的Mapper接口，通过`@Autowired`注解将其注入到`UserService`中。

以上是使用注解模式整合Spring和MyBatis的基本步骤。你可以根据实际需求进行适当的调整和扩展。

# 整合JUint

# AOP

AOP（Aspect-Oriented Programming）是一种编程范式，用于通过将横切关注点（cross-cutting concerns）从核心业务逻辑中分离出来，实现对系统的模块化和解耦。

在传统的面向对象编程中，我们将功能按照业务逻辑划分到不同的类中，但有些功能在多个类中都会出现，例如日志记录、事务管理、安全检查等。这些功能被称为横切关注点，它们无法通过类的继承或组合来有效地复用，而是散布在各个类中，导致代码重复、可维护性差、耦合度高等问题。

AOP通过将横切关注点从核心业务逻辑中分离出来，实现了以下几个方面的优势：

1. 模块化：AOP允许我们将横切关注点封装为独立的模块，称为切面（Aspect），它可以跨越多个类和对象。这样，我们可以将关注点的实现逻辑集中在一个地方，提高代码的可维护性和可重用性。

2. 解耦：AOP通过将横切关注点与核心业务逻辑分离，减少了它们之间的耦合。核心业务逻辑不再关心关注点的具体实现，而是通过AOP容器在运行时动态地将切面织入到目标对象中。这样，业务逻辑和关注点可以独立地进行开发和演化，提高了系统的灵活性和可扩展性。

3. 代码重用：AOP可以将横切关注点应用到多个类和对象上，避免了重复编写相同的代码。例如，可以通过切面实现日志记录功能，然后将该切面应用到多个类中，避免了在每个类中都编写日志记录的代码。

4. 提高系统可维护性：将横切关注点从核心业务逻辑中分离出来，使得核心业务逻辑更加清晰、简洁。同时，由于关注点的实现逻辑集中在切面中，可以更方便地修改和维护关注点的功能。

总之，AOP通过将横切关注点从核心业务逻辑中解耦出来，实现了代码的模块化、解耦、重用和提高系统可维护性的优势。这使得开发人员可以更专注于核心业务逻辑的实现，同时在需要时方便地应用和修改关注点的功能。

在AOP中，有一些关键概念需要理解，包括连接点（Join Point）、通知（Advice）、通知类（Advice Types）、切面（Aspect）和切入点（Pointcut）。

1. 连接点（Join Point）：连接点是在应用程序执行过程中可以插入切面的点。它代表着应用程序中的一个特定位置，例如方法的调用、方法的执行、异常的抛出等。连接点是AOP中可以被拦截的特定点。

2. 通知（Advice）：通知是在连接点上执行的代码。它表示切面在连接点上执行的特定动作。常见的通知类型包括前置通知（Before Advice）、后置通知（After Advice）、返回通知（After Returning Advice）、异常通知（After Throwing Advice）和环绕通知（Around Advice）。

3. 通知类型（Advice Types）：通知类型是指根据通知类型对通知进行分类的方式。不同的通知类型定义了在连接点上执行的具体动作。例如，前置通知在连接点之前执行，后置通知在连接点之后执行，返回通知在方法返回之后执行，异常通知在方法抛出异常时执行，环绕通知可以在连接点前后执行。

4. 切面（Aspect）：切面是由切入点和通知组成的。切入点定义了在哪些连接点上应用通知，而通知定义了在连接点上执行的具体动作。切面是将横切关注点模块化的方式，它将关注点的功能封装为可重用的模块。

5. 切入点（Pointcut）：切入点是用于定义在哪些连接点上应用通知的表达式。它可以通过指定类、方法、注解等来选择连接点。切入点定义了切面在哪些连接点上起作用。

简而言之，连接点是AOP中可以被拦截的特定点，通知是在连接点上执行的代码，通知类是根据通知类型对通知进行分类，切面是由切入点和通知组成的，切入点定义了在哪些连接点上应用通知。这些概念共同构成了AOP的核心元素，用于实现对横切关注点的处理。

## AOP入门案例

任务：在接口执行前输出当前系统时间

开发模式：注解

步骤：

* 导入坐标

  ```xml
  <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>6.0.9</version>
      </dependency>
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>6.0.9</version>
      </dependency>
  ```

  

* 制作连接点方法(原始操作)

* 制作共性功能(通知)

* 定义切入点

* 绑定切入点与通知关系(切面)

```java
@Component
@Aspect

public class MyAdvice {
    @Pointcut("execution(void com.nanak.dao.UserDao.save())")
    private void pt() {}

    @Before("pt()")
    public void before() {
        System.out.println(System.currentTimeMillis());
    }
```

```java
package com.nanak.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
import org.springframework.stereotype.Component;

@Component
@ComponentScan("com.nanak")
@EnableAspectJAutoProxy
public class SpringConfig {
}
```

## AOP通知类型

```java
package com.nanak.advice;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
import org.springframework.stereotype.Component;

@Component
@Aspect

public class MyAdvice {
    @Pointcut("execution(void com.nanak.dao.UserDao.save())")
    private void pt() {}

    @Before("pt()")
    public void before() {
        System.out.println("Before");
    }
    @After("pt()")
    public void after() {
        System.out.println("After");
    }

    @Around("pt()")
    public void around(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("Around before");
        pjp.proceed();
        System.out.println("Around after");
    }

    @AfterReturning("pt()")
    public void afterReturning() {
        System.out.println("AfterReturning");
    }
    @AfterThrowing("pt()")
    public void afterThrowing() {
        System.out.println("AfterThrowing");
    }



}

```

运行结果：

```shell
Around before
Before
UserDao.save()
AfterReturning
After
Around after
```

## AOP通知获取数据

* 获取切入点方法的参数

  可以使用`JoinPoint`对象获取参数,`ProceedJointPoint`适用于环绕通知

  ```java
  	@Before("servicePt()")
      public void before(JoinPoint jp) {
          System.out.println(Arrays.toString(jp.getArgs()));
      }
      
      @After("servicePt()")
      public void after(JoinPoint jp) {
          System.out.println(Arrays.toString(jp.getArgs()));
      }
  ```

* 获取切入点方法返回值

  

  * 返回后通知

    使用`returning`可以获取返回值

    ```java
    @AfterReturning(value = "servicePt()", returning = "result")
        public void afterReturning(JoinPoint jp, Object result) { // 参数顺序不能替换
            System.out.println(Arrays.toString(jp.getArgs()));
        }
    ```

  * 环绕通知

* 获取切入点方法运行异常信息

  使用`throwing`可以获取异常信息

  * 抛出异常后通知

    ```java
    @AfterThrowing(value = "servicePt()", throwing = "e")
        public void afterThrowing(JoinPoint jp, Exception e) {
            System.out.println(Arrays.toString(jp.getArgs()));
        }
    ```

    

  * 环绕通知

# Spring事务

1. 配置事务管理器：首先需要配置一个事务管理器，用于管理和控制事务。Spring提供了多个事务管理器的实现，例如`DataSourceTransactionManager`（用于JDBC事务管理）、`HibernateTransactionManager`（用于Hibernate事务管理）等。你可以根据具体的需求选择适合的事务管理器，并将其配置到Spring的配置文件中。
2. 配置事务属性：在配置文件中，你可以指定事务的属性，例如事务的传播行为、隔离级别、回滚规则等。这些属性将决定事务的行为和特性。你可以使用注解或XML配置来定义事务属性。
3. 声明事务边界：在需要开启事务的方法上添加事务注解或通过XML配置进行声明。常用的事务注解是`@Transactional`，它可以应用在方法级别或类级别上。通过在方法或类上添加`@Transactional`注解，你可以告诉Spring该方法或类需要在事务的管理下执行。
4. 开启事务：当调用被标记为事务的方法时，Spring会根据事务的属性和传播行为来开启一个事务。事务管理器将会在方法执行之前创建一个事务，并将其与当前线程关联起来,在Spring配置中`@EnableTransactionManagement`
5. 提交或回滚事务：当方法执行完成后，事务管理器将根据方法的执行结果决定是提交事务还是回滚事务。如果方法成功执行并没有抛出异常，事务管理器将提交事务，使得数据库操作生效。如果方法抛出了异常，事务管理器将回滚事务，撤销之前的数据库操作。

![image-20231112201052802](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231112201052802.png)

# `SpringMVC`

## 入门案例

1. 导入`pom`依赖
2. 创建`SpringMVC`控制器类(等同于Servlet功能)
3. 初始化SpringMVC环境(同Spring环境)，设定SpringMVC加载对应Bean
4. 初始化Servlet容器，加载SpringMVC环境，并设置SpringMVC技术处理的请求

注解：

* @Controller
* @RequestMapping
* @RequestBody

# Spring整合SpringMVC

## 乱码处理

```java
package com.nanak.config;

import jakarta.servlet.Filter;
import org.springframework.web.filter.CharacterEncodingFilter;
import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

public class ServletContainerInitConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMVCConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    // 乱码处理
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter filter = new CharacterEncodingFilter();
        filter.setEncoding("UTF-8");
        return new Filter[]{filter};
    }
}

```

## Restful风格的Controller开发

```java
package com.nanak.controller;

import com.nanak.domain.User;
import com.nanak.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;


@RestController
@RequestMapping("/users")
public class UserController {
    @Autowired
    private UserService userService;

    // 根据Id查询用户
    @GetMapping("/{id}")
    public User getUserById(@PathVariable int id) {
        return userService.getUserById(id);
    }
    // 添加用户
    @PostMapping
    public int addUser(@RequestBody User user) {
        return userService.addUser(user);
    }
    // 更新用户
    @PutMapping
    public int updateUser(@RequestBody User user) {
        return userService.updateUser(user);
    }
    // 删除用户
    @DeleteMapping("/{id}")
    public boolean deleteUser(@PathVariable int id) {
        return userService.deleteUser(id);
    }
}

```

## 使用统一数据返回结果类

```java
public class Reslut {
    private Object data;
    private int code;
    private String msg;
}
```

## 异常处理

![image-20231113125524180](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20231113125524180.png)

* 各个层级均出现异常，异常处理代理书写在哪一层？（表现层）

SpringMVC提供了异常处理器

```java
@RestControllerAdvice
public class ProjectExceptionAdvice {
    @ExceptionHandler(Exception.class)
    public Result doException(Exception e) {
        return new Result(500, null, "异常");
    }
}
```

### 异常分类及处理方案

* 业务异常(BusinessException)

  * 发送对应消息传递给用户，提醒规范操作

* 系统异常(SystemException)

  * 发送固定消息传递给用户，安抚用户
  * 发送消息给运维，提醒维护
  * 记录日志

* 其他异常(Exception)

  * 发送固定消息传递给用户，安抚用户
  * 发送消息给编程人员，提醒维护
  * 记录日志

  