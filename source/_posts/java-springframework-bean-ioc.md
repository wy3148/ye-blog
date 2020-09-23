---
title: IOC of bean in spring framework
date: 2020-08-29 00:48:15
tags: 
---


###Basic part of refelection

java.lang.Class;

The refelction of java is mainly from java.lang.Class;

Class has a lot of useful methods,


```java

//each class, we can get the Constructor 

    @CallerSensitive
    public Constructor<?>[] getConstructors() throws SecurityException {
        SecurityManager sm = System.getSecurityManager();
        if (sm != null) {
            checkMemberAccess(sm, Member.PUBLIC, Reflection.getCallerClass(), true);
        }
        return copyConstructors(privateGetDeclaredConstructors(true));
    }


```

And constructor has the function

```java
    @CallerSensitive
    @ForceInline // to ensure Reflection.getCallerClass optimization
    public T newInstance(Object ... initargs)
        throws InstantiationException, IllegalAccessException,
               IllegalArgumentException, InvocationTargetException

```


## Example of a class

```java

//Define a class of User,
class User{
	
    public User(){
        System.out.println("User construct is called");
    }
}

	//Define a className, string variable is the a class whole path(class name)
	//create a new user instance,
    String className = "com.mycompany.app.User";
    try {
        Object u = Class.forName(className).getConstructor().newInstance();
        //it's like User u = new User();
    } catch (Exception e) {
        System.out.println(e);
    }

```

springframework-bean load the xml file, and eventually create instance of class object.





##Use the annotation (no xml) to implement bean in framework


Firstly use @Configuration annotation to represent  this class as configuration class,

```java
package com.mycompany.app;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Scope;
import org.springframework.beans.factory.config.BeanDefinition;

@Configuration
public class UserConfiguration {

//Method is Scope, for method only
//@Target({ElementType.TYPE, ElementType.METHOD})
//@Retention(RetentionPolicy.RUNTIME)
//@Documented


	//Class instance is by default the singleton instance,
	//but we can use Scop annoation to change to mulitple instances
	//SCOPE_SINGLETON
	//SCOPE_PROTOTYPE

    @Scope(BeanDefinition.SCOPE_SINGLETON)
    @Bean
    public User createUser() {
        return new User();
    }

    @Bean(initMethod="cleanup")
    public void clean(){
        System.out.println("cleanup is called");
    }

    @Scope(BeanDefinition.SCOPE_PROTOTYPE)
    @Bean
    public Device createDevice(String addr){
        Device d =  new Device();
        d.setDeviceAddr(addr);
        return d;
    }
}
```


Define the actual bean class User, use the @Component annotation,

```java
package com.mycompany.app;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.context.annotation.Bean;

@Component
public class User{
    private String name;
    private String email;
    public User(){
        System.out.println("User construct is called");
    }

    public void setName(String name){this.name = name;}
    public String getName(){return this.name;}
    public void setEmail(String e){
        this.email = e;
    }
    public String getEmail(){return this.email;}

    public void show(){
        System.out.println(this.name + ":" + this.email);
    }

    public void close(){
        System.out.println(this.name + "is destroyed now");
    }
}

```

in the main funciton, to get the User instance, we use AnnotationConfigApplicationContext,
as we don't load beans from xml file, we don't need to use xml class ClassPathXmlApplicationContext,


```java
       ApplicationContext context = new AnnotationConfigApplicationContext(UserConfiguration.class);
       User bean1 = context.getBean("createUser",User.class);
       System.out.println(bean1);
       bean1.show();
```
