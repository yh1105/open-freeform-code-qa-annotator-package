
# Post \#74701738 [Link](https://stackoverflow.com/questions/74701738/)

## Spring Boot 3 springdoc-openapi-ui doesn't work

**Vote**: 28 (207/702) **Views**: 11531 (400/702) 

**Internal ID** \#2-5-380

Created at 2022-12-06 11:29:11

Tags: `java` `spring` `spring-boot` `swagger-ui`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I'm trying to add swagger-ui (OpenAPI 3.0) to a Spring Boot v3 application.
I've added the openapi-ui maven dependency, and it should work as per the documentation.
```
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-ui</artifactId>
    <version>1.6.11</version>
</dependency>
```

But apparently, it still doesn't work and localhost:8080/swagger-ui.html returns an 404 error.
What am I missing?
[](https://i.stack.imgur.com/hAeIX.png)


----------
        
## Answer \#0

**Accepted** Vote: 40

Created at 2022-12-06 11:32:14

------------

According to the documentation:
> For  support, make sure you use 
[https://springdoc.org/v2/](https://springdoc.org/v2/)
> For the integration between spring-boot and swagger-ui, add the
library to the list of your project dependencies (No additional
configuration is needed)
```
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.0.0</version>
</dependency>
```

> This will automatically deploy swagger-ui to a spring-boot
application:Documentation will be available in HTML format, using the official
swagger-ui jarsThe Swagger UI page will then be available at
`http://server:port/context-path/swagger-ui.html` and the OpenAPI
description will be available at the following url for json format:
`http://server:port/context-path/v3/api-docs`
```
server: The server name or IP

port: The server port

context-path: The context path of the application

Documentation can be available in yaml format as well, on the following path : /v3/api-docs.yaml
```


---



[https://springdoc.org/v2/#migrating-from-springdoc-v1](https://springdoc.org/v2/#migrating-from-springdoc-v1)
[](https://i.stack.imgur.com/EevN8.png)


------------
    
    
## Answer \#1

 Vote: 9

Created at 2022-12-23 12:03:28

------------

I completely agreed with @JCompetence. Please note that
 now changed to 
from spring boot 3.
```
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.0.2</version>
</dependency>
```

Please try this. If you want to find more info, please check the official link:
[https://springdoc.org/v2/#features](https://springdoc.org/v2/#features)


------------
    
    
## Answer \#2

 Vote: 6

Created at 2022-12-14 14:19:45

------------

For me it helped, just changed the dependency
```
implementation group: 'org.springdoc', name: 'springdoc-openapi-ui', version: '1.6.11'
```

to
```
implementation group: 'org.springdoc', name: 'springdoc-openapi-starter-webmvc-ui', version: '2.0.0'
```

or
```
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.0.0</version>
</dependency>
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2023-01-18 06:00:56

------------

`springdoc-openapi-starter-webmvc-ui` doesn't work with `spring-boot-starter-webflux`
until you include `spring-boot-starter-web`.
If you include spring security then it is dead.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2023-01-26 12:08:41

------------


Generator “spring” supports Jakarta namespace
```
<plugin>
    <groupId>org.openapitools</groupId>
    <artifactId>openapi-generator-maven-plugin</artifactId>
    <configuration>
        <configOptions>
            ...
            **<useSpringBoot3>true</useSpringBoot3>**
        </configOptions>
    </configuration>
</plugin>
```

Generator “java” (to generate a client) doesn’t support Jakarta namespace yet.
So use the Eclipse Transformer plugin (Javax dependencies need provided scope!)
```
<plugin>
        <groupId>org.eclipse.transformer</groupId>
        <artifactId>transformer-maven-plugin</artifactId>
        <extensions>true</extensions>
        <configuration>
          <rules>
           <jakartaDefaults>true</jakartaDefaults>
         </rules>
       </configuration>
        <executions>
            <execution>
                <id>jakarta-ee</id>
                <goals><goal>jar</goal></goals>
                <phase>package</phase>
                <configuration>
                    <artifact>
                        <groupId>${project.groupId}</groupId>
                        <artifactId>${project.artifactId}</artifactId>
                    </artifact>
                </configuration>
            </execution>
        </executions>
    </plugin>
```



------------
    
    