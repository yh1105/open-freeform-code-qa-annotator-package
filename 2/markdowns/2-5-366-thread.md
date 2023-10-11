
# Post \#65181495 [Link](https://stackoverflow.com/questions/65181495/)

## bean creation error when starting spring boot application

**Vote**: 29 (199/702) **Views**: 93601 (90/702) 

**Internal ID** \#2-5-366

Created at 2020-12-07 12:10:05

Tags: `java` `spring` `maven` `spring-cloud` `openfeign`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

When i try to run my spring boot application i get this Exception:
> org.springframework.beans.factory.BeanCreationException: Error
creating bean with name 'configurationPropertiesBeans' defined in
class path resource
[org/springframework/cloud/autoconfigure/ConfigurationPropertiesRebinderAutoConfiguration.class]:
Post-processing of merged bean definition failed; nested exception is
java.lang.IllegalStateException: Failed to introspect Class
[org.springframework.cloud.context.properties.ConfigurationPropertiesBeans]
from ClassLoader
[jdk.internal.loader.ClassLoaders$AppClassLoader@3764951d]
I think it's an version incompatibility. I imported open feign in my pom.xml and after that it wasn't working, but i dont know how to fix that. I use open feign 2.2.5.RELEASE.
Here my pom.xml:
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.0</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>privas.microservice</groupId>
    <artifactId>sellcar</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>sellcar</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>15</java.version>
        <maven.compiler.target>${java.version}</maven.compiler.target>
        <maven.compiler.source>${java.version}</maven.compiler.source>
        <spring-cloud.version>Hoxton.SR8</spring-cloud.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>


    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>RELEASE</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>jakarta.validation</groupId>
            <artifactId>jakarta.validation-api</artifactId>
            <version>2.0.2</version>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-commons</artifactId>
            <version>2.4.1</version>
            <scope>compile</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```



----------
        
## Answer \#0

**Accepted** Vote: 22

Created at 2021-01-19 14:05:19

------------

To elaborate on @M-deinum's comment, setting Spring Boot version to `2.3.4.RELEASE` (instead of `2.4.2` in my case) solved the issue. In `gradle` this meant changing:
```
plugins {
    id 'org.springframework.boot' version '2.4.2'
    ...
}
```

To
```
plugins {
    id 'org.springframework.boot' version '2.3.4.RELEASE'
    ...
}
```



------------
    
    
## Answer \#1

 Vote: 18

Created at 2021-07-17 18:21:44

------------

I had a same issue, and it is happening because of Spring Cloud services and Spring Boot version issues. I got rid of it by using [https://start.spring.io/](https://start.spring.io/) to generate my project.
When you select all dependencies needed for your project, you can then click the  button  and check the `pom.xml` file.
This issue happened to me when I tried to add dependency for `Eureka-client` to my `pom.xml` after generating project, so using IntelliJ.
I got the same error.
Then I went to Spring.io again select dependencies that I use for my project and also dependency for `Eureka-client`, clicked on  button and saw that I need to add this line of code under java version in `pom.xml`
```
<spring-cloud.version>2020.0.3</spring-cloud.version>
```

But also this lines as well:
```
<dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

So I just copy pasted it to my existing `pom.xml` and it worked!


------------
    
    
## Answer \#2

 Vote: 15

Created at 2021-03-04 07:19:23

------------

You need to change spring boot version to Released version
from
```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.0</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

to
```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.3.RELEASE</version>
    <relativePath /> <!-- lookup parent from repository -->
</parent>
```



------------
    
    
## Answer \#3

 Vote: 10

Created at 2021-07-05 00:31:02

------------

Spring-Cloud - Hoxton.SR8 is not compatiable with Spring-boot 2.4.0
Just use either of the combinations:
```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.0</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

<spring-cloud.version>2020.0.3</spring-cloud.version>
```

Or
```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.0</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

<spring-cloud.version>Hoxton.SR8</spring-cloud.version>
```



------------
    
    
## Answer \#4

 Vote: 9

Created at 2021-11-16 15:25:29

------------

You need to follow the release train of Spring Cloud and match the version of the Spring-boot starter. The release train is available on the spring-cloud website [https://spring.io/projects/spring-cloud](https://spring.io/projects/spring-cloud)
The release train as of now is:
[Release train for spring-cloud](https://i.stack.imgur.com/B2kjW.png)


------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-04-19 13:14:58

------------

just Changing to  :
```
<dependency>                                                                          
<groupId>org.springframework.cloud</groupId>
<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
<version>3.1.1</version>
</dependency>
```

and Adding version to
```
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>2.6.6</version>
        </plugin>
    </plugins>
</build>
```

Worked Fine For Me


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-05-26 22:46:00

------------

This worked for me.
Go ti spring initialize start.spring.io and add the dependancies again. Compare your .pom with downloaded pom. I got my issue.
<spring-cloud.version>2021.0.3-SNAPSHOT</spring-cloud.version>
I was having diffent version of spring cloud hence there was a conflict.


------------
    
    
## Answer \#7

 Vote: 0

Created at 2022-11-16 16:18:17

------------

```
<repository>
      <id>netflix-candidates</id>
      <name>Netflix Candidates</name>
      <url>https://artifactory-oss.prod.netflix.net/artifactory/maven-oss-candidates</url>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
```

add this to the repository


------------
    
    