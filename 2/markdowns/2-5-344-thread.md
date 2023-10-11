
# Post \#61020200 [Link](https://stackoverflow.com/questions/61020200/)

## Getting java.lang.AbstractMethodError when I test using REST Assured

**Vote**: 16 (306/702) **Views**: 15240 (355/702) 

**Internal ID** \#2-5-344

Created at 2020-04-03 20:50:39

Tags: `java` `spring-boot` `maven` `junit` `rest-assured`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I'm trying to test a REST api defined in my controller class using `REST Assured v4.3.0`, but I get `java.lang.AbstractMethodError` when I run the test. I understand this error is occurring because I'm calling an abstract method, but I'm having a hard time resolving it. 

It seems that the error is occurring due to `.body(is(equalTo("success")))` in `SampleControllerTest.java` because when I remove this line, the test succeeds. I tried a few things to resolve it, but didn't get any success:

- `io.rest-assured/spring-mock-mvc`- `org.hamcrest.Matchers.*``org.hamcrest.CoreMatchers.*`- `org.hamcrest/hamcrest`

Here's my code for your reference:



```
test
|- src/
|  |- main/
|  |  |- java/
|  |  |  |- org/
|  |  |  |  |- example/
|  |  |  |  |  |- Application.java
|  |  |  |  |  |- SampleController.java
|  |- test/
|  |  |- java/
|  |  |  |- org/
|  |  |  |  |- example/
|  |  |  |  |  |- SampleControllerTest.java
|- target/
|- pom.xml
```




```
package org.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```




```
package org.example;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseStatus;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class SampleController {
    @RequestMapping(value = "/sample")
    @ResponseStatus(value = HttpStatus.OK)
    public String getSample() {
        return "success";
    }
}
```




```
package org.example;

import org.junit.Test;

import static io.restassured.module.mockmvc.RestAssuredMockMvc.*;
import static org.hamcrest.Matchers.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

public class SampleControllerTest {
    @Test
    public void testGetSample() {
        given()
            .standaloneSetup(new SampleController())
            .when()
            .get("/sample")
            .then()
            .assertThat(status().isOk())
            .body(is(equalTo("success")));
    }
}
```




```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <packaging>jar</packaging>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>org.example</groupId>
    <artifactId>test</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <java.version>8</java.version>
        <start-class>org.example.Application</start-class>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- JUnit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <!-- https://mvnrepository.com/artifact/io.rest-assured/spring-mock-mvc -->
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>spring-mock-mvc</artifactId>
            <version>4.3.0</version>
            <scope>test</scope>
        </dependency>

        <!-- https://mvnrepository.com/artifact/io.rest-assured/json-path -->
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>json-path</artifactId>
            <version>4.3.0</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/io.rest-assured/xml-path -->
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>xml-path</artifactId>
            <version>4.3.0</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/io.rest-assured/rest-assured -->
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <version>4.3.0</version>
            <scope>test</scope>
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


When I run the test using `mvn test`, this is the error I get:

```
[ERROR] Tests run: 1, Failures: 0, Errors: 1, Skipped: 0, Time elapsed: 1.325 s <<< FAILURE! - in org.example.SampleControllerTest
[ERROR] testGetSample(org.example.SampleControllerTest)  Time elapsed: 1.288 s  <<< ERROR!
java.lang.AbstractMethodError: Method io/restassured/internal/ResponseSpecificationImpl.getProperty(Ljava/lang/String;)Ljava/lang/Object; is abstract
        at org.example.SampleControllerTest.testGetSample(SampleControllerTest.java:20)
```


Thanks for any help in advance!


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2020-04-03 21:31:30

------------

It turns out that `io.rest-assured/spring-mock-mvc` dependency was conflicting with `io.rest-assured/rest-assured` dependency. Once I removed the `io.rest-assured/rest-assured` from `pom.xml`, the test worked successfully. 

A few years ago when I was using REST Assured version `3.1.1`, I could keep both of these dependencies, but perhaps newer versions don't allow this.


------------
    
    
## Answer \#1

 Vote: 15

Created at 2020-07-17 08:47:53

------------

Because of rest-assured 4.3.0 upgrade the Groovy from 2.5.7 to 3.0.2, see [rest-assured/changelog.txt at master · rest-assured/rest-assured](https://github.com/rest-assured/rest-assured/blob/master/changelog.txt), you can specified the groovy version in pom file.
```
<properties>
    <rest-assured.version>4.3.0</rest-assured.version>
    <groovy.version>3.0.2</groovy.version>
  </properties>
```



------------
    
    
## Answer \#2

 Vote: 9

Created at 2020-04-03 21:31:29

------------

It looks like a bug in REST Assured 4.3.0, your code works with 4.2.0 version.

I didn't find opened issue. [https://github.com/rest-assured/rest-assured/issues](https://github.com/rest-assured/rest-assured/issues)


------------
    
    
## Answer \#3

 Vote: 6

Created at 2020-04-20 08:01:40

------------

I used 4.2.0 instead of 4.3.0 .. it works for me 


------------
    
    
## Answer \#4

 Vote: 5

Created at 2021-01-29 12:47:53

------------

For Maven projects, the code below works well with Spring Boot projects:
```
<properties>
       <groovy.version>3.0.7</groovy.version>
       <rest-assured.version>4.3.3</rest-assured.version>
   </properties>

   <dependencies>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
       <dependency>
           <groupId>io.rest-assured</groupId>
           <artifactId>spring-mock-mvc</artifactId>
           <scope>test</scope>
           <exclusions>
               <exclusion>
                   <artifactId>groovy</artifactId>
                   <groupId>org.codehaus.groovy</groupId>
               </exclusion>
           </exclusions>
       </dependency>
       <dependency>
           <groupId>org.codehaus.groovy</groupId>
           <artifactId>groovy</artifactId>
           <version>${groovy.version}</version>
       </dependency>
       <dependency>
           <groupId>org.codehaus.groovy</groupId>
           <artifactId>groovy-xml</artifactId>
           <version>${groovy.version}</version>
       </dependency>
       <dependency>
           <groupId>org.codehaus.groovy</groupId>
           <artifactId>groovy-json</artifactId>
           <version>${groovy.version}</version>
       </dependency>
       <dependency>
           <groupId>io.rest-assured</groupId>
           <artifactId>json-path</artifactId>
           <version>${rest-assured.version}</version>
           <scope>test</scope>
       </dependency>
       <dependency>
           <groupId>io.rest-assured</groupId>
           <artifactId>xml-path</artifactId>
           <version>${rest-assured.version}</version>
           <scope>test</scope>
       </dependency>
   </dependencies>
```
```



------------
    
    
## Answer \#5

 Vote: 4

Created at 2020-05-06 12:16:54

------------

I also had an `java.lang.AbstractMethodError` but on another method.

rest-assured 4.3 upgraded it's implementation to use [groovy 3](https://groovy-lang.org/releasenotes/groovy-3.0.html), see [rest assured's release notes](https://github.com/rest-assured/rest-assured/blob/master/changelog.txt)

In my case the dependency resolution downgraded groovy to use some 2.x version.

If you use maven:

You can check that using `mvn dependency:tree`. 
If there's a conflict, add the necessary groovy dependencies as direct dependencies with a version=>3.0.2 to your project. 

If you use gradle:

You can check that using `gradle depencencies`.
If there's a conflict, then add a resolution strategy:

```
configurations.all {
    resolutionStrategy.eachDependency { DependencyResolveDetails details ->
        if (details.requested.group == 'org.codehaus.groovy') {
            details.useVersion "3.0.2"
            details.because "needed by rest-assured>=4.3"
        }
    }
}
```


Maybe that can help somebody.


------------
    
    
## Answer \#6

 Vote: 2

Created at 2020-11-25 11:12:03

------------

If you use Rest Assured version `4.3.2`, just specify  the groovy version to `3.0.6` and that fixes the error.
```
<rest-assured.version>4.3.2</rest-assured.version>
<groovy.version>3.0.6</groovy.version>
```

Good luck.


------------
    
    
## Answer \#7

 Vote: 1

Created at 2020-10-08 12:32:45

------------

Use below dependency  of rest assured in pom.xml
```
<dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>4.2.0</version>
        <scope>test</scope>
    </dependency>
```



------------
    
    