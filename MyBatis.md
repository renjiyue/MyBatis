# MyBatis

## MyBatis01:第一个程序

### 什么是MyBatis:

- MyBatis是一款优秀的**持久层框架**
- MyBatis避免了几乎所有的JDBC代码和手动设置参数以及获取结果集的过程
- MyBatis可以使用简单的XML或注解来配置和映射原生信息，将接口和Java的实体类【Pian Old Java Object,普通的JAVA对象】映射成数据库中的记录
- MyBatis是apache的一个开源项目ibatis,2010年这个项目由apache迁移到了Google code，并改名为MyBatis
- 2013年11月迁移到Github

### 持久化

​	持久化是将程序数据在持久状态和瞬时状态转换的机制

- 即把数据（如内存中的对象）保存到可永久保存的存储设备中（如磁盘）。持久化的主要应用是将内存中的对象存储在数据库中，或者存储在磁盘文件中、XML数据文件等等
- JDBC就是一种持久化机制。文件IO也是一种持久化机制
- 在生活中：将鲜肉冷藏，吃的时候再解冻的方法也是。将水果做成罐头的方法也是

​	为什么需要持久化服务呢？那是由于内存本身的缺陷引起的

- 内存断电后数据会丢失，但有一些对象是无论如何都不能丢失的，比如银行账号等，遗憾的是，人们还无法保证内存永不掉电
- 内存过于昂贵，与硬盘、光盘等外存相比，内存的价格要高2~3个数量级，而且维持成本过高，至少需要一直供电吧。所以对象不需要永久保存，也会因为内存的容量限制不能一直呆在内存中，需要持久化来缓存到外存

### 持久层

​	什么是持久层？

- 完成持久化工作的代码块，---->dao层【DAO（Data Access Object）数据访问对象】
- 大多数情况下特别是企业级应用，数据持久化往往也就意味着将内存中的数据保存在磁盘上加以固化，而持久化大的实现过程大多通过各种**关系数据库**来完成
- 不过这里有一个字需要特别强调，也就是所谓的“层”，对于应用系统而言，数据持久功能大多是必不可少的组成部分。也就是说，我们的系统中，已经天然的具备了“持久层”概念，也许是，但也许实际情况并非如此。之所以要独立一个“持久层”的概念，应该有一个相对独立的逻辑层面，专注于数据持久化逻辑地实现
- 与系统其他部分相对而言，这个层面应该具有一个较为清晰和严格的逻辑边界。【说白了就是用来操作数据库存在的】

为什么需要MyBatis

- MyBatis就是帮助程序员将数据存入数据库中，和从数据库中取数据
- 传统的JDBC操作，有很多重复代码块。比如：数据库取出的封装，数据库的建立连接等等...，通过框架可以减少重复代码，提高开发效率
- MyBatis是一个半自动化的**ORM框架（Object Relationship Mapping）-->对象关系映射**
- 所有的事情，不用MyBatis依旧可以做到，只要用了它，所有实现会更加简单！**技术没有高低之分，只有使用这个技术的人有高低之分**
- MyBatis的优点
  - 简单易学：本身久很小且简单，没有任何第三方依赖，最简单安装只要两个jar文件+配置几个映射文件就可已了，易于学习，易于使用，通过文档和源代码，可以比较完全的掌握他的设计思路和实现
  - 灵活：MyBatis不会对应用程序或者数据库的设计强加任何影响。SQL写在XML里，便于统一管理和优化，通过Sql语句可已满足操作数据库的所有需求
  - 解除sql与程序代码的耦合：通过提供DAO层，将业务逻辑和数据访问逻辑分离，使系统的设计更加清晰，更易维护，更易单元测试，sql和代码的分离，提高了可维护性
  - 提供了XML标签，支持编写动态sql
  - ......
- 最重要的一点，使用的人多！公司需要

### MyBatis第一个程序

**思路流程：搭建环境-->导入Mybatis-->编写代码-->测试**

**代码演示**

#### 1.搭建实验数据库

```sql
CREATE DATABSE `mybatis`;

USE `mybatis`;

DROP TABLE IF EXISTS `USER`;

CREATE TABLE `user`(
	`id` int(20) NOT NULL,
    `name` varchar(30) DEFAULT NULL,
    `pwd` varchar(30) DEFAULT NULL,    
    PRIMARY KEY(`id`)
)ENGINE=InnoDB DEFAILT CHARSET=utf-8;

insert into `user`(`id`,`name`,`pwd`) value(1,"狂神"，'123456',2,'张三','abcde',3,'李四','987654');
```

#### 2.导入MyBatis相关jar包

- Github上找

```xml
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.49</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
```

#### 3.编写MyBatis和弦配置文件

- 查看帮助文档

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!-- configuration标签 => 声明MyBatis核心配置 -->
<configuration>
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    <!-- environments标签 => 设置MyBatis选用的环境信息 -->
    <environments default="development">
        <environment id="development">
            <!-- transactionManager标签 => 事务管理 -->
            <transactionManager type="JDBC"/>
            <!-- dataSource标签 => 配置数据源属性 -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=utf8&amp;serverTimezone=CST"/>
                <property name="username" value="root"/>
                <property name="password" value="******"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/renjiyue/dao/UserMapper.xml"/>
    </mappers>
</configuration>
```

#### 4.编写MyBatis工具类

- 查看帮助文档

```java
package com.renjiyue.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MybatisUtlis {
    private static SqlSessionFactory sqlSessionFactory;
    static {

        try {
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession(true);
    }
}
```

#### 5.创建实体类

```java
package com.renjiyue.pojo;

import lombok.Data;

@Data
public class User {
    private int id;
    private String username;
    private String possword;

}

```

#### 6.编写Mapper接口类

```java
package com.renjiyue.dao;

import com.renjiyue.pojo.User;

import java.util.List;

public interface UserDao {
    List<User> getUserList();
}

```

#### 7.编写Mapper.xml配置文件

- namespace十分重要，不能写错

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.renjiyue.dao.UserDao">

    <select id="getUserList" resultType="com.renjiyue.pojo.User">
        select * from mybatis.user
    </select>
</mapper>
```

#### 8.编写测试类

- junit包测试

```java
package com.renjiyue.Dao;

import com.renjiyue.dao.UserDao;
import com.renjiyue.pojo.User;
import com.renjiyue.utils.MybatisUtlis;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserDaoTest {
    @Test
    public void getUserList01(){
        SqlSession sqlSession = MybatisUtlis.getSqlSession();
        UserDao mapper = sqlSession.getMapper(UserDao.class);
        List<User> userList = mapper.getUserList();
        for (User user : userList) {
            System.out.println(user);
        }
        sqlSession.close();
    }
}
```

#### 9.运行测试，成功的查询出来的我们的数据，ok

### 问题说明

**可能出现问题说明：Maven静态资源过滤问题**

```xml
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

## MyBatis02:CRUD操作及配置解析

### 1.namespace

1. 将上面案例中的UserMapper接口改名为UserDao;
2. 将UserMapper.xml中的namespace改为UserDao的路径
3. 再次测试

**结论：**

- 配置文件中namespace中的名称为对应的Mapper接口或者Dao接口的完整包名

  必须一致

### 2.select

- select标签是mybatis中最常用的标签之一

- select语句有很多属性可以详细配置每一条SQL语句

  - SQL语句返回值类型。【完整的类名或者别名】
  - 传入SQL语句的参数类型。【万能的Map,可以多尝试使用】
  - 命名空间中唯一的标识符
  - 接口中的方法名与映射文件的SQL语句ID一一对应
  - id
  - parameterType
  - resultType


### 需求：根据id查询用户

#### 1.在UserMapper中添加对应方法

```java
package com.renjiyue.dao;

import com.renjiyue.pojo.User;

import java.util.List;

public interface UserMapper {
    //查询全部用户
    List<User> selectUser();
    //根据id查询用户
    User selectUserById(int id);
}

```

#### 2.在UserMapper.xml中添加Select语句

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.renjiyue.dao.UserMapper">
    <select id="selectUserById" resultType="com.renjiyue.pojo.User">
        select * from user where id = #{id}
    </select>
</mapper>
```

#### 3.测试类中测试

```java
package com.renjiyue.dao;

import com.renjiyue.pojo.User;
import com.renjiyue.utils.MyBatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;


public class testSelectUserById {
    @Test
    public void test(){
        SqlSession session = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        User user = mapper.selectUserById(1);
        System.out.println(user);
        session.close();
    }
}
```

#### 课堂练习

##### 1.思路一 直接在方法中传递参数

1. 在接口方法的参数前加@Param属性
2. Sql语句编写的时候，直接取@Param中设置的值即可，不需要单独设置参数类型

UserMapper:

```java
    //通过密码和姓名查询用户
    User selectUserByNP(@Param("name")String username,@Param("pwd")String possword);
```

UserMapper.xml

```xml
    <select id="selectUserByNP" resultType="com.renjiyue.pojo.User">
        select * from user where username = #{name} and possword = #{pwd}
    </select>
```

##### 2.思路二：使用万能的map

1.在接口方法中，参数直接传递Map;

```java
    User selectUserByNP2(Map<String,Object> map);
```

2.编写sql语句的时候，需要传递参数类型，参数类型为map

```xml
    <select id="selectUserByNP2" parameterType="map" resultType="com.renjiyue.pojo.User">
        select * from user where username = #{username} and possword = #{possword}
    </select>
```

3.在使用方法的时候，Map的Key为sql中取的值即可，没有顺序要求！

```java
@Test   
public void test(){
        Map<String,Object>map = new HashMap<String,Object>();
        map.put("username","renjiyue");
        map.put("possword","123456");
        SqlSession session = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        User user = mapper.selectUserByNP2(map);
        System.out.println(user);
        session.close();
    }
```

总结：如果参数过多，我们可以考虑直接使用Map实现，如果参数过少，直接传递参数即可

### 3.insert

我们一般使用insert标签进行插入操作，他的配置和select标签差不多！

#### 需求：给数据库添加一个用户

- 在UserMapper接口中添加对应的方法

```java
    //添加一个用户
    int addUser(User user);
```

- 在UserMapper.xml中添加insert语句

```xml
    <insert id="addUser" parameterType="com.renjiyue.pojo.User">
        insert into user (id,username,possword) values (#{id},#{username},#{possword})
    </insert>
```

- 测试

```java
@Test
public void test(){
    SqlSession sqlSession = MyBatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = new User(4, "yuweishui", "123456");
    int i = mapper.addUser(user);
    System.out.println(i);
    //提交事务，重点！不写的话不会提交事务
    sqlSession.commit();
    sqlSession.close();
}
```

注意点：增删改查操作需要提交事务

### 4.Update

我们一般使用update标签进行更新操作，他的配置和select标签差不多

#### 需求：修改用户的信息

- 同理，编写接口的方法

```java
    //修改一个用户
    int updateUser(User user);
```

- 编写对应的配置文件SQL

```xml

    <update id="updateUser" parameterType="com.renjiyue.pojo.User">
        update user set username = #{username},possword = #{possword} where id = #{id}
    </update>
```

- 测试

```java
    @Test
    public void Test2(){
        SqlSession session = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        User user = new User(4, "yuweishui1", "111111");
        int i = mapper.updateUser(user);
        System.out.println(i);
        session.commit();
        session.close();
    }
```

### 5.delete

我们一般使用delete标签进行删除操作，他的配置和select标签差不多

#### 需求：根据id删除一个用户

- 同理，编写接口的方法

```java
    //删除一个用户
    int deleteUser(int id);
```

- 编写对应的配置文件SQL

```xml
    <delete id="deleteUser" parameterType="int">
        delete from user where id = #{id}
    </delete>
```

- 测试

```java
    @Test
    public void test3(){
        SqlSession session = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        int i = mapper.deleteUser(4);
        System.out.println(i);
        session.commit();
        session.close();
    }
```

小结：

- 所有的增删改查都需要提交事务
- 接口的所有普通参数，尽量都写上@Param参数，尤其是多个参数，必须协商！
- 有时候根据业务的需求，可以考虑使用map传递参数
- 为了规范操作，在SQL的配置文件中，我们尽量将Parameter参数和resultType都写上1

#### 思考题

#### 模糊查询like语句该怎么写？

第一种：在Java代码中添加SQL通配符

```java
String wildcardname = "%smi%";
list<name> names = mappers.selectlike(wildcardname);

<select id="selectlike">
    select * from foo where bar like #{value}
</select>
```

第二种：在sql语句中拼接通配符，会引入sql注入

```java
string wildcardname = "smi";
list<name> names = mappers.selectlike(wildcardname);

<select>
    select * from foo where bar like "%"#{value}"%"
</select>
```

### 6.配置解析

#### 核心配置文件

- mybatis-config.xml系统核心配置文件
- MyBatis的配置文件包含了会深深影响MyBatis行为的设置和属性信息
- 能配置的内容如下

```xml
configuration(配置)
properties(属性)
settings(设置)
typeAliases(类别别名)
typeHandlers(类别处理器)
objectFactory(对象工厂)
plugins(插件)
envrionments(环境配置)
envrionment(环境变量)
transactionManageer(事务管理器)
dataSource(数据源)
databaseldProvider(数据库厂商标识)
mappers(映射器)
<!--注意元素节点的顺序！顺序不对报错-->
```

我们可以阅读mybatis-config.xml上面的dtd的头文件

#### envrionments元素

```xml
    <!-- environments标签 => 设置MyBatis选用的环境信息 -->
    <environments default="development">
        <environment id="development">
            <!-- transactionManager标签 => 事务管理 -->
            <transactionManager type="JDBC"/>
            <!-- dataSource标签 => 配置数据源属性 -->
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="&{possword}"/>
            </dataSource>
        </environment>
    </environments>
```

- 配置MyBatis的多套运行环境，将SQL映射到多个不同的数据库上，必须指定其中一个为默认运行环境（通过default指定）

- 子元素节点：environment

  - dataSource元素使用标准的jdbc数据源接口来配置JDBC链接对象的资源

  - 数据源是必须配置的

  - 有三种内建的数据源类型

  - ```xml
    type = "[UNPOOLED|POOLED|JNDI]"
    ```

  - unpooled:这个数据库的实现只是每次请求是打开和关闭连接

  - pooled：这种i数据源的实现利用“池”的概念将JDBC连接对象组织起来，这是一种使得并发Web应用快速想用请求的流行处理方式

  - jndi:这个数据源的实现是为了能在如Spring或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个jndi上下文的引用

  - 数据源也有很多第三方的实现，如dbcp,c3p0,druid等等...

  - 详情：去看官方文档

  - 这两种事务管理类型都不需要设置任何属性

  - 具体的一套环境，通过设置id进行区别，id保证唯一

  - 子元素节点：transactionManager - [事务管理器]

  - ```xml
    <!--语法-->
    <transactionManager> type = "[JDBC|MANAGED]"/>
    ```

  - 子元素节点：数据源（dataSource）

#### mappers元素

mappers

- 映射器：定义映射SQL语句文件
- 既然MyBatis的行为其他元素已经配置完了，我们现在就要定义SQL映射语句了，但是首先我们需要告诉MyBatis到哪里去找这些语句。Java在自动查找方面没有提供一个很好的方法，所以最佳的方式是告诉MyBatis到哪里取找映射文件，你可以使用相对类路径的资源引用，或完全限定资源定位符（包括 file://的URL），或类名和包名等。映射器是MyBatis中最核心的组件之一，在MyBatis 3之前，只支持XML映射器，即：所有的SQL语句都必须在XML文件中配置。而从MyBatis 3开始，还支持接口映射器，这种映射器方式允许以Java代码的方式注解定义SQL语句，非常简洁

引入资源方式

```xml
<!--使用相对于类路径的资源引用-->
<mappers>
    <mappper resource = "org/mybatis/builder/PostMapper.xml"/>
</mapppers>
```

```xml
<!--使用完全限定资源定位符（URL）-->
<mappers>
    <mapper url="file://var/mappers/AuthorMapper.xml"/>
</mappers>
```

```xml
<!--使用映射器接口实现类的完全限定类名需要配置文件名称和接口名称一致，并且位于同一个目录下-->
<mappers>
    <mapper class="org.mybatis.builder.AuthorMapper"/>
</mappers>
```

```xml
<!--将包内的映射器接口实现全部注册为映射器但是需要配置文件名称和接口名称一致，并且位于同一个目录下-->
<mappers>
	<package name="org.mybatis.builder"/>
</mappers>
```

Mapper文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.renjiyue.mapper.UserMapper">
		
</mapper>
```

- namespace中文意思：命名空间，作用如下：

  - namespace的命名必须跟某个接口同名
  - 接口中的方法与映射文件中sql语句id应该意义对应

  1. namespace和子元素的id联合保证唯一，区别不同的mapper
  2. 绑定DAO接口
  3. namespace命名规则：包名+类名

  MyBatis的真正强大在于他的映射语句，这是他的魔力所在，由于他的异常强大，映射器的XML文件就显得相对简单，如果那他跟具有相同功能的JDBC代码进行对比，你会立刻发现省掉了将尽95%的代码。MyBatis为聚焦于sql而构建，已尽可能的为你减少麻烦 。

#### Properties优化

数据库这些属性都是可外部配置且动态替换的，即可以在典型的Java属性文件中配置，亦可以通过properties元素的子元素来传递。具体的官方文档

我们来优化我们的配置文件

第一步：在资源目录下新建一个db.properties

```xml
driver = com.mysql.jdbc.Driver
url = jdbc:mysql://localhost:3306/mybatisuseSSL=false&useUnicode=true&characterEncoding=utf8
username = root
password = 991211
```

第二步：将文件导入porperties配置文件

```xml
<!-- configuration标签 => 声明MyBatis核心配置 -->
<configuration>
    <properties resource="db.properties"/>
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    <!-- environments标签 => 设置MyBatis选用的环境信息 -->
    <environments default="development">
        <environment id="development">
            <!-- transactionManager标签 => 事务管理 -->
            <transactionManager type="JDBC"/>
            <!-- dataSource标签 => 配置数据源属性 -->
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/renjiyue/dao/UserMapper.xml"/>
    </mappers>
</configuration>
```

- 配置文件优先级问题
- 新特性：使用占位符

#### typeAliases优化

类别名是为Java类型设置一个短的名字，他只和XML文件配置有关，存在的意义仅用于来减少类完全限定名的冗余

```xml
<!--配置别名，注意顺序-->
<typeAliases>
    <typeAlias type="com.renjiyue.pojo.User" alias="User"/>
</typeAliases>
```

当配置时，User可以用在任何时候使用com.renjiyue.pojo.User的地方，也可以指定一个包名，MyBatis会在包名下面搜索需要的Java Bean，比如：

```xml
<typeAliases>
    <typeAlias type="com.renjiyue.pojo"/>
</typeAliases>
```

每一个在包com.renjiyue.pojo中的Java Bean,在没有注解的情况下，会使用Bean的首字母小写的非限定类名作为他的别名

若有注解，则别名为其注解值。见下面的例子

```java
@Alias("user")
public class User(){
    ....
}
```

去官网查看一下MyBatis默认的一些类别名！

#### 其他配置浏览

##### 设置

- 设置（settings）相关==>查看帮助文档
  - 懒加载
  - 日志实现
  - 缓存开启关闭
- 一个配置完整的setting元素的实例如下

```xml
<settings>
  <setting name="cacheEnabled" value="true"/>
  <setting name="lazyLoadingEnabled" value="true"/>
  <setting name="multipleResultSetsEnabled" value="true"/>
  <setting name="useColumnLabel" value="true"/>
  <setting name="useGeneratedKeys" value="false"/>
  <setting name="autoMappingBehavior" value="PARTIAL"/>
  <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
  <setting name="defaultExecutorType" value="SIMPLE"/>
  <setting name="defaultStatementTimeout" value="25"/>
  <setting name="defaultFetchSize" value="100"/>
  <setting name="safeRowBoundsEnabled" value="false"/>
  <setting name="mapUnderscoreToCamelCase" value="false"/>
  <setting name="localCacheScope" value="SESSION"/>
  <setting name="jdbcTypeForNull" value="OTHER"/>
  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings>
```

##### 类处理器

- 无论是MyBatis在预处理语句（PreparedStatement）中设置一个参数时，还是从结果集中去除一个值时，都会用类型处理器将获取的值以合适的方式转换成Java类型
- 你可以重写类型处理器或创建你自己的类型处理器来处理不支持的或非标准的类型。

##### 对象工厂

- MyBatis每次创建结果对象的新实例时，他都会使用一个对象工厂（ObjectFactory）实例来完成
- 默认的对象工厂需要做的仅仅是实例化目标类，要么通过默认构造方法，要么在参数映射存在的时候通过有参构造来实例化
- 如果想要覆盖工厂的默认行为，则可以通过创建自己的对象工厂来实现

#### 生命周期和作用域

##### 作用域（Scope）和生命周期

理解我们目前已经讨论过的不同作用域和生命周期是至关重要的，因为错误的使用会导致非常严重的并发问题

先看一个流程图，分析MyBatis的执行过程

![Scope](C:\Users\000\Desktop\MyBatis\image\Scope.png)

##### 作用域理解

- SqlSessionFactoryBuilder的作用在于创建SqlSessionFactory，创建成功后，SqlSessionFactoryBuilder失去作用，所以他只能存在于创建SqlSessionFactory的方法中，而不是让其长期存在。因此SqlSessionFactoryBuilder实例的最佳作用域是方法作用域（也就是局部变量法）
- SqlSessionFactory可以被认为是一个数据库连接池，他的作用是创建SqlSession接口对象，因为MyBatis的本质就是Java对数据库的操作，所以SqlSessionFactory的生命周期存在于整个MyBatis应用，所以可以认为SqlSesionFactory的生命周期就等同于MyBatis的应用周期
- 由于SqlSessionFactory是一个对数据库的连接池，所以它占据着数据库的连接资源，如果创建多个SqlSessionFactory，那么就存在多个数据库连接池，这样不利于对数据库资源的控制，也会导致数据库链接资源被耗光，出现系统宕机等情况，所以尽量避免发生这样的情况
- 因此在一般的应用中我们往往希望SqlSessionFactory作为一个单例，让他在应用中被共享，所以说**SqlSessionFactory的最佳作用域是应用作用域**
- 如果说SqkSessionFactory相当于数据库链接池，那么SqlSession就相当于一个数据库连接（Connection对象），你可以在一个事务里面执行多条SQL，然后通过commit,rollback等方法，提交或者回滚事务。所以它应该存活在一个业务请求中，处理完整个请求后，应该关闭这条连接，让它归还给SqlSessionFactory，否则数据库资源很快被耗费精光，系统就会瘫痪，所以用try...catch..finally...语句来保证其正确关闭
- **所以SqlSession的最佳的作用域是请求或方法作用域**

![liveCycle](C:\Users\000\Desktop\MyBatis\image\liveCycle.png)

## MyBatis03:ResultMap及分页

### 1.查寻为null问题

要解决的问题：属性名和字段名不一致

环境：新建一个项目，将之前的项目拷贝过来

- 查看之前的数据库的字段名

![MySQL](C:\Users\000\Desktop\MyBatis\image\MySql.png)

- Java中的实体类设计

```java
package com.renjiyue.pojo;

import lombok.Data;

@Data
public class User {
    private int id;
    private String username;
    private String pwd;  //重点在这儿

    public User() {
    }

    public User(int id, String username, String pwd) {
        this.id = id;
        this.username = username;
        this.possword = pwd;
    }
}
```

- 接口

```java
    //根据id查询用户
    User selectUserById(int id);
```

- mapper映射文件

```xml
    <select id="selectUserById" resultType="com.renjiyue.pojo.User">
        select * from user where id = #{id}
    </select>
```

- 测试

```java
    @Test
    public void test4(){
        SqlSession session = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        User user = mapper.selectUserById(1);
        System.out.println(user);
        session.close();
    }
```

- 结果：

  - User(id = 1,username="huxiaolong",pwd"null")
  - 查询出来发现pwd为空，说明出现了问题！

- 分析

  - select * from user where id =#{id} 可以看作 select id，username,password from user where id=#{id}
  - mybatis会根据这些查询的列名（会将列名转化为小写，数据库不区分大小写），去对应的实体类中查找相应对应列名和set方法设值，由于找不到setPassword(),所以pwd返回null;[自动映射]

- 解决方案

  - 方案一：为列名指定别名，别名和Java实体类的属性名一致

  ```xml
      <select id="selectUserById" resultType="com.renjiyue.pojo.User">
          select id,username,password as pwd from user where id = #{id}
      </select>
  ```

  - 方案二：使用结果集映射-->ResultMap[推荐]

  ```xml
      <resultMap id="UserMap" type="com.renjiyue.pojo.User">
          <id column="id" property="id"/>
          <result column="username" property="username"/>
          <result column="password" property="pwd"
      </resultMap>
      <select id="selectUserById" resultMap="UserMap">
          select id,username,passwoord from user where id = #{id}
      </select>
  ```

#### 2.ResultMap

- ###### 自动映射

  - resultMap元素是Mybatis中最重要最强大的元素。它可以让你从90%的JDBC **ResultSets**数据提取代码中解放出来
  - 实际上，在为上一代比如连接的复杂语句编写映射代码的时候，一份resultMap能够代替实现同等功能的长达数千行的代码
  - ResultMap的设计思想，对于简单的语句根本不需要配置显式的结果映射，而对于复杂一点儿的语句只需要描述他们的关系就行了
  - 你已经见过简单的的映射语句的实例了，但并没有显示指定resultMap。比如：
  
  ```xml
  <select id = "selectUserById" resultType="map">
      select id,name,pwd from user where id = #{}id
  </select>
  ```
  
  上述语句只是简单的将所有的列映射到HashMap的键上，不由resultType属性指定，虽然在大部分情况下都够用，但是HashMap不是一个很好的模型。你的程序更可能会使用JavaBean或POJO(Plain Old Java Objects，普通老式Java对象)作为模型
  
  ResultMap最优秀ide地方在于，虽然你已经对它相当了解了，但是根本久不需要显式的用到他们
  
- 手动映射

  - 返回值类型为resultMap

  ```xml
  <select id = "selectUserById" resultType="UserMap">
      select id,name,pwd from user where id = #{}id
  </select>
  ```

  - 编写resultMap,实现手动映射！

  ```xml
      <resultMap id="UserMap" type="com.renjiyue.pojo.User">
          <!--id为主键-->
          <id column="id" property="id"/>
          <!--column是数据库表的列名，property是对应实体类的属性名-->
          <result column="username" property="username"/>
          <result column="password" property="pwd"
      </resultMap>
  ```

  如果世界总是这么简单就好了。但是肯定不是的，数据库中，存在一对多，多对一情况，我们之后会使用到一些高级的结果集映射，association，collection这些，我们将在之后讲解，今天你们需要把这些知识都消化掉才是最重要的！理解结果集映射这个概念！

### 2.分页的几种方式

#### 日志工厂

思考：我们在测试SQL的时候，要是能够在控制台输出SQL的话，是不是就能够有更快的排错效率？

如果一个数据库相关的操作出现了问题，我们可以根据输出的SQL语句快速排查问题

对于以往的开发过程，我们会经常使用到debug模式来调节，跟踪我们的代码执行过程。但是现在使用MyBatis是基于接口，配置文件的源代码执行过程。因此，我们必须选择日志工具来作为我们开发，调节的工具

MyBatis内置的日志工厂提供日志功能，具体的日志实现有以下几种工具

- SLF4J
- Apache Commons Logging
- Log4j 2
- log4j
- JDK logging

具体选择那个日志实现工具有MyBatis的内置日志工厂。他会使用最先找到的（按上文列举的顺序查找）。如果一个都没找到，日志功能就会被禁用。

##### 标准日志实现

指定MyBatis应该使用那个日志记录实现。如果此设置不存在，则会自动发现日志记录实现。

```xml
<settings>
	<setting name="loglmpl" value="STDOUT_LOGGING"/>
</settings>
```

测试，可以看出控制台有大量的输出！我们可以通过这些输出来判断程序到底哪里出了BUG

##### **Log4j**

- Log4j是Apache的一个开源项目
- 通过使用Log4j，我们可以控制日志信息输送的目的地：控制台，文本，GUI组件
- 我们也可以控制每一个日志输出格式
- 通过定义每一条日志信息的级别，我们能够更加细致控制日志的生成过程。最令人感兴趣的就是，这些可以通过一个配置文件来灵活的进行配置，而不需要修改应用的代码

###### 使用步骤

- 导入Log4j的包

```xml
<dependency>
	<groupld>log4j</groupld>
    <artifactld>log4j</artifactld>
    <version>1.2.17</version>
</dependency>
```

- ​	配置文件编写

```properties
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/shun.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

- setting设置日志实现

```xml
<ssettings>
	<setting name="loglmpl" value="LOG4J"/>
</ssettings>
```

- 在程序中使用Log4J进行输出

```java
//注意导包：org.apache.log4j.Logger
static Logger logger = Logger.getLogger(MyTest.class);

@Test
public void selectUser() {
   logger.info("info：进入selectUser方法");
   logger.debug("debug：进入selectUser方法");
   logger.error("error: 进入selectUser方法");
   SqlSession session = MybatisUtils.getSession();
   UserMapper mapper = session.getMapper(UserMapper.class);
   List<User> users = mapper.selectUser();
   for (User user: users){
       System.out.println(user);
  }
   session.close();
}

```

- 测试，看控制台输出
  - 使用log4j输出日志
  - 可以看到还生成了一个日志的文件【需要修改file的日志级别】

### 3.limit实现分页

思考：为什么需要分页？

在学习mybatis等持久层框架的时候，会经常对数据进行增删改查操作，使用最多的是对数据库进行查询操作，如果查询大量数据的时候，我们往往使用分页进行查询，也就是每次处理小部分苏剧，这样对数据库夜里就在可空范围内

- 使用Limit实现分页

```xml
#语法
SELECT * FROM table LIMIT stratlndex pageSize

SELECT * FROM table LIMIT 5,10;//检索记录行6-15

#为检索从某一个偏移量到记录集的结束所有的记录行，可以指定第二个参数为-1
SELECT * FROM table LIMIT 95,-1;//检索记录行96-last

#如果只给定一个参数，它表示返回最大的记录行数据；
SELECT * FROM table LIMIT 5；//检索前5个记录行
#换句话说，LIMIT n等价于LIMIT 0,n;
```

- 步骤

  - 修改Mapper文件

  ```xml
  <select id="selectUser" parameterType="map" resultType="user">
  	select * from user limit #{startlndx},#{pageSize}
  </select>
  ```

  - Mapper接口，参数为map

  ```java
  List<User> selectUser(Map<String,integer>map);
  ```

  - 在测试类中传入参数测试

    - 推断：起始位置=（当前页面-1）*页面大小

    ```java
    
    //分页查询 , 两个参数startIndex , pageSize
    @Test
    public void testSelectUser() {
       SqlSession session = MybatisUtils.getSession();
       UserMapper mapper = session.getMapper(UserMapper.class);
    
       int currentPage = 1;  //第几页
       int pageSize = 2;  //每页显示几个
       Map<String,Integer> map = new HashMap<String,Integer>();
       map.put("startIndex",(currentPage-1)*pageSize);
       map.put("pageSize",pageSize);
    
       List<User> users = mapper.selectUser(map);
    
       for (User user: users){
           System.out.println(user);
      }
       session.close();
    }
    ```

### 4.RowRounds分页

我们除了使用Limit在sql层面实现分页，也可以使用RowBounds在Java代码层面实现分页，当然此中方式作为了解即可，我们看下如何实现的

- 步骤

  - mapper接口

  ```java
  //选择全部用户RowBounds实现分页
  List<User> getUserByRowBounds();
  ```

  - mapper文件

  ```xml
  <select id="getUserByRowBounds">
  	select * from user
  </select>
  ```

  - 测试类

  在这里，我们需要使用RowBounds类

  ```java
  ![PageHelper](C:\Users\000\Desktop\MyBatis\image\PageHelper.png)@Test
  public void testUserByRowBounds() {
     SqlSession session = MybatisUtils.getSession();
  
     int currentPage = 2;  //第几页
     int pageSize = 2;  //每页显示几个
     RowBounds rowBounds = new RowBounds((currentPage-1)*pageSize,pageSize);
  
     //通过session.**方法进行传递rowBounds，[此种方式现在已经不推荐使用了]
     List<User> users = session.selectList("com.kuang.mapper.UserMapper.getUserByRowBounds", null, rowBounds);
  
     for (User user: users){
         System.out.println(user);
    }
     session.close();
  }
  ```

### 5.PageHelper

<img src="C:\Users\000\Desktop\MyBatis\image\PageHelper.png" alt="PageHelper" style="zoom:40%;" />

## MyBatis04:使用注解开发

### 面向接口编程

- 大家之前都学过面向对象编程，也学习过接口，但在真正的开发中，很多时候我们会选择面向接口编程
- **根本原因：解耦，可拓展，提高复用，分层开发中，上层不用管具体的实现，大家都遵守共同的标准，是的开发变得容易，规范性更好**
- 在一个面向对象的系统中，系统的各种功能是由像许多的不同对象写作完成的。在这中情况下，各个对象内部是如何实现自己的，对系统设计人员讲就不那么重要了
- 而各个对象之间的协作关系则成为系统设计的关键，小到不同类之间的通信，大到各模块之间的交互，在系统设计之初都要赵中考虑的，这也是系统设计的主要工作内容，面向接口编程就是这种按照这种思想来编程。

#### 关于接口的理解

- 接口从更深层次的理解，应是定义（规范，约束）与实现（名实分离的原则）的分离
- 接口的本身反应了系统设计人员对系统的抽象理解
- 接口应有两类：
  - 第一类是对一个个体的抽象，他可对应为一个抽像体（abstruct class）
  - 第二类是对一个个体某一方面的抽象，即形成一个抽象面（interface）
- 一个体可能有多个抽象面。抽象体与抽象面是有区别的

三个面向区别

- 面向对象是指，我们考虑问题，以对像为单位，考虑它的属性和方法
- 面向过程是指：我们考虑问题，以一个具体的流程（事务过程）为单位，考虑它的实现
- 接口设计与非接口设计是针对复用技术而言的，与面向对象（过程）不是一个问题，更多的体现就是对系统整体的架构

### 使用注解开发

- mybatis最初配置信息是基于XML，映射语句（SQL）也是定义在XML中的，而到MyBtais3提供了新的基于注解的配置。不幸的是，Java注解的表达里和灵活性十分有限。最强大的MyBatis映射并不能用注解来构建

- sql类型主要分成：

  - @select()
  - @update()
  - @insert()
  - @delete()

  注意：利用注解开发就不需要mapper.xml映射文件了

  1.我们在我们的接口中添加注解

  ```java
      @Select("select id,username,possword from user")
      List<User> getAllUser();
  ```

  2.mybatis的核心配置文件中注入

  ```xml
  <mappers>
  	<mapper namespace="com.renjiyue.dao.UserMapper"/>
  </mappers>
  ```

  3,测试

  ```java
      @Test
      public void testGetAllUser(){
          SqlSession sqlSession = MyBatisUtils.getSqlSession();
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
          for (User user : mapper.getAllUser()) {
              System.out.println(user);
          }
      sqlSession.close();
      }
  ```

  5.本质上利用了jvm的动态代理机制

  ![jvm](C:\Users\000\Desktop\MyBatis\image\jvm.png)

  6.MyBatis详细的执行流程

  ![zhixing](C:\Users\000\Desktop\MyBatis\image\zhixing.png)

  注解增删改

  改造MyBatisUtils工具类的getSession()方法，重载实现

  ```java
      //获取SqlSession链接
      public static SqlSession getSqlSession(){
          return sqlSessionFactory.openSession(true);
      }
      public static SqlSession getSqlSession(boolean flag){
          return sqlSessionFactory.openSession(flag);
      }
  ```


#### 查询

1.编写接口方法和注解

```java
    @Select("select * from user where id = #{id}")
    User selectUserById1(@Param("id") int id);
```

2.测试

```java
    @Test
    public void testSelectUserById1(){
        SqlSession session = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        User user = mapper.selectUserById(2);
        System.out.println(user);
        session.close();
    }
```

#### 新增

1.编写接口方法注解

```java
    //添加一个用户
    @Insert("insert into user(id,username,possword) value (#{id},#{username},#{possword})")
    int addUser2(User user);
```

2.测试

```java
    public void testAddUser(){
        User user = new User(4,"huxiaolong1","123456");
        SqlSession session = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        mapper.addUser2(user);
        session.close();
    }
```

#### 删除

1.编写接口方法注解

```java
    //根据id删除
    @Delete("delete from user where id = #{id}")
    int deleteUser3(@Param("id") int id);
```

2.测试

```java
    @Test
    public void testDeleteser(){
        SqlSession session = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        mapper.deleteUser3(4);
        session.close();
    }
```

[注意点：增删改一定记得对事务的处理]

#### 关于@Param

@Param注解用于给方法参数起一个名字。以下是总结的使用原则

- 在方法只接受一个参数的情况下，可以不使用@Param
- 在方法接受多个参数的情况下，建议一定要使用@Param注解给参数命名
- 如果参数是JavaBean，则不能使用@Param
- 不使用@Param注解时，参数只能有一个，并且是JavaBean

#### #和$的区别

- #{}的作用主要是替换预编译语句（PrepareStatement）中的占位符？

```sql
INSERT INTO user(name) VALUES (#{name});
INSERT INTO user(name) VALUES (?) 
```

- ${}的作用是直接进行字符串替换

```sql
INSERT INTO user(name) VALUES ('${name}');
INSERT INTO user(name) VALUES ('huxiaolong')
```

### MyBatis05:一对多和多对一处理

#### 多对一的处理

##### 多对一的理解

- 多个学生对应一个老师
- 如果对于学生这边，就是一个多对一的现象，即从学生这边关联一个老师

#### 数据库设计

```sql
CREATE TABLE `teacher` (
`id` INT(10) NOT NULL,
`name` VARCHAR(30) DEFAULT NULL,
PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO teacher(`id`, `name`) VALUES (1, '秦老师');

CREATE TABLE `student` (
`id` INT(10) NOT NULL,
`name` VARCHAR(30) DEFAULT NULL,
`tid` INT(10) DEFAULT NULL,
PRIMARY KEY (`id`),
KEY `fktid` (`tid`),
CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8


INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('1', '小明', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('2', '小红', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('3', '小张', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('4', '小李', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('5', '小王', '1');
```

#### 搭建测试环境

1.IDEA安装Lombok插件

2.引入Maven依赖

```xml
    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.10</version>
        </dependency>
    </dependencies>
```

3.在代码中增加注解

```java
import lombok.Data;

@Data
public class Student {
    private int id;
    private String name;
    private Teacher teacher;
}
```

```java
import lombok.Data;
@Data
public class Teacher {
    private int id;
    private String name;
}

```

4.编写实体类对应的Mapper接口【两个】

- 无论有没有需求，都应该写上，以备不时之需

```java
public interface StudentMapper {
    //获取所有学生以及对应老师增加方法
    List<Student> getStudent();
}
```

```java
public interface TeacherMapper {
    @Select("select * from teacher where id=#{tid}")
    Teacher getteacher(@Param("tid") int id);
}
```

5.编写Mapper接口对应的mapper.xml配置文件【两个】

- 无论有没有需求，都应该写上，以备后来之需

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.renjiyue.dao.StudentMapper">
</mapper>
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.renjiyue.dao.TeacherMapper">
</mapper>
```

#### 按查询嵌套处理

1.给StudentMapper接口增加方法

```java
    //获取所有学生以及对应老师增加方法
    List<Student> getStudent();
```

2.编写对应的Mapper文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.renjiyue.dao.StudentMapper">

    <select id="getStudent" resultMap="StudentTeacher">
        select * from student
    </select>

    <resultMap id="StudentTeacher" type="com.renjiyue.pojo.Student">
        <result property="id" column="id"/>
        <result property="name" column="name"/>
        <association property="teacher" column="tid" javaType="com.renjiyue.pojo.Teacher" select="getTeacher"/>
    </resultMap>
    <select id="getTeacher" resultType="com.renjiyue.pojo.Teacher">
        select * from teacher where id = #{id}
    </select>
</mapper>
```

3.编写完毕去MyBatyis配文件中，注册Mapper!

4.注意点说明

```xml
<resultMap id="StudentTeacher" type="Student">
   <!--association关联属性 property属性名 javaType属性类型 column在多的一方的表中的列名-->
   <association property="teacher"  column="{id=tid,name=tid}" javaType="Teacher" select="getTeacher"/>
</resultMap>
<!--
这里传递过来的id，只有一个属性的时候，下面可以写任何值
association中column多参数配置：
   column="{key=value,key=value}"
   其实就是键值对的形式，key是传给下个sql的取值名称，value是片段一中sql查询的字段名。
-->
<select id="getTeacher" resultType="teacher">
  select * from teacher where id = #{id} and name = #{name}
</select>
```

5.测试

```java
    @Test
    public void testStudent(){
        SqlSession sqlSession = Mybatisutils.getSqlSession();
        StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);
        for (Student student : mapper.getStudent()) {
            System.out.println(student);
        }
        sqlSession.close();
    }
```

#### 按结果嵌套处理

除了上面的这种方式，还有其他思路吗？

我们还可以按照结果进行嵌套处理

1.接口方法编写

```java
     List<Student> getStudent2();
```

2.编写对应的mapper文件

```xml
    <select id="getStudent2" resultMap="StudentTeacher2">
        select s.id sid, s.name sname , t.name tname from student s,teacher t where s.tid = t.id
    </select>

    <resultMap id="StudentTeacher2" type="com.renjiyue.pojo.Student">
        <id property="id" column="sid"/>
        <result property="name" column="sname"/>
        <association property="teacher" javaType="com.renjiyue.pojo.Teacher">
            <result property="name" column="tname"></result>
        </association>
    </resultMap>
```

3.去mybatis-config文件注入

4.测试

```java
    //结果嵌套处理
    @Test
    public void testGetStudent2(){
        SqlSession session = Mybatisutils.getSqlSession();
        StudentMapper mapper = session.getMapper(StudentMapper.class);
        List<Student> student2 = mapper.getStudent2();
        System.out.println(student2);
        session.close();
    }
```

#### 小结

按照查询进行嵌套处理就像SQL中的子查询

按照结果进行嵌套处理就像SQL中的联表查询

#### 一对多的处理

- 一个老师拥有多个学生
- 如果对于老师这边，就是一个一对多的现象，即从一个老师下面拥有一群学生（集合）！

##### 实体类编写

```java
@Data
public class Student {
    private int id;
    private String name;
    private int tid;
}
```

```java
@Data
public class Teacher {
    private int id;
    private String name;
    //一个老师拥有多个学生
    private List<Student> students;
}
```

....和之前一样，搭建测试环境！

##### 按结果嵌套处理

1.TeacherMapper接口编写方法

```java
public interface TeacherMapper {
    //获取老师
    List<Teacher> getTeacher();
    //获取指定老师下的所有学生信息及老师信息
    Teacher getTeacher1(@Param("tid") int id);
}
```

2.编写接口对应的Mapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="renjiyue.dao.TeacherMapper">
    <select id="getTeacher" resultType="renjiyue.pojo.Teacher">
        select * from teacher
    </select>
    <select id="getTeacher1" resultMap="teacherStudent">
        select s.id sid,s.name sname,t.name tname,t.id tid from student s,teacher t where s.tid=t.id and t.id=#{tid}
    </select>

    <resultMap id="teacherStudent" type="renjiyue.pojo.Teacher">
        <result property="id" column="tid"/>
        <result property="name" column="tname"/>
        <collection property="students" ofType="renjiyue.pojo.Student">
            <result property="id" column="sid"/>
            <result property="name" column="sname"/>
            <result property="tid" column="tid"/>
        </collection>
    </resultMap>
</mapper>
```

3.将Mapper文件注册到MyBatis-config文件中

```xml
    <mappers>
        <mapper resource="renjiyue/dao/TeacherMapper.xml"/>
    </mappers>

```

4.测试

```java
    @Test
    public void getTeacher2(){
        SqlSession session = Mybatisutils.getSqlSession();
        TeacherMapper mapper = session.getMapper(TeacherMapper.class);
        Teacher teacher1 = mapper.getTeacher1(1);
        System.out.println(teacher1);
        session.close();
    }
```

##### 按查询嵌套处理

1.TeacherMapper接口编写方法

```java
    Teacher getTeacher2(int id);
```

2.编写接口对应的Mapper配置文件

```xml
    <select id="getTeacher2" resultMap="TeacherStudent2">
        select * from teacher where id = #{id}
    </select>

    <resultMap id="TeacherStudent2" type="renjiyue.pojo.Teacher">
        <collection property="students" javaType="ArrayList" ofType="renjiyue.pojo.Student" column="id" select="getStudentByTeacherld"/>
    </resultMap>
    <select id="getStudentByTeacherld" resultType="renjiyue.pojo.Student">
        select * from student where tid = #{id}
    </select>
```

3.将Mapper文件注册到MyBatis-config文件中

4.测试

```java
    @Test
    public void getTeacher3(){
        SqlSession session = Mybatisutils.getSqlSession();
        TeacherMapper mapper = session.getMapper(TeacherMapper.class);
        Teacher teacher2 = mapper.getTeacher2(1);
        System.out.println(teacher2);
        session.close();
    }
```

#### 小结

- 关联-association
- 集合-collection
- 所以association是用于一对一和多对一，而collection是用于一对多的关系
- JavaType和ofType都是用来指定对象类型的
  - JavaType是用于指定pojo中属性的类型
  - ofType指定的映射到List集合属性中的pojo的类型
- 注意说名
  - 保证SQL的可读性，尽量通俗易懂
  - 根据实际要求，尽量编写性能更高的sql语句
  - 注意属性名和字段不一致的问题
  - 注意一对多和多对一中：字段和属性对应的问题
  - 尽量使用log4j,通过日志来查看自己的错误

### MyBatis06:动态SQL

#### 介绍

什么是动态SQL：**动态SQL指的是根据不同的查询条件，生成不同的SQL语句**

```xml
官网描述：
MyBatis 的强大特性之一便是它的动态 SQL。如果你有使用 JDBC 或其它类似框架的经验，你就能体会到根据不同条件拼接 SQL 语句的痛苦。例如拼接时要确保不能忘记添加必要的空格，还要注意去掉列表最后一个列名的逗号。利用动态 SQL 这一特性可以彻底摆脱这种痛苦。
虽然在以前使用动态 SQL 并非一件易事，但正是 MyBatis 提供了可以被用在任意 SQL 映射语句中的强大的动态 SQL 语言得以改进这种情形。
动态 SQL 元素和 JSTL 或基于类似 XML 的文本处理器相似。在 MyBatis 之前的版本中，有很多元素需要花时间了解。MyBatis 3 大大精简了元素种类，现在只需学习原来一半的元素便可。MyBatis 采用功能强大的基于 OGNL 的表达式来淘汰其它大部分元素。

  -------------------------------
  - if
  - choose (when, otherwise)
  - trim (where, set)
  - foreach
  -------------------------------
```

我们之前写的SQL语句都比较简单，如果有比较复杂的业务，我们需要写复杂的SQL语句，往往需要拼接，而拼接sql，稍微不注意，由于引号，空格等缺失可能都会导致错误

那么怎么去解决这个问题，这就要用Mybatis动态SQL,通过if,choose,when,otherwise,trim,where,set,foreach等标签，可组合成非常灵活的SQL语句，从而在提高SQL语句的准确性的同时，也大大提高了开发人员的效率

#### 搭建环境

新建一个数据库

新建一个数据库：blog

字段：id,title,author,create_time,views

```sql
CREATE TABLE `blog` (
`id` varchar(50) NOT NULL COMMENT '博客id',
`title` varchar(100) NOT NULL COMMENT '博客标题',
`author` varchar(30) NOT NULL COMMENT '博客作者',
`create_time` datetime NOT NULL COMMENT '创建时间',
`views` int(30) NOT NULL COMMENT '浏览量'
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

1.创建MyBatis基础工程

2.IDutil工具类

```java
package com.renjiyue.IDTtil;

import java.util.UUID;

public class IDutil {
    public static String genld(){
        return UUID.randomUUID().toString().replaceAll("-","");
    }
}

```

3.实体类编写【注意set方法作用】

```java
package com.renjiyue.pojo;

import java.util.Date;

public class Blog {
    private String id;
    private String title;
    private String author;
    private Date createTime;
    private int views;

    public void setId(String id) {
        this.id = id;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public void setCreateTime(Date createTime) {
        this.createTime = createTime;
    }

    public void setViews(int views) {
        this.views = views;
    }

    public String getId() {
        return id;
    }

    public String getTitle() {
        return title;
    }

    public String getAuthor() {
        return author;
    }

    public Date getCreateTime() {
        return createTime;
    }

    public int getViews() {
        return views;
    }
}

```

4.编写Mapper接口及xml文件

```java
public interface BlogMapper {
    //新增一个博客
    int addBlog(Blog blog);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.renjiyue.dao.BlogMapper">
    <insert id="addBlog" parameterType="com.renjiyue.pojo.Blog">
        insert into blog(id,title,author,create_time,views) value (#{id},#{title},#{author},#{createTime},#{views})
    </insert>
</mapper>
```

5.MyBatis核心配置文件，下划线驼峰自动转换

```xml
<settings>
   <setting name="mapUnderscoreToCamelCase" value="true"/>
   <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
<mappers>
    <mapper resource="com/renjiyue/dao/BlogMapper.xml"/>
</mappers>
```

6.插入初始数据

编写接口

```java
    //新增一个博客
    int addBlog(Blog blog);
```

sql配置文件

```xml
    <insert id="addBlog" parameterType="com.renjiyue.pojo.Blog">
        insert into blog(id,title,author,create_time,views) value (#{id},#{title},#{author},#{createTime},#{views})
    </insert>
```

初始化博客方法

```java
    @Test
    public void addInitBlog(){
        SqlSession sqlSession = Mybatisutils.getSqlSession();
        BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
        Blog blog = new Blog();
        blog.setId(IDutil.genld());
        blog.setTitle("MyBatis如此简单");
        blog.setAuthor("任吉跃");
        blog.setCreateTime(new Date());
        blog.setViews(9999);
        mapper.addBlog(blog);

        sqlSession.close();
    }
```

初始化数据完毕

#### if语句

需求：根据作者名字和博客名字来查询博客，如果作者名子为空，那么只根据博客名字查询，反之，根据作者名来查询

1.编写接口类

```java
    //需求1
    List<Blog> queryBlogif(Map map);
```

2.编写SQL语句

```xml
    <select id="queryBlogif" parameterType="java.util.HashMap" resultType="com.renjiyue.pojo.Blog">
        select * from blog where <if test="title != null">title = #{title}</if><if test="author!=null">and author =#{author}</if>
    </select>
```

3.测试

```java
    @Test
    public void testQueryBlogif(){
        SqlSession session = Mybatisutils.getSqlSession();
        BlogMapper mapper = session.getMapper(BlogMapper.class);
        HashMap<String, String> map = new HashMap<>();
        map.put("title","MyBatis如此简单");
        map.put("author","任吉跃");
        List<Blog> blogs = mapper.queryBlogif(map);
        System.out.println("=========");
        System.out.println(blogs);
        session.close();
    }
```

这样写我们可以看到，如果author等于null,那么查询语句为select * from user where title = #{title} 但是如果title为空呢？那么查询语句为select * from user where and author = #{author}，这是错误的SQL语句，如何解决？请看下面的where语句

#### Where

修改上面的SQL语句

```xml
    <select id="queryBlogif2" parameterType="java.util.HashMap" resultType="com.renjiyue.pojo.Blog">
        select * from blog <where><if test="title != null">title = #{title}</if><if test="author!=null">and author =#{author}</if></where>
    </select>
```

这个”where"标签会知道如果它包含的标签中有返回值的，他就插入一个“where”.此外，如果标签返回的内容是以AND或OR开头，则他会剔除掉。

#### Set

同理，上面的对于查询SQL语句包含where关键字，如果在进行更新操作的时候，含有set关键词，我们怎么处理呢？

1.编写接口方法

```JAVA
    //更新
    int updateBlog(Map map);
```

2.SQL配置文件

```xml
    <update id="updateBlog" parameterType="java.util.HashMap">
        update blog <set><if test="title != null">title = #{title}</if> ,<if test="author!=null">author =#{author}</if></set> where id = #{id}
    </update>
```

3.测试

```java
   @Test
   public void testupdateBlog(){
       SqlSession session = Mybatisutils.getSqlSession();
       BlogMapper mapper = session.getMapper(BlogMapper.class);
       HashMap<String, String> map = new HashMap<String,String>();
       map.put("title","动态SQL");
       map.put("author","胡晓龙");
       map.put("id","shuabi1234");
       mapper.updateBlog(map);
       session.close();
   }
```

#### choose语句

有时候，我们不想用到所有的查询条件，只想选择其中一个，查询条件有一个满足即可，使用choose标签可以解决此类问题，类似于Java的switch语句

1.编写接口方法

```java
    List<Blog> queryBlogChoose(Map map);
```

2.sql配置文件

```xml
    <select id="queryBlogChoose" parameterType="java.util.HashMap" resultType="com.renjiyue.pojo.Blog">
        select * from blog
        <where>
            <choose>
                <when test="title!=null">title=#{title}</when>
                <when test="author!=null">author=#{author}</when>
                <otherwise>and views = #{views}</otherwise>
            </choose>
        </where>
    </select>
```

3.测试类

```java
   @Test
    public void testqueryBlogchoose(){
       SqlSession session = Mybatisutils.getSqlSession();
       BlogMapper mapper = session.getMapper(BlogMapper.class);
       HashMap<String, Object> map = new HashMap<>();
       map.put("title","java如此简单");
       map.put("author","任吉跃");
       map.put("views",9999);
       for (Blog blog : mapper.queryBlogChoose(map)) {
           System.out.println(blog);
       }
       session.close();
   }
```

#### SQL片段

有时候可能某个sql语句我们用的特别多，为了增加代码的重用性，简化代码，我们需要将这些代码抽取出来，然后直接调用

###### 提取SQL片段

```sql
<sql id="if-title-author">
   <if test="title != null">
      title = #{title}
   </if>
   <if test="author != null">
      and author = #{author}
   </if>
</sql>
```

###### 引用sql片段

```xml
<select id="queryBlogIf" parameterType="map" resultType="blog">
  select * from blog
   <where>
       <!-- 引用 sql 片段，如果refid 指定的不在本文件中，那么需要在前面加上 namespace -->
       <include refid="if-title-author"></include>
       <!-- 在这里还可以引用其他的 sql 片段 -->
   </where>
</select>
```

注意：

1. 最好基于单表来定义sql片段，提高片段的可重用性
2. 在sql片段中部要包括where

#### Foreach

将数据库前三个数据id修改为1，2，3

**需求：我们需要查询blog表中id分别为1，2，3的博客信息**

1.编写接口

```java
    List<Blog> queryBlogForeach(Map map);
```

2.编写SQL语句

```xml
    <select id="queryBlogForeach" parameterType="java.util.HashMap" resultType="com.renjiyue.pojo.Blog">
        select * from blog
        <where>
            <foreach collection="ids" item="id" open="and(" close=")" separator="or">
                id=#{id}
            </foreach>
        </where>
    </select>
```

3.测试

```java
   @Test
    public void testQueryBlogForeach(){
       SqlSession session = Mybatisutils.getSqlSession();
       BlogMapper mapper = session.getMapper(BlogMapper.class);
       HashMap map = new HashMap();
       ArrayList<Integer> ids = new ArrayList<Integer>();
       ids.add(1);
       ids.add(2);
       ids.add(3);
       map.put("ids",ids);
       List<Blog> blogs = mapper.queryBlogForeach(map);
       System.out.println(blogs);
       session.close();
   }
```

#### 小结

其实动态sql语句的编写往往就是一个拼接的问题，为了保证拼接准确，我们最好首先要写原生的SQL语句出来，然后在通过mybatis动态sql对照着改，防止出错。多在实践中掌握他的技巧

### MyBatis07:缓存

#### 简介：

什么是缓存【Cache】?

- 存在内存中的临时数据
- 将用户经常查询的数据放在缓存（内存）中，用户去查询数据就不用从磁盘上（关系型数据库文件）查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题

为什么使用缓存？

- 减少和数据库的交互次数，减少系统开销，提高系统效率

什么样的数据能使用缓存？

- 经常查询并且不经常改变的数据

#### MyBatis缓存

- MyBtais包含一个非常强大的查询缓存特性，它可以非常方便的定制和配置缓存。缓存可以极大的提升查询效率
- MyBatis系统中默认定义了两级缓存：**一级缓存和二级缓存**
  - 默认情况下，只有一级缓存开启。（SqlSession级别的缓存，也成为本地缓存）
  - 二级缓存需要手动开启和配置，他是基于namespace级别的缓存
  - 为了提高拓展性，MyBatis定义了缓存接口Cache，我们可以通过实现Cache接口来自定义二级缓存

#### 一级缓存

一级缓存也叫本地缓存：

- 与数据库同一次会话期间查询到的数据会放在本地缓存中。
- 以后如果需要获取相同的数据，直接从缓存中，没必须再去查询数据库

#### 测试

- 在MyBatis中加入日志，方便测试结果
- 编写接口方法

```java
    //根据id查询用户
    User selectUserById(int id);
```

- 接口对应的Mapper文件

```xml
    <select id="selectUserById" resultType="com.renjiyue.pojo.User">
        select * from user where id = #{id}
    </select>
```

- 测试

```java
    @Test
    public void test123(){
        SqlSession session = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        User user = mapper.selectUserById(1);
        System.out.println(user);
        session.close();
    }
```

- 结果分析

![jieguo](C:\Users\000\Desktop\MyBatis\image\2.png)

#### 一级缓存失效的四种情况

一级缓存是SqlSession级别的缓存，是一直开启的，我们关闭不下它

一级缓存失效情况：没有使用到当前的一级缓存，效果就是，还需在想数据库中发起一次查询请求！

1.sqlSession不同

```java
    @Test

    public void testQueryuSERb(){
        SqlSession session = MyBatisUtils.getSqlSession();
        SqlSession session1 = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        UserMapper mapper1 = session1.getMapper(UserMapper.class);

        User user = mapper.selectUserById(1);
        System.out.println(user);

        User user1 = mapper1.selectUserById(1);
        System.out.println(user1);
        System.out.println(user==user1);
        session.close();
        session1.close();
    }
```

观察结果：发现发送了两条SQL语句

结论：**每个sqlSession中的缓存相互独立**

2.sqlSession相同，查询条件不同

```java
    @Test
    public void test123(){
        SqlSession session = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        User user = mapper.selectUserById(1);
        System.out.println(user);
        User user1 = mapper.selectUserById(2);
        System.out.println(user1);
        session.close();
    }
```

观察结果：发现发送了两条SQL语句!很正常的理解

结论：**当前缓存中，不存在这个数据**

3.sqlSession相同，两次查询之间执行了增删改操作！

```java
    @Test
    public void test123(){
        SqlSession session = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        User user = mapper.selectUserById(1);
        System.out.println(user);
        mapper.deleteUser(2);
        User user1 = mapper.selectUserById(1);
        System.out.println(user1);
        session.close();
    }
```

观察结果：查询在中间执行了增删改操作后，重新执行了

结论：**因为增删改操作可能会对当前的数据产生影响**

4.sqlSession相同，手动清除一级缓存

```java
    @Test
    public void test123(){
        SqlSession session = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        User user = mapper.selectUserById(1);
        System.out.println(user);

        session.clearCache();//手动清除缓存
        
        User user1 = mapper.selectUserById(1);
        System.out.println(user1);
        session.close();
    }
```

一级缓存就是一个map

#### 二级缓存

- 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存
- 基于namespace级别的缓存，一个命名空间，对应一个二级缓存
- 工作机制
  - 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中
  - 如果当前会话关闭了，这个会话对应的一级缓存就没了，但是我们想要的是，一级缓存中的数据被保存到二级缓存中
  - 新的会话查询信息，就可以从二级缓存中获取内容
  - 不同的mapper查出的数据就会放在自己对应的缓存（map）中

##### 使用步骤

1.开启全局缓存【mybatis-config.xml】

```xml
<setting name="cacheEnabled" value="true"/>
```

2.去每个mapper.xml中配置使用二级缓存，这个配置非常简单，【xxxMapper.xml】

```xml
官方示例=====>查看官方文档
<cache
 eviction="FIFO"
 flushInterval="60000"
 size="512"
 readOnly="true"/>
这个更高级的配置创建了一个 FIFO 缓存，每隔 60 秒刷新，最多可以存储结果对象或列表的 512 个引用，而且返回的对象被认为是只读的，因此对它们进行修改可能会在不同线程中的调用者产生冲突。
```

3.代码测试

- 所有的实体类先实现序列接口
- 测试代码

```java
    @Test
    public void testQueryuSERb(){
        SqlSession session = MyBatisUtils.getSqlSession();
        SqlSession session1 = MyBatisUtils.getSqlSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        UserMapper mapper1 = session1.getMapper(UserMapper.class);

        User user = mapper.selectUserById(1);
        System.out.println(user);
        session.close();

        User user1 = mapper1.selectUserById(1);
        System.out.println(user1);
        System.out.println(user==user1);
        session1.close();
    }
```

##### 结论

- 只要开启了二级缓存，我们在同一个Mapper中查询，可以在二级缓存中拿到数据
- 查出的数据都会被默认有限放在一级缓存中
- 只有会话提交或者关闭以后，一级缓存中的是数据才会转到二级缓存中

### 缓存原理图

![cache](C:\Users\000\Desktop\MyBatis\image\cache.png)

### EhCache

第三方缓存实现-EhCache:查看百度百科

Ehcache是一种广泛使用的java分布式缓存，用于通用缓存；

要在应用程序中使用EhCache，需要引入依赖的jar包

```xml
<!-- https://mvnrepository.com/artifact/org.ehcache/ehcache -->
<dependency>
    <groupId>org.ehcache</groupId>
    <artifactId>ehcache</artifactId>
    <version>3.10.0</version>
</dependency>
```

在mapper.xml中使用对应的缓存即可

```xml
<mapper namespace = “org.acme.FooMapper” >
   <cache type = “org.mybatis.caches.ehcache.EhcacheCache” />
</mapper>
```

编写ehcache.xml文件，如果在加载是未找到/ehcach.xml资源或出现问题，则使用默认配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
        updateCheck="false">
   <!--
      diskStore：为缓存路径，ehcache分为内存和磁盘两级，此属性定义磁盘的缓存位置。参数解释如下：
      user.home – 用户主目录
      user.dir – 用户当前工作目录
      java.io.tmpdir – 默认临时文件路径
    -->
   <diskStore path="./tmpdir/Tmp_EhCache"/>
   
   <defaultCache
           eternal="false"
           maxElementsInMemory="10000"
           overflowToDisk="false"
           diskPersistent="false"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="259200"
           memoryStoreEvictionPolicy="LRU"/>

   <cache
           name="cloud_user"
           eternal="false"
           maxElementsInMemory="5000"
           overflowToDisk="false"
           diskPersistent="false"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="1800"
           memoryStoreEvictionPolicy="LRU"/>
   <!--
      defaultCache：默认缓存策略，当ehcache找不到定义的缓存时，则使用这个缓存策略。只能定义一个。
    -->
   <!--
     name:缓存名称。
     maxElementsInMemory:缓存最大数目
     maxElementsOnDisk：硬盘最大缓存个数。
     eternal:对象是否永久有效，一但设置了，timeout将不起作用。
     overflowToDisk:是否保存到磁盘，当系统当机时
     timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
     timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。最大时间介于创建时间和失效时间之间。仅当eternal=false对象不是永久有效时使用，默认是0.，也就是对象存活时间无穷大。
     diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.
     diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
     diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
     memoryStoreEvictionPolicy：当达到maxElementsInMemory限制时，Ehcache将会根据指定的策略去清理内存。默认策略是LRU（最近最少使用）。你可以设置为FIFO（先进先出）或是LFU（较少使用）。
     clearOnFlush：内存数量最大时是否清除。
     memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。
     FIFO，first in first out，这个是大家最熟的，先进先出。
     LFU， Less Frequently Used，就是上面例子中使用的策略，直白一点就是讲一直以来最少被使用的。如上面所讲，缓存的元素有一个hit属性，hit值最小的将会被清出缓存。
     LRU，Least Recently Used，最近最少使用的，缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
  -->
</ehcache>
```

合理使用缓存，可以让我们程序的性能大大提升

