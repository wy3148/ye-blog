---
layout: beginning level of jpa and mybatis
title: beginning of mybatis vs java jpa
date: 2020-08-17 20:40:10
tags:
---

Try to learn about how to use java to support persistence,

##mybatis


```java
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.5</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.26</version>
        </dependency>

```

in pom.xml, need to configure the default location of source (xml) files,

```java
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                    <include>**/**/*.xml</include>
                    <include>**/*.properties</include>
                </includes>
            </resource>
        </resources>
```


configure the default xml file, mybatis-config.xml


```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost"/>
        <property name="username" value="test"/>
        <property name="password" value="test1234"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="mappers/UserMapper.xml"/>
  </mappers>
</configuration>
```

##Define User class
this represent the 'user' table in db,

```java
package com.mycompany.app;

public class User {
    public int id;
    public int customerId;
    public String email;
    public String password;

    public void setEmail(String e){
        this.email = e;
    }
    public String getEmail(){
        return this.email;
    }
    public void setPassword(String pwd){
        this.password= pwd;
    }
    public String getPassword(){return this.password;}

    public void setCustomerId(int id){
        this.customerId = id;
    }
    public int getCustomerId(){
        return this.customerId;
    }

    public User(String e, int customer, String pwd){
        super();
        this.customerId = customer;
        this.email = e;
        this.password = pwd;
    }
    public User(){
        super();
    }
}
```

UserMapper is an interface being defined,

```java
public interface UserMapper {
    public void insertUser(User user);
    public User getUser(int id);
    public User getUserByEmail(String e);
}
```


```java

 		//how to open a new session

        String resource = "mybatis-config.xml";
        try {
            InputStream inputStream = Resources.getResourceAsStream(resource);
            db.sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        }catch (IOException e){
            System.out.println(e);
        }

        SqlSession sqlSession = this.sqlSessionFactory.openSession();
        try {
            UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
            User user = new User("bttt@gmail.com", 17600886,"wwewewews");
            userMapper.insertUser(user);
            sqlSession.commit();
        } finally {
            sqlSession.close();
        }
```


The 'interface' defined in UserMapper relies on the UserMapper.xml file

'insertUser'
'getUser'
'getUserByEmail'

in each setction, define the SQL query,

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mycompany.app.UserMapper">
    <!-- 这里namespace必须是UserMapper接口的路径” -->
    <insert id="insertUser" parameterType="com.mycompany.app.User">
        insert into user(email,customer_id,password) values(#{email},#{customerId},#{password})
        <!-- 这里sql结尾不能加分号，否则报“ORA-00911”的错误 -->
    </insert>

    <!-- 这里的id必须和UserMapper接口中的接口方法名相同 -->
    <select id="getUser" resultType="com.mycompany.app.User" parameterType="java.lang.Integer">
        select * from user where id=#{id}
    </select>

    <select id="getUserByEmail" resultType="com.mycompany.app.User" parameterType="java.lang.String">
        select * from user where email=#{email}
    </select>
</mapper>
```

xml files path

```java
<!-- Using classpath relative resources -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
<!-- Using url fully qualified paths -->
<mappers>
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
```



##References
xml mapping

https://mybatis.org/mybatis-3/sqlmap-xml.html



##Spring-data-jpa

configuration of mysql, spring-jpa doesn't reqiure to write SQL most of time,
this is most important difference with mybatis.


```java
spring.jpa.hibernate.ddl-auto=update
spring.datasource.url=jdbc:mysql://${MYSQL_HOST:localhost}:3306/db_example?serverTimezone=Australia/Sydney
spring.datasource.username=springuser
spring.datasource.password=ThePassword
debug=true
```


Define an Entity , table in database.
```java
package com.example.accessingdatamysql;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity // This tells Hibernate to make a table out of this class
public class User {
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Integer id;

    private String name;

    private String email;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}

```

Define interface for CRUD Repository, spring will automatically implement the interface

```java

// This will be AUTO IMPLEMENTED by Spring into a Bean called userRepository
// CRUD refers Create, Read, Update, Delete

public interface UserRepository extends JpaRepository<User, Integer> {

    public User getUserByEmail(String email);

//    @Query(value = "SELECT * FROM user WHERE name like concat('%',?1,'%') ",nativeQuery = true)
//    @Query(value = "SELECT * FROM user WHERE name like %?% ",nativeQuery = true)
    //So spring-data-jpa can also support native SQL query, but should try to avoid that
    public Iterable<User> getUserByName(String name);
}
```

