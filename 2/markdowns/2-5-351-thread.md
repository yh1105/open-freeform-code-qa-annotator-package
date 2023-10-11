
# Post \#64802216 [Link](https://stackoverflow.com/questions/64802216/)

## How to generate resttemplate based on yml file?

**Vote**: 2 (631/702) **Views**: 4225 (540/702) 

**Internal ID** \#2-5-351

Created at 2020-11-12 10:32:18

Tags: `java` `spring` `gradle` `swagger` `openapi`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I use open openapi-generator-gradle-plugin. This is my build.gradle:
```
buildscript {
    repositories {
        mavenLocal()

        jcenter {
            url "http://nexus.ca.sbrf.ru:8081/nexus/content/repositories/jcenter"
        }
        maven {
            url "http://nexus.ca.sbrf.ru:8081/nexus/content/repositories/central"
        }
        maven { url 'http://nexus.ca.sbrf.ru:8081/nexus/content/repositories/Atlassian_proxy' }
        maven { url 'http://nexus.ca.sbrf.ru:8081/nexus/content/groups/public' }

        dependencies {
            classpath 'org.openapitools:openapi-generator-gradle-plugin:4.3.0'
            classpath "org.springframework.boot:spring-boot-gradle-plugin:2.2.1.RELEASE"
            classpath "io.spring.gradle:dependency-management-plugin:1.0.9.RELEASE"
        }
    }
}


apply plugin: 'java'
apply plugin: 'org.openapi.generator'
apply plugin: "maven-publish"
apply plugin: "io.spring.dependency-management"
apply plugin: "org.springframework.boot"

dependencies {
    compile 'org.springframework:spring-context:5.3.0'
    compile 'org.springframework:spring-web:4.1.6.RELEASE'
    compile 'javax.servlet:javax.servlet-api:4.0.1'
    compile 'org.openapitools:jackson-databind-nullable:0.2.1'
    compile 'io.swagger:swagger-core:1.5.21'
    compile 'javax.annotation:javax.annotation-api:1.3.1'
}

openApiGenerate {
    generatorName = "spring"
    library = "resttemplate"
    inputSpec = "$rootDir/openapi-contracts/src/main/resources/openapi/api.yml".toString()
    outputDir = "$project.buildDir/generated/openapi"
    apiPackage = "ru.openapi.api.v1"
    modelPackage = "ru.openapi.model.v1"
    enablePostProcessFile = true
    configOptions = [
            dateLibrary         : "java8",
            useBeanValidation   : "true",
            enableBuilderSupport: "true",
            interfaceOnly       : "true",
            delegatePattern     : "true"
    ]
}

sourceSets {
    main {
        java {
            srcDirs "build/generated/openapi/src/main/java"
        }
    }
}
```

The server code is being generated, everything is fine, but I want the resttemplate to be generated for it.
For this I have indicated:
```
library = "resttemplate"
```

I am getting this error:
> Unknown library: resttemplate
Available libraries:
spring-boot
spring-mvc
spring-cloud
How can I solve this problem?


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2020-11-12 11:37:48

------------

Not sure how to do it with gradle, but in maven it'll be like this:
```
<plugin>
            <groupId>org.openapitools</groupId>
            <artifactId>openapi-generator-maven-plugin</artifactId>
            <version>${openapi.plugin.version}</version>
            <executions>
                <execution>
                    <id>${your-id}</id>
                    <phase>generate-sources</phase>
                    <goals>
                        <goal>generate</goal>
                    </goals>
                    <configuration>
                        <skipIfSpecIsUnchanged>true</skipIfSpecIsUnchanged>
                        <inputSpec>${project.basedir}/swagger.json</inputSpec>
                        <generatorName>java</generatorName>
                        <library>resttemplate</library>
                        <apiPackage>your.package</apiPackage>
                        <modelPackage>your.package</modelPackage>
                        <generateApis>true</generateApis>
                        <generateApiDocumentation>false</generateApiDocumentation>
                        <generateApiTests>false</generateApiTests>
                        <generateModels>true</generateModels>
                        <generateModelDocumentation>false</generateModelDocumentation>
                        <generateModelTests>false</generateModelTests>
                        <generateSupportingFiles>true</generateSupportingFiles>
                        <configOptions>
                            <sourceFolder>src/main/java</sourceFolder>
                            <java8>true</java8>
                            <dateLibrary>java8</dateLibrary>
                            <useTags>true</useTags>
                            <interfaceOnly>false</interfaceOnly>
                        </configOptions>
                    </configuration>
                </execution>
            </executions>
        </plugin>
```

Hopefully will help you.
Maybe the problem is that resttemplate is in spring-web dependency


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-11-08 08:35:08

------------

One solution is to use the `spring` generator that will give you annotated interfaces and classes.
I [created a library](https://github.com/tomasbjerre/spring-resttemplate-client) (and [there are others](https://stackoverflow.com/questions/16512188/how-to-generate-java-client-proxy-for-restful-service-implemented-with-spring/69867494)) that, given the annotated interface, will construct a type safe client at runtime.
```
final StoreApi storeApi = SpringRestTemplateClientBuilder
  .create(StoreApi.class)
  .setRestTemplate(restTemplate)
  .setUrl("https://...")
  .build();

// This will become a rest-call
storeApi.deleteOrder(1234L);
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-07-16 12:42:24

------------

To use  library, you have to set  as "java"


------------
    
    