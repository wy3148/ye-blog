---
title: Understand bean on spring framework 1- by xml file
date: 2020-08-24 22:44:22
tags:
---


## Dependancy about spring-framework ( not spring-boot)


It's just spring framwork (not springboot, we only need to include some necessary packages)

```java
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-core -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>5.2.8.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.2.8.RELEASE</version>
    </dependency>

```


## Define xml file for beans


create xml at  'src/main/resources/beans.xml'


<bean> has supported several different tags

	
```java

<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="
http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="iwang" class="com.mycompany.app.User">
        <property name="name" value="iwang"/>
         <property name="car" ref = "car1" />
    </bean>

    <bean id="car1" class="com.mycompany.app.Car">
        <property name="name" value="bmw"/>

        <property name="session">
            <map>
                <entry key="uid" value="9527"/>
                <entry key="username" value="Jack"/>
                <entry key="password" value="1234"/>
            </map>
        </property>

    </bean>

</beans>

```
in above xml, User class refer the 'car' as a private variable.


## Define User and Car Class

After we define xml file, we need to implement Bean classes, User and Car

```java
package com.mycompany.app;

import javax.swing.plaf.synth.SynthTextAreaUI;

public class User {
    private String name;
    private Car car;

    public User(){
    }

    public String getName(){return this.name;}
    public void setName(String n){ this.name = n;}
    public void setCar(Car c){
        System.out.println("got setcar:" + c.getName());
        this.car = c;
    }
    public Car getCar(){return this.car;}
    public void show(){
        this.car.drive();
    }
}
```

```java
package com.mycompany.app;

import java.util.HashMap;

public class Car {
    private String name;

    private HashMap<String, String> session;

    public Car(){};

    public void setName(String name){
        System.out.println("set named:" + name);
        this.name = name;
    }
    public String getName(){
        return this.name;
    }

    public void setSession( HashMap<String, String> s ){
        this.session = s;
    }

    public HashMap<String, String> getSession() {
        return this.session;
    }
    
    public void drive(){
        System.out.println("starting drive now!" + this.session.get("username"));

    }
}
```


## Load and test beans.xml


```java
package com.mycompany.app;

import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.context.ApplicationContext;


public class App 
{
    public static void main( String[] args ) {
        ApplicationContext  applicationContext  =  new ClassPathXmlApplicationContext("beans.xml");
        User u = (User) applicationContext.getBean("iwang");
        u.show();
    }
}
```

By default, bean factory create singleton instance of class defined in bean.


