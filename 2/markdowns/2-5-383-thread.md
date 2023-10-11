
# Post \#49088847 [Link](https://stackoverflow.com/questions/49088847/)

## After Spring Boot 2.0 migration: jdbcUrl is required with driverClassName

**Vote**: 107 (56/702) **Views**: 206097 (36/702) 

**Internal ID** \#2-5-383

Created at 2018-03-03 20:22:26

Tags: `java` `spring` `spring-boot`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------


My solution: I delete dataSource() method from DatabaseConfig.java. Then, the application was started successfully :)

I've just updated my  project from  to . Before the update, this application works properly but after the update, I'm getting some errors are below. What is the problem, can you help me?

I use PostgreSQL, Hibernate, JPA in the project.

I've tried also [this](https://stackoverflow.com/questions/45449745/sprint-boot-with-jpa-error-creating-bean-name-entitymanagerfactory-jdbcurl) but it doesn't work for me.



Here is problems:

```
2018-03-03 23:19:37.934 ERROR 42323 --- [           main] com.zaxxer.hikari.HikariConfig           : HikariPool-1 - dataSource or dataSourceClassName or jdbcUrl is required.
2018-03-03 23:19:37.938  WARN 42323 --- [           main] ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Unsatisfied dependency expressed through method 'entityManagerFactory' parameter 0; nested exception is org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'entityManagerFactoryBuilder' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Unsatisfied dependency expressed through method 'entityManagerFactoryBuilder' parameter 0; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'jpaVendorAdapter' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.springframework.orm.jpa.JpaVendorAdapter]: Factory method 'jpaVendorAdapter' threw exception; nested exception is java.lang.IllegalArgumentException: dataSource or dataSourceClassName or jdbcUrl is required.
2018-03-03 23:19:37.939  INFO 42323 --- [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
2018-03-03 23:19:37.954  INFO 42323 --- [           main] ConditionEvaluationReportLoggingListener : 

Error starting ApplicationContext. To display the conditions report re-run your application with 'debug' enabled.
2018-03-03 23:19:37.963 ERROR 42323 --- [           main] o.s.boot.SpringApplication               : Application run failed

org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Unsatisfied dependency expressed through method 'entityManagerFactory' parameter 0; nested exception is org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'entityManagerFactoryBuilder' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Unsatisfied dependency expressed through method 'entityManagerFactoryBuilder' parameter 0; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'jpaVendorAdapter' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.springframework.orm.jpa.JpaVendorAdapter]: Factory method 'jpaVendorAdapter' threw exception; nested exception is java.lang.IllegalArgumentException: dataSource or dataSourceClassName or jdbcUrl is required.
    at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:729) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.ConstructorResolver.instantiateUsingFactoryMethod(ConstructorResolver.java:470) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.instantiateUsingFactoryMethod(AbstractAutowireCapableBeanFactory.java:1250) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1099) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:545) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:502) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:312) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:228) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:310) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:200) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.context.support.AbstractApplicationContext.getBean(AbstractApplicationContext.java:1085) ~[spring-context-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:858) ~[spring-context-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:549) ~[spring-context-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:140) ~[spring-boot-2.0.0.RELEASE.jar:2.0.0.RELEASE]
    at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:752) [spring-boot-2.0.0.RELEASE.jar:2.0.0.RELEASE]
    at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:388) [spring-boot-2.0.0.RELEASE.jar:2.0.0.RELEASE]
    at org.springframework.boot.SpringApplication.run(SpringApplication.java:327) [spring-boot-2.0.0.RELEASE.jar:2.0.0.RELEASE]
    at org.springframework.boot.SpringApplication.run(SpringApplication.java:1246) [spring-boot-2.0.0.RELEASE.jar:2.0.0.RELEASE]
    at org.springframework.boot.SpringApplication.run(SpringApplication.java:1234) [spring-boot-2.0.0.RELEASE.jar:2.0.0.RELEASE]
    at com.Test.Test.TestApplication.main(TestApplication.java:17) [classes/:na]
Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'entityManagerFactoryBuilder' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Unsatisfied dependency expressed through method 'entityManagerFactoryBuilder' parameter 0; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'jpaVendorAdapter' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.springframework.orm.jpa.JpaVendorAdapter]: Factory method 'jpaVendorAdapter' threw exception; nested exception is java.lang.IllegalArgumentException: dataSource or dataSourceClassName or jdbcUrl is required.
    at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:729) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.ConstructorResolver.instantiateUsingFactoryMethod(ConstructorResolver.java:470) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.instantiateUsingFactoryMethod(AbstractAutowireCapableBeanFactory.java:1250) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1099) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:545) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:502) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:312) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:228) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:310) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:200) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.config.DependencyDescriptor.resolveCandidate(DependencyDescriptor.java:251) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.doResolveDependency(DefaultListableBeanFactory.java:1138) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.resolveDependency(DefaultListableBeanFactory.java:1065) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.ConstructorResolver.resolveAutowiredArgument(ConstructorResolver.java:815) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:721) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    ... 19 common frames omitted
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'jpaVendorAdapter' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.springframework.orm.jpa.JpaVendorAdapter]: Factory method 'jpaVendorAdapter' threw exception; nested exception is java.lang.IllegalArgumentException: dataSource or dataSourceClassName or jdbcUrl is required.
    at org.springframework.beans.factory.support.ConstructorResolver.instantiateUsingFactoryMethod(ConstructorResolver.java:587) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.instantiateUsingFactoryMethod(AbstractAutowireCapableBeanFactory.java:1250) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1099) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:545) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:502) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:312) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:228) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:310) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:200) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.config.DependencyDescriptor.resolveCandidate(DependencyDescriptor.java:251) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.doResolveDependency(DefaultListableBeanFactory.java:1138) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.resolveDependency(DefaultListableBeanFactory.java:1065) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.ConstructorResolver.resolveAutowiredArgument(ConstructorResolver.java:815) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:721) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    ... 33 common frames omitted
Caused by: org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.springframework.orm.jpa.JpaVendorAdapter]: Factory method 'jpaVendorAdapter' threw exception; nested exception is java.lang.IllegalArgumentException: dataSource or dataSourceClassName or jdbcUrl is required.
    at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:185) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.beans.factory.support.ConstructorResolver.instantiateUsingFactoryMethod(ConstructorResolver.java:579) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    ... 46 common frames omitted
Caused by: java.lang.IllegalArgumentException: dataSource or dataSourceClassName or jdbcUrl is required.
    at com.zaxxer.hikari.HikariConfig.validate(HikariConfig.java:1063) ~[HikariCP-2.7.8.jar:na]
    at com.zaxxer.hikari.HikariDataSource.getConnection(HikariDataSource.java:109) ~[HikariCP-2.7.8.jar:na]
    at org.springframework.jdbc.datasource.DataSourceUtils.fetchConnection(DataSourceUtils.java:151) ~[spring-jdbc-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.jdbc.datasource.DataSourceUtils.doGetConnection(DataSourceUtils.java:115) ~[spring-jdbc-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.jdbc.datasource.DataSourceUtils.getConnection(DataSourceUtils.java:78) ~[spring-jdbc-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.jdbc.support.JdbcUtils.extractDatabaseMetaData(JdbcUtils.java:318) ~[spring-jdbc-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.jdbc.support.JdbcUtils.extractDatabaseMetaData(JdbcUtils.java:355) ~[spring-jdbc-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.boot.autoconfigure.orm.jpa.DatabaseLookup.getDatabase(DatabaseLookup.java:72) ~[spring-boot-autoconfigure-2.0.0.RELEASE.jar:2.0.0.RELEASE]
    at org.springframework.boot.autoconfigure.orm.jpa.JpaProperties.determineDatabase(JpaProperties.java:168) ~[spring-boot-autoconfigure-2.0.0.RELEASE.jar:2.0.0.RELEASE]
    at org.springframework.boot.autoconfigure.orm.jpa.JpaBaseConfiguration.jpaVendorAdapter(JpaBaseConfiguration.java:111) ~[spring-boot-autoconfigure-2.0.0.RELEASE.jar:2.0.0.RELEASE]
    at org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaConfiguration$$EnhancerBySpringCGLIB$$738c1df5.CGLIB$jpaVendorAdapter$3(<generated>) ~[spring-boot-autoconfigure-2.0.0.RELEASE.jar:2.0.0.RELEASE]
    at org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaConfiguration$$EnhancerBySpringCGLIB$$738c1df5$$FastClassBySpringCGLIB$$beb7cb58.invoke(<generated>) ~[spring-boot-autoconfigure-2.0.0.RELEASE.jar:2.0.0.RELEASE]
    at org.springframework.cglib.proxy.MethodProxy.invokeSuper(MethodProxy.java:228) ~[spring-core-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.context.annotation.ConfigurationClassEnhancer$BeanMethodInterceptor.intercept(ConfigurationClassEnhancer.java:361) ~[spring-context-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    at org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaConfiguration$$EnhancerBySpringCGLIB$$738c1df5.jpaVendorAdapter(<generated>) ~[spring-boot-autoconfigure-2.0.0.RELEASE.jar:2.0.0.RELEASE]
    at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:na]
    at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:na]
    at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:na]
    at java.base/java.lang.reflect.Method.invoke(Method.java:564) ~[na:na]
    at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:154) ~[spring-beans-5.0.4.RELEASE.jar:5.0.4.RELEASE]
    ... 47 common frames omitted


Process finished with exit code 1
```


Here is my pom.xml:

```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.0.RELEASE</version>
</parent>

<properties>
     <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
       <java.version>1.9</java.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-properties-migrator</artifactId>
    </dependency>
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
    </dependency>
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
    </dependency>
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.7.0</version>
    </dependency>
    <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.7.0</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-core</artifactId>
    </dependency>
    <dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-entitymanager</artifactId>
    </dependency>
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-mail</artifactId>
            <version>1.4.3.RELEASE</version>
    </dependency>
</dependencies>
```


Here is my application.yml:

```
spring:
  application:
    name: Test

  jpa:
    hibernate:
      ddl-auto: update
      use-new-id-generator-mappings: true
    generate-ddl: true
    properties:
      dialect: org.hibernate.dialect.PostgreSQLDialect

  session:
    store-type: none

  datasource:
    driverClassName: org.postgresql.Driver
    url: jdbc:postgresql://localhost:5432/test
    username: test
    password: 1234

    tomcat:
      max-idle: 2
      max-active: 3
    type: com.zaxxer.hikari.HikariDataSource
```




My DatabaseConfig.java:

```
@Configuration
public class DatabaseConfig {
    @Bean
    @Primary
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource dataSource() { return DataSourceBuilder.create().build(); }

    @Bean
    public SimpleMailMessage templateSimpleMessage() {
        SimpleMailMessage message = new SimpleMailMessage();
        message.setText(
                "This is the test email template for your email:\n%s\n");
        return message;
    }
}
```



----------
        
## Answer \#0

**Accepted** Vote: 67

Created at 2018-03-06 02:48:04

------------

In case you do need to define `dataSource()`, for example when you have multiple data sources, you can use:
```
@Autowired Environment env;

@Primary
@Bean
public DataSource customDataSource() {

    DriverManagerDataSource dataSource = new DriverManagerDataSource();
    dataSource.setDriverClassName(env.getProperty("custom.datasource.driver-class-name"));
    dataSource.setUrl(env.getProperty("custom.datasource.url"));
    dataSource.setUsername(env.getProperty("custom.datasource.username"));
    dataSource.setPassword(env.getProperty("custom.datasource.password"));

    return dataSource;

}
```

By setting up the `dataSource` yourself (instead of using `DataSourceBuilder`), it fixed my problem which you also had.
The always knowledgeable [Baeldung](http://www.baeldung.com/spring-data-jpa-multiple-databases) has a tutorial which explains in depth.


------------
    
    
## Answer \#1

 Vote: 175

Created at 2018-03-06 23:11:18

------------

As this post gets a bit of popularity I edited it a bit.
Spring Boot 2.x.x changed [default](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-connect-to-production-database) JDBC connection pool from Tomcat to faster and better HikariCP. Here comes incompatibility, because HikariCP uses different property of jdbc url. There are two ways how to handle it:



There is very good explanation and workaround in spring [docs](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#howto-configure-a-datasource):

Also, if you happen to have Hikari on the classpath, this basic setup does not work, because Hikari has no url property (but does have a jdbcUrl property). In that case, you must rewrite your configuration as follows:

```
app.datasource.jdbc-url=jdbc:mysql://localhost/test
app.datasource.username=dbuser
app.datasource.password=dbpass
```




There is also how-to in the docs how to get it working from "both worlds". It would look like below. ConfigurationProperties bean would do "conversion" for `jdbcUrl` from `app.datasource.url`

```
@Configuration
public class DatabaseConfig {
    @Bean
    @ConfigurationProperties("app.datasource")
    public DataSourceProperties dataSourceProperties() {
        return new DataSourceProperties();
    }

    @Bean
    @ConfigurationProperties("app.datasource")
    public HikariDataSource dataSource(DataSourceProperties properties) {
        return properties.initializeDataSourceBuilder().type(HikariDataSource.class)
                .build();
    }
}
```



------------
    
    
## Answer \#2

 Vote: 80

Created at 2018-08-21 14:24:34

------------

This happened to me because I was using:

```
app.datasource.url=jdbc:mysql://localhost/test
```


When I replaced url by `jdbc-url` then it worked:

```
app.datasource.jdbc-url=jdbc:mysql://localhost/test
```



------------
    
    
## Answer \#3

 Vote: 13

Created at 2018-04-22 19:55:40

------------

I also read the Spring docs, as [lapkritinis](https://stackoverflow.com/users/1182648/lapkritinis) suggested - and luckily this brought me on the right path! But I don´t think, that the Spring docs explain this good right now. At least for me, they aren´t consistent IMHO.

The original problem/question is on what to do, if you upgrade an existing Spring Boot 1.5.x application to 2.0.x, which is using PostgreSQL/Hibernate. The main reason, you get your described error, is [that Spring Boot 2.0.x uses HikariCP instead of Tomcat JDBC pooling DataSource as a default](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide#configuring-a-datasource) - and Hikari´s DataSource doesn´t know the `spring.datasource.url` property, instead it want´s to have `spring.datasource.jdbc-url` ([lapkritinis](https://stackoverflow.com/users/1182648/lapkritinis) also pointed that out).

So far so good. BUT the docs also suggest - and that´s the problem here - that Spring Boot uses `spring.datasource.url` to determine, [if the - often locally used - embedded Database like H2 has to back off and instead use a production Database](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-connect-to-production-database):

> You should at least specify the URL by setting the
  spring.datasource.url property. Otherwise, Spring Boot tries to
  auto-configure an embedded database.

You may see the dilemma. If you want to have your embedded DataBase like you´re used to, you have to switch back to Tomcat JDBC.  To get your existing application working after the Spring Boot 1.5.x --> 2.0.x upgrade with PostgreSQL, just add `tomcat-jdbc` as a dependency to your pom.xml:

```
<dependency>
        <groupId>org.apache.tomcat</groupId>
        <artifactId>tomcat-jdbc</artifactId>
    </dependency>
```


And then configure Spring Boot to use it accordingly inside application.properties:

```
spring.datasource.type=org.apache.tomcat.jdbc.pool.DataSource
```


Hope to help some folks with this, was quite a time consuming problem. I also hope my beloved Spring folks update the docs - and the way new Hikari pool is configured - to get a more consistent Spring Boot user experience :)


------------
    
    
## Answer \#4

 Vote: 9

Created at 2019-08-17 08:49:23

------------

This worked for me.

`application.properties`, used  instead of :

```
datasource.apidb.jdbc-url=jdbc:mysql://localhost:3306/apidb?useSSL=false
datasource.apidb.username=root
datasource.apidb.password=123
datasource.apidb.driver-class-name=com.mysql.jdbc.Driver
```


Configuration class:

```
@Configuration
@EnableJpaRepositories(
        entityManagerFactoryRef = "fooEntityManagerFactory",
        basePackages = {"com.buddhi.multidatasource.foo.repository"}
)
public class FooDataSourceConfig {

    @Bean(name = "fooDataSource")
    @ConfigurationProperties(prefix = "datasource.foo")
    public HikariDataSource dataSource() {
        return DataSourceBuilder.create().type(HikariDataSource.class).build();
    }

    @Bean(name = "fooEntityManagerFactory")
    public LocalContainerEntityManagerFactoryBean fooEntityManagerFactory(
            EntityManagerFactoryBuilder builder,
            @Qualifier("fooDataSource") DataSource dataSource
    ) {
        return builder
                .dataSource(dataSource)
                .packages("com.buddhi.multidatasource.foo.model")
                .persistenceUnit("fooDb")
                .build();
    }
}
```



------------
    
    
## Answer \#5

 Vote: 8

Created at 2018-09-15 20:18:58

------------


### Configure Two DataSources in Spring Boot 2.0.* or above



If you need to configure multiple data sources, you have to mark one of the DataSource instances as `@Primary`, because various auto-configurations down the road expect to be able to get one by type.

If you create your own DataSource, the auto-configuration backs off. In the following example, we provide the exact same feature set as the auto-configuration provides on the primary data source:



```
@Bean
@Primary
@ConfigurationProperties("app.datasource.first")
public DataSourceProperties firstDataSourceProperties() {
    return new DataSourceProperties();
}

@Bean
@Primary
@ConfigurationProperties("app.datasource.first")
public DataSource firstDataSource() {
    return firstDataSourceProperties().initializeDataSourceBuilder().build();
}

@Bean
@ConfigurationProperties("app.datasource.second")
public BasicDataSource secondDataSource() {
    return DataSourceBuilder.create().type(BasicDataSource.class).build();
}
```


> `firstDataSourceProperties` has to be flagged as `@Primary` so that the database initializer feature uses your copy (if you use the initializer).

`application.propoerties`

```
app.datasource.first.url=jdbc:oracle:thin:@localhost/first
app.datasource.first.username=dbuser
app.datasource.first.password=dbpass
app.datasource.first.driver-class-name=oracle.jdbc.OracleDriver

app.datasource.second.url=jdbc:mariadb://localhost:3306/springboot_mariadb
app.datasource.second.username=dbuser
app.datasource.second.password=dbpass
app.datasource.second.driver-class-name=org.mariadb.jdbc.Driver
```


The above method is the correct to way to init multiple database in spring boot 2.0 migration and above. More read can be found [here](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#howto-two-datasources).


------------
    
    
## Answer \#6

 Vote: 3

Created at 2020-03-30 14:43:26

------------

I have added in Application Class

```
@Bean
@ConfigurationProperties("app.datasource")
public DataSource dataSource() {
    return DataSourceBuilder.create().build();
}
```


application.properties I have added 

```
app.datasource.url=jdbc:mysql://localhost/test
app.datasource.username=dbuser
app.datasource.password=dbpass
app.datasource.pool-size=30
```


More details [Configure a Custom DataSource](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-data-access)


------------
    
    
## Answer \#7

 Vote: 2

Created at 2020-06-19 01:52:42

------------

Others have answered so I'll add my 2-cents.

You can either use autoconfiguration (i.e. don't use a @Configuration to create a datasource) or java configuration.

Auto-configuration:

define your datasource type then set the type properties. E.g.

```
spring.datasource.type=com.zaxxer.hikari.HikariDataSource
spring.datasource.hikari.driver-class-name=org.h2.Driver
spring.datasource.hikari.jdbc-url=jdbc:h2:mem:testdb
spring.datasource.hikari.username=sa
spring.datasource.hikari.password=password
spring.datasource.hikari.max-wait=10000
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000
spring.datasource.hikari.leak-detection-threshold=600000
spring.datasource.hikari.maximum-pool-size=100
spring.datasource.hikari.pool-name=MyDataSourcePoolName
```


Java configuration:

Choose a prefix and define your data source 

```
spring.mysystem.datasource.type=com.zaxxer.hikari.HikariDataSource
spring.mysystem.datasource.jdbc- 
url=jdbc:sqlserver://databaseserver.com:18889;Database=MyDatabase;
spring.mysystem.datasource.username=dsUsername
spring.mysystem.datasource.password=dsPassword
spring.mysystem.datasource.driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver
spring.mysystem.datasource.max-wait=10000
spring.mysystem.datasource.connection-timeout=30000
spring.mysystem.datasource.idle-timeout=600000
spring.mysystem.datasource.max-lifetime=1800000
spring.mysystem.datasource.leak-detection-threshold=600000
spring.mysystem.datasource.maximum-pool-size=100
spring.mysystem.datasource.pool-name=MySystemDatasourcePool
```


Create your datasource bean:

```
@Bean(name = { "dataSource", "mysystemDataSource" })
@ConfigurationProperties(prefix = "spring.mysystem.datasource")
public DataSource dataSource() {
    return DataSourceBuilder.create().build();
}
```


You can leave the datasource type out, but then you risk spring guessing what datasource type to use.


------------
    
    
## Answer \#8

 Vote: 1

Created at 2020-11-27 14:11:10

------------

Your can use DataSourceBuilder for this purpose.
```
@Primary
@Bean(name = "dataSource")
@ConfigurationProperties(prefix = "spring.datasource")
public DataSource dataSource(Environment env) {
    final String datasourceUsername = env.getRequiredProperty("spring.datasource.username");
    final String datasourcePassword = env.getRequiredProperty("spring.datasource.password");
    final String datasourceUrl = env.getRequiredProperty("spring.datasource.url");
    final String datasourceDriver = env.getRequiredProperty("spring.datasource.driver-class-name");
    return DataSourceBuilder
            .create()
            .username(datasourceUsername)
            .password(datasourcePassword)
            .url(datasourceUrl)
            .driverClassName(datasourceDriver)
            .build();
}
```



------------
    
    
## Answer \#9

 Vote: 1

Created at 2021-12-31 19:47:12

------------

```
This is working at my side. Hope it helps someone who are facing this issue.

    database.properties:

    jdbcUrl=jdbc:mysql://localhost:3306/candelete
    dataSource.user=root
    dataSource.password=
    dataSource.cachePrepStmts=true
    dataSource.prepStmtCacheSize=250
    dataSource.prepStmtCacheSqlLimit=2048
```



------------
    
    