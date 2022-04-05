<h1 align='center'>mybatis</h1>



## 一. 自学mybatis的第一天

### 1. 建立一个普通的maven项目（使工具为idea）

#### 1.1 打开idea -- > File --> new --> Project 

![image-20210115144805956](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5CJava%E5%8D%9A%E5%AE%A2%5Cmybatis.assets%5Cimage-20210115144805956.png)

#### 1.2 选择maven项目，JDK环境使用1.8，然后直接next

![image-20210115145022428](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5CJava%E5%8D%9A%E5%AE%A2%5Cmybatis.assets%5Cimage-20210115145022428.png)

#### 1.3 更改对应的工程名，分组等。然后点finish。一个普通的maven项目就建立完成了。

![image-20210115145046930](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5CJava%E5%8D%9A%E5%AE%A2%5Cmybatis.assets%5Cimage-20210115145046930.png)

#### 1.4 删除工程下的src目录(文件夹). 我们这里想使用父子maven工程。当然不需要的可以不需要删除。省略这一步

![image-20210115145310457](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5CJava%E5%8D%9A%E5%AE%A2%5Cmybatis.assets%5Cimage-20210115145310457.png)

#### 1.5 建立一个maven模块

点击刚刚新建的maven项目，然后右键单击，新建一个模块

![image-20210115145358267](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5CJava%E5%8D%9A%E5%AE%A2%5Cmybatis.assets%5Cimage-20210115145358267.png)

这里我们依旧建立一个普通的maven模块，环境为JDK1.8，直接next

![image-20210115145507464](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5CJava%E5%8D%9A%E5%AE%A2%5Cmybatis.assets%5Cimage-20210115145507464.png)

parent指定为第一次建立的maven工程。然后更改自己要建立模块的名称等。

![image-20210115145541045](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5CJava%E5%8D%9A%E5%AE%A2%5Cmybatis.assets%5Cimage-20210115145541045.png)

#### 1.6 建立好的目录结构如图所示

![image-20210115145757205](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5CJava%E5%8D%9A%E5%AE%A2%5Cmybatis.assets%5Cimage-20210115145757205.png)

### 2. 更改父工程的pom(maven)配置

#### 2.1  建立好子模块以后，在父工程的(maven)配置文件里面会出现下列代码。

```xml
<!-- 子模块-->
<modules>
    <module>mybatis-01</module>
</modules>
```

表名新建的模块为该工程的子模块。**所以我们只需要在父工程的配置文件中配置相关的依赖，子模块中自动便具有了相关依赖。**

#### 2.2 我们这里导入的依赖

1. mysql - connector -java 的依赖

   ```xml
   <!--    mysql驱动-->
   <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>8.0.20</version>
   </dependency>
   ```

2. junit 依赖

   ```xml
   <!--    Junit依赖-->
   <!-- https://mvnrepository.com/artifact/junit/junit -->
   <dependency>
       <groupId>junit</groupId>
       <artifactId>junit</artifactId>
       <version>4.12</version>
       <scope>test</scope>
   </dependency>
   ```

3. mybatis 相关依赖

```xml
<!--        mybatis依赖-->
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.6</version>
</dependency>
```

#### 2.3 配置mybatis的配置文件

官方文档说：

每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例。

从 XML 文件中构建 SqlSessionFactory 的实例非常简单，建议使用类路径下的资源文件进行配置。 但也可以使用任意的输入流（InputStream）实例，比如用文件路径字符串或 file:// URL 构造的输入流。MyBatis 包含一个名叫 Resources 的工具类，它包含一些实用方法，使得从类路径或其它位置加载资源文件更加容易。

```java
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```



所以第一步，在子模块的src --> main --> resources 目录下新建一个mybatis-config.xml 的配置文件。

![image-20210115150941456](F:%5CMarkDown%5C%E7%AC%94%E8%AE%B0%5CJava%E5%8D%9A%E5%AE%A2%5Cmybatis.assets%5Cimage-20210115150941456.png)

XML 配置文件中包含了对 MyBatis 系统的核心设置，包括获取数据库连接实例的数据源（DataSource）以及决定事务作用域和控制方式的事务管理器（TransactionManager）。

配置文件示例：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--configuration 核心配置文件-->
<configuration>
  <!--环境-->
  <environments default="development">
    <environment id="development">
      <!-- 事务管理， 默认使用的是JDBC-->
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
          <!--将下列value的值该为自己的对应的值-->
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```

当然，还有很多可以在 XML 文件中配置的选项，上面的示例仅罗列了最关键的部分。 注意 **XML 头部的声明**，它用来验证 XML 文档的正确性。**environment 元素体中包含了事务管理和连接池的配置**。mappers 元素则包含了一组映射器（mapper），这些映射器的 XML 映射文件包含了 SQL 代码和映射定义信息。

#### 2.4 从 SqlSessionFactory 中获取 SqlSession

既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);
}
```

诚然，这种方式能够正常工作，对使用旧版本 MyBatis 的用户来说也比较熟悉。但现在有了一种更简洁的方式——使用和指定语句的参数和返回值相匹配的接口（比如 BlogMapper.class），现在你的代码不仅更清晰，更加类型安全，还不用担心可能出错的字符串字面值以及强制类型转换。

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  Blog blog = mapper.selectBlog(101);
}
```

### 3. mybatis 测试

#### 3.1 手动编写mybatis的工具类

工具类代码：

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

/**
 * @Description sqlSessionFactory --- >  sqlSession
 * @Author 毛毛
 * @CreateDate 2021/01/15/周五 15:29
 */
public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;
    //使用mybatis的第一步
    /**
     * 加载类的时候就把相关配置文件加载到内存中，并且只加载一次
     */
    static {
        //直接写文件名就行了，因为该文件在resources文件夹下面
        String resource = "mybatis-config.xml";
        //字节输入流，读取配置文件
        InputStream inputStream = null;
        try {
            inputStream = Resources.getResourceAsStream(resource);
            //获取sqlSessionFactory实例
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (inputStream != null) {
                try {
                    inputStream.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

    }

    //既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
    // SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。
    //但现在有了一种更简洁的方式——使用和指定语句的参数和返回值相匹配的接口（比如 BlogMapper.class），现在你的代码不仅更清晰，更加类型安全，还不用担心可能出错的字符串字面值以及强制类型转换。
    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();

    }
}
```

#### 3.2 编写测试代码

- 实体类

  ```java
  package com.mao.pojo;
  
  import java.sql.Date;
  
  /**
   * @Description admin 实体类
   * @Author 毛毛
   * @CreateDate 2021/01/15/周五 15:47
   */
  public class Admin {
      
      private int id;
      private String username;
      private String password;
      private Date createDate;
  
      public Admin() {
      }
  
      public Admin(String username, String password, Date createDate) {
          this.username = username;
          this.password = password;
          this.createDate = createDate;
      }
  
      public int getId() {
          return id;
      }
  
      public void setId(int id) {
          this.id = id;
      }
  
      public String getUsername() {
          return username;
      }
  
      public void setUsername(String username) {
          this.username = username;
      }
  
      public String getPassword() {
          return password;
      }
  
      public void setPassword(String password) {
          this.password = password;
      }
  
      public Date getCreateDate() {
          return createDate;
      }
  
      public void setCreateDate(Date createDate) {
          this.createDate = createDate;
      }
  
      @Override
      public String toString() {
          return "Admin{" +
                  "id=" + id +
                  ", username='" + username + '\'' +
                  ", password='" + password + '\'' +
                  ", createDate=" + createDate +
                  '}';
      }
  }
  
  ```

- Dao接口

  ```java
  package com.mao.dao;
  
  import com.mao.pojo.Admin;
  
  import java.util.List;
  
  /**
   * @Description  操纵 admin 实体类dao层
   * @Author 毛毛
   * @CreateDate 2021/01/15/周五 15:51
   */
  public interface AdminDao {
      List<Admin> getAdminList();
  }
  ```

- 接口实现类由原来的AdminDaoImpl转换为一个mapper文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <!--namespace= 绑定一个对应的Dao/Mapper接口-->
  <mapper namespace="com.mao.dao.AdminDao">
      <!--    select 查询语句-->
      <!--    id 对应要实现的方法名-->
  <!--    resultType= 对应返回值类型，绑定为一个类-->
      <select id="getAdminList" resultType="com.mao.pojo.Admin">
          select *
          from `admin`
          where id = 1
      </select>
  </mapper>
  ```

#### 3.3 进行代码测试

##### 3.3.1 注意点：

出现错误：

org.apache.ibatis.binding.BindingException: Type interface com.mao.dao.AdminDao is not known to the **MapperRegistry**.

在mybatis的核心配置文件中添加如下代码：

```xml
<!--    每一个Mapper.xml都需要在Mybatis核心配置文件中进行注册-->
    <mappers>
        <mapper resource="com/mao/dao/AdminMapper.xml"/>
    </mappers>
```

更改后还可能出现如下错误：

ExceptionInInitializerError 。 发生初始化异常，无法找到相对应的配置文件。

解决方案：

​		maven由于约定大于配置，所以可能出现配置文件无法导出或者生效的问题，解决方案如下：
​	只需要在父pom文件中（或者在子pom文件中也加入）如下代码：

```xml
<!--maven由于约定大于配置，所以可能出现配置文件无法导出或者生效的问题，解决方案如下-->
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>

        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```

##### 3.3.2 MapperRegistry 是什么：

核心配置文件中注册的Mappers





