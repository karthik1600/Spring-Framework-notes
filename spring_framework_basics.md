# spring features
> Inversion of Control? 
- Inversion of Control is a principle in software engineering which transfers the control of objects or portions of a program to a container or framework. We most often use it in the context of object-oriented programming.

- We can achieve Inversion of Control through various mechanisms such as: Strategy design pattern, Service Locator pattern, Factory pattern, and Dependency Injection (DI).

- Dependency Injection?
    - Dependency injection is a pattern we can use to implement IoC, where the control being inverted is setting an object’s dependencies.
    - Connecting objects with other objects, or “injecting” objects into other objects, is done by an assembler rather than by the objects themselves.

---

## Dependency injection in Java
- add dependency in pom file
>pom.xml
```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.3.14</version>
        </dependency>
    </dependencies>
```
---
- The Spring Framework core is, simply put, an IoC container used to manage beans.
- There are two basic types of containers in Spring – the Bean Factory and the Application Context.
- The former provides basic functionalities, which are introduced here; the latter is a superset of the former and is most widely used.

## Application context
- we first create a spring.xml file where we add the beans we need
- we use `Application context` to get create the context
- we `getBeans` of the context to get the bean fron the container
> spring.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id = "doctor" class="demo.Doctor"></bean>
</beans>
```
> Main
```java
public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");
        Doctor doc = context.getBean(Doctor.class);
        doc.assist();
    }
}
```
- we have in the above code used `ClassPathXmlApplicationContext`
- if by any chance DOctor has properties we can add default values using property inside bean of Doctor,but u need to make sure Doctor has getters and setters
```xml
    <bean id = "doctor" class="demo.Doctor">
        <property name="qualification" value="MBBS"></property>
    </bean>
```
- if the property is a class which is a bean then we can use ref which thwn injects the entire object to class
```xml
    <bean id = "doctor" class="demo.Doctor">
        <property name="qualification" value="MBBS"></property>
        <property name="nurse" ref="nurse"></property> ref wants the id of Nurse
    </bean>
    <bean id ="nurse" class="demo.Nurse"></bean>
```
- we can also use connstructors for class instead of getters and setters then we use constructor-arg
```xml
    <bean id = "doctor" class="demo.Doctor">
        <constructor-arg value="MBBS" ></constructor-arg>
    </bean>
```
- we can get bean using the id but then we need to cast it too then
```java
Doctor doc = (Doctor)context.getBean("doctor");
```
---
# Annotation based configuration
- we use `@Component` to tell whichh class is to made into a bean
- we use `context:component-scan` to tell sppring whichh package it need to search to create beans
```java
@Component
public class Doctor {
    public void assist(){
        System.out.println("doc is assisting");
    }
}
```
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="demo"></context:component-scan> //important
```
---
## Java Based Configuration
- create a BeanConfig class
- in that add `@Configuration` or `ComponentScan`
- we use @Component to tell which class is to be made into a bean
- now we use `AnnotationConfigApplicationContext`
```java
@Configuration
@ComponentScan(basePackages = "demo")
public class BeanConfig {
}
```
```java
public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(BeanConfig.class); ///
        Doctor doc = context.getBean(Doctor.class);
        doc.assist();
    }
}
```
we can also create a bean inside `BeanConfig` and remove the `@component` in all the bean classes
```java
@Configuration
//@ComponentScan(basePackages = "demo")
public class BeanConfig {
    @Bean
    public Doctor doctor(){
        return new Doctor();
    }
}
```
---
## Spring bean scopes
- by default scope is singleton so we always get the same object 
- to get different objects we use `prootype` as scope
- there are 5 scopes available - [link](https://www.tutorialspoint.com/spring/spring_bean_scopes.htm)
```java
@Configuration
@ComponentScan(basePackages = "demo")
public class BeanConfig {

}
```
```java
@Component
@Scope(scopeName = "prototype")
public class Doctor {
    private String qualification;

    @Override
    public String toString() {
        return "Doctor{" +
                "qualification='" + qualification + '\'' +
                '}';
    }

    public void assist(){
        System.out.println("doc is assisting");
    }

    public void setQualification(String qualification) {
        this.qualification = qualification;
    }
}
```
- if we didnt add prototype as scope any change done by any object will change the state of all other objects as ll hold the same reference
