
# Post \#66813147 [Link](https://stackoverflow.com/questions/66813147/)

## SpringCloud 2020.0.2 upgrade generates testing error

**Vote**: 22 (248/702) **Views**: 28754 (235/702) 

**Internal ID** \#2-5-362

Created at 2021-03-26 08:10:53

Tags: `spring` `spring-boot` `spring-cloud` `spring-test`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I'm trying to upgrade a project from SpringCloud BOM `2020.0.1` to `2020.0.2`
When I change the BOM and re-build, I get an error in JUnit tests, saying that the new parameter `spring.config.import` is not set for configserver.
This project is not a ConfigServer, neither use ConfigServer (commented config client)
This is the reported error in tests `contextLoads()`
```
java.lang.IllegalStateException: Failed to load ApplicationContext
    at org.springframework.test.context.cache.DefaultCacheAwareContextLoaderDelegate.loadContext(DefaultCacheAwareContextLoaderDelegate.java:132)
    at org.springframework.test.context.support.DefaultTestContext.getApplicationContext(DefaultTestContext.java:124)
    at org.springframework.test.context.support.DependencyInjectionTestExecutionListener.injectDependencies(DependencyInjectionTestExecutionListener.java:118)
    .. Many more
Caused by: org.springframework.cloud.config.client.ConfigServerConfigDataMissingEnvironmentPostProcessor$ImportException: No spring.config.import set
    at org.springframework.cloud.config.client.ConfigServerConfigDataMissingEnvironmentPostProcessor.postProcessEnvironment(ConfigServerConfigDataMissingEnvironmentPostProcessor.java:60)
    at org.springframework.boot.env.EnvironmentPostProcessorApplicationListener.onApplicationEnvironmentPreparedEvent(EnvironmentPostProcessorApplicationListener.java:100)
    at org.springframework.boot.env.EnvironmentPostProcessorApplicationListener.onApplicationEvent(EnvironmentPostProcessorApplicationListener.java:86)
    ... Many more
```

This is my build.gradle
```
plugins {
    id 'org.springframework.boot' version '2.4.2'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
}

group = 'com.example.microservices.composite.product'
version = '1.0.0-SNAPSHOT'
sourceCompatibility = '1.8'

repositories {
    mavenCentral()
    maven {
        url 'https://repo.spring.io/milestone'
    }
}

ext {
   // resilience4jVersion = "1.7.0"
   resilience4jVersion = "1.6.1"

}

dependencies {
    // Local projects dependencies
    implementation project(':api')
    implementation project(':util') 

    // Implementations dependencies
    // Standard (actuator - for monitoring and Health)  
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    // WebFlux (asynchronous Web)
    implementation 'org.springframework.boot:spring-boot-starter-webflux'

    // SpringFox dependencies
    implementation "io.springfox:springfox-boot-starter:3+"
    implementation('io.springfox:springfox-spring-webflux:3+')

    // Implementation: Spring cloud
    implementation('org.springframework.cloud:spring-cloud-starter-config')
    implementation('org.springframework.cloud:spring-cloud-starter-stream-rabbit')
    implementation('org.springframework.cloud:spring-cloud-starter-stream-kafka')

    // Security
    implementation('org.springframework.boot:spring-boot-starter-security')
    implementation('org.springframework.security:spring-security-oauth2-resource-server')
    implementation('org.springframework.security:spring-security-oauth2-jose')

    // CircuitBreaker with Resilience4J
    implementation("io.github.resilience4j:resilience4j-spring-boot2:${resilience4jVersion}")
    implementation("io.github.resilience4j:resilience4j-reactor:${resilience4jVersion}")
  
    // Implementation: Tracing
    implementation('org.springframework.cloud:spring-cloud-starter-sleuth') 

    // Implementation: Performance metrics
    implementation("io.micrometer:micrometer-registry-prometheus")
    
    // TEST dependencies
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
    }
    testImplementation 'io.projectreactor:reactor-test'
    testImplementation('org.springframework.cloud:spring-cloud-stream-test-support')

}


dependencyManagement {
    imports {
        // mavenBom 'org.springframework.cloud:spring-cloud-dependencies:2020.0.1'
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:2020.0.2"
    }
}

test {
    useJUnitPlatform()
}
```

And my contextLoads() method in test class is trivial
```
// Test: Application
@AutoConfigureWebTestClient
@SpringBootTest(
    webEnvironment = WebEnvironment.RANDOM_PORT, 
    classes = { 
        ProductCompositeServiceApplication.class,
        TestSecurityConfig.class }, 
    properties = { 
        "spring.main.allow-bean-definition-overriding=true" })

    @Test
    public void contextLoads() {
    }
}
```

NOTE: I have also tried defining the `spring.config.import' property to empty or none in the class, with no change
```
@SpringBootTest(
    webEnvironment = WebEnvironment.RANDOM_PORT, 
    classes = { 
        ProductCompositeServiceApplication.class,
        TestSecurityConfig.class }, 
    properties = { 
        "spring.main.allow-bean-definition-overriding=true",
        "spring.config.import=" })
```



----------
        
## Answer \#0

**Accepted** Vote: 20

Created at 2021-03-29 08:42:32

------------

I have noted the same problem after upgrading to SpringCloud 2020.0.2
Adding `spring.cloud.config.enabled=false` in the tests solved the issue.
E.g.:
```
@SpringBootTest(
  webEnvironment = RANDOM_PORT, 
  properties = {"spring.cloud.config.enabled=false"}
)
```



------------
    
    
## Answer \#1

 Vote: 25

Created at 2021-04-27 10:44:21

------------

I've faced same issue and resolved by adding bootstrap lib with config lib as follows,
```
implementation 'org.springframework.cloud:spring-cloud-starter-config'
implementation 'org.springframework.cloud:spring-cloud-starter-bootstrap'
```



------------
    
    
## Answer \#2

 Vote: 16

Created at 2021-11-23 14:29:44

------------

i fixed this. adding the dependency
```
<dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```



------------
    
    
## Answer \#3

 Vote: 3

Created at 2021-10-31 05:34:17

------------

You can disable the import checks by adding these lines to your `application.yml` file in the `test/resources` folder:
```
spring:
  cloud:
    config:
       import-check:
          enabled: false
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2022-12-05 09:58:58

------------

Temporary solution
```
spring.cloud.config.import-check.enabled=false
```



------------
    
    
## Answer \#5

 Vote: 1

Created at 2022-07-26 14:01:41

------------

This method work for me:
```
<dependency>
    <groupId>org.spring framework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-09-22 01:21:37

------------

Please check your application.yml or application.xml (which is your configuration file is in place and it is able to pull all the configuration


------------
    
    