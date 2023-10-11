
# Post \#52608600 [Link](https://stackoverflow.com/questions/52608600/)

## [HV000030: No validator could be found for constraint 'javax.validation.constraints.NotEmpty' validating type 'java.lang.String'

**Vote**: 45 (139/702) **Views**: 94114 (88/702) 

**Internal ID** \#2-5-372

Created at 2018-10-02 12:43:26

Tags: `hibernate` `validation` `annotations`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I want to underline that I already searched for this kind of problem but couldn't find a solution for my case.
In my Spring Boot webapp I keep getting this error when validating beans using @NotEmpty or @NotBlank annotation of package javax.validation.constraints:

```
14:04:59,426 ERROR [org.springframework.boot.web.servlet.support.ErrorPageFilter] (default task-33) Forwarding to error page from request [/registrati
on] due to exception [HV000030: No validator could be found for constraint 'javax.validation.constraints.NotEmpty' validating type 'java.lang.String'.
 Check configuration for 'username']: javax.validation.UnexpectedTypeException: HV000030: No validator could be found for constraint 'javax.validation
.constraints.NotEmpty' validating type 'java.lang.String'. Check configuration for 'username'
        at org.hibernate.validator.internal.engine.constraintvalidation.ConstraintTree.throwExceptionForNullValidator(ConstraintTree.java:229)
        at org.hibernate.validator.internal.engine.constraintvalidation.ConstraintTree.getConstraintValidatorNoUnwrapping(ConstraintTree.java:310)
        at org.hibernate.validator.internal.engine.constraintvalidation.ConstraintTree.getConstraintValidatorInstanceForAutomaticUnwrapping(Constraint
Tree.java:244)
```


No errors if I use @NotNull annotation, but this is not the desired behavior because it allows for blank fields.
These are my pom.xml dependencies:

```
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
          <groupId>nz.net.ultraq.thymeleaf</groupId>
          <artifactId>thymeleaf-layout-dialect</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-batch</artifactId>
       </dependency>
       <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>org.mariadb.jdbc</groupId>
            <artifactId>mariadb-java-client</artifactId>
            </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
        </dependency>

        <!-- https://mvnrepository.com/artifact/net.sourceforge.nekohtml/nekohtml -->
        <dependency>
            <groupId>net.sourceforge.nekohtml</groupId>
            <artifactId>nekohtml</artifactId>
            <version>1.9.21</version><!--$NO-MVN-MAN-VER$-->
        </dependency>

        <!-- webjars -->
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>bootstrap</artifactId>
            <version>4.1.3</version>
            <exclusions>
                <exclusion>
                    <groupId>org.webjars</groupId>
                    <artifactId>jquery</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.webjars/datatables -->
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>datatables</artifactId>
            <version>1.10.19</version>
            <exclusions>
                <exclusion>
                    <groupId>org.webjars</groupId>
                    <artifactId>jquery</artifactId>
                </exclusion>
            </exclusions>
        </dependency>


    </dependencies>

    <build>
        <plugins>
            <!-- plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin -->
        </plugins>
    </build>


</project>
```


I see that hibernate validator is working because if I don't use any @NotEmpty nor @NotBlank annotation, other annotations such as @Size are working correctly.

In my bean I'm importing javax.validation.constraints.
When starting up my JBoss, following line about hibernate validator appears:

```
14:04:17,676 INFO  [org.hibernate.validator.internal.util.Version] (background-preinit) HV000001: Hibernate Validator 5.3.5.Final-redhat-2
```


This is not the same version as the hibernate-validator 6.0.11 jar that is resolved by Maven.

What's happening? Maybe some dependency conflict?

Thanks to everyone that could help me.


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2019-01-10 17:55:36

------------

Thanks to ChuckL comment I just found out that JBoss 7.1 has its own hibernate-validator-5.3.5.Final-redhat-2.jar under:

```
modules\system\layers\base\org\hibernate\validator\main
```


That's why I was seeing version 5.3.5.Final-redhat-2 in my startup logs and not the one packaged in my WAR. It also has validation-api-1.1.0.Final-redhat-1.jar under:

```
modules\system\layers\base\javax\validation\api\main
```


One could try to exclude those dependencies by creating a jboss-deployment-structure.xml file in the root META-INF of the webapp and trying to use those packaged in the application WAR/JAR.

Anyway JBoss 7.1 is a JEE7 implementation container. Changing the bean validation modules that are shipped with JBoss could lead to unexpected consequences that I don't want to experiment :)

The clean solution would be to migrate JBoss to a newer version which supports JEE8, otherwise keep using deprecated APIs.


------------
    
    
## Answer \#1

 Vote: 46

Created at 2019-05-12 04:39:08

------------

I got a similiar error but `...Constraints.NotEmpty' validating type 'java.lang.Long'` 

It happened because I was using `@NotEmpty` in a non-String field.

I solved it replacing `@NotEmpty` with `@Size(value=1, message= "whatever")`


------------
    
    
## Answer \#2

 Vote: 19

Created at 2020-10-23 19:15:27

------------


1. @NotBlank : Checks that the annotated character sequence is not null and the trimmed length is greater than 0.
2. @NotNull : Checks that the annotated value is not null, however it can be empty.
3. @NotEmpty : Checks whether the annotated element is not null nor empty.


Before use the above annotation, have to think about how it works.
for further reading use this article : [Read more article one](https://howtodoinjava.com/hibernate/hibernate-validator-java-bean-validation/) [Read more article two](http://hibernate.org/validator/tooling/)


------------
    
    
## Answer \#3

 Vote: 10

Created at 2018-10-04 16:32:56

------------

I have the same problem; my  dependency includes a dependency on  but I.  I added this exclusion to my POM and it's all all good now:

```
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.hibernate</groupId>
                    <artifactId>hibernate-validator</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
</dependencyManagement>
```



------------
    
    
## Answer \#4

 Vote: 3

Created at 2021-09-06 07:51:48

------------

adding newer version in pom.xml fixed this error:
```
<dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-validator</artifactId>
        <version>6.1.6.Final</version>
    </dependency>
```

I encountered this error, because I used another version of validator  api directly. Like this:
```
<dependency>
        <groupId>javax.validation</groupId>
        <artifactId>validation-api</artifactId>
        <version>2.0.1.Final</version>
        <scope>compile</scope>
    </dependency>
```



------------
    
    
## Answer \#5

 Vote: 1

Created at 2021-12-02 15:39:54

------------

Your hibernate validator is implementing the Java Bean Validation 1.1 specification, but @NotBlank is from Java Bean Validation 2.0. As a result, you have three options:

1. Upgrade your hibernate validator so that it implements the Java Bean Validation 2.0 specification
2. Remove @NotBlank, since your version of hibernate validator does not support it.
3. Remove hibernate validator and lose bean validation features, but this error will go away.




------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-05-19 13:28:21

------------

Change the @NotEmpty import from org.hibernate.validator.constraints.NotEmpty;


------------
    
    
## Answer \#7

 Vote: 0

Created at 2022-12-28 07:29:36

------------

This error means you are trying to Validate an attribute of some other type than the one the annotation is made for
e.g. I got below error while trying to validate an Enum with a NotBlank annotation
```
javax.validation.UnexpectedTypeException: HV000030: No validator could be found for constraint 'javax.validation.constraints.NotBlank' validating type 'com.example.irrigation.dtos.TimeSlot'. Check configuration for 'timeSlot'
```

so,
@NotBlank, @NotEmpty is for validating String
They cannot be used for an enum.
If try to mix data Type (e.g. Enum for @NotBlank, etc.)
then UnexpectedTypeException will be thrown.


------------
    
    
## Answer \#8

 Vote: 0

Created at 2023-03-04 20:29:29

------------

@NotEmpty should only be used for String type attributes.
For mandatory data you should use @NotNull.
And it also has @Future and @Past annotations to validate whether it is a future or past date.


------------
    
    
## Answer \#9

 Vote: -3

Created at 2022-02-06 13:13:15

------------

Just go with @NotNull and will work


------------
    
    