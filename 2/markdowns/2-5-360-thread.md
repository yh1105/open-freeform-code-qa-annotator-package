
# Post \#66084248 [Link](https://stackoverflow.com/questions/66084248/)

## @Value for numeric types in yaml file in Spring Boot

**Vote**: 5 (499/702) **Views**: 4169 (543/702) 

**Internal ID** \#2-5-360

Created at 2021-02-07 03:21:48

Tags: `java` `spring` `spring-boot` `yaml`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java` `yaml`)

----------

**Notepad**


----------

I got a `data.yml` in resources folder of a following structure:
```
main:
  header:
    info: 3600L
```

I use Spring Boot version 2.4.2, I want to inject property `main.header1.info` to a field, I do this the following way:
```
@Component
@PropertySource("classpath:data.yml")
public class SomeClass {
    @Value("`main.header1.info")
    private long info;
    ...
}
```

But a `NumberFormatException` ocurres:
```
java.lang.NumberFormatException: For input string: "main.header1.info"
    at java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:65) ~[na:na]
    at java.base/java.lang.Long.parseLong(Long.java:692) ~[na:na]
    ...
```

I know that `long` is not supported in `yml`, but I think its not the case. I tried other numeric types and corresponding wrapper-classes, like `Double`.
So, how to fix that?


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2021-02-07 04:04:03

------------

I recommend using `application.yml` file inspite of custom `YAML` file.
>  : `application.properties` is spring's default config file. If you use it, you don't have to worry about loading the file to
context manually as spring takes care of it. But, In you case, you are
trying to load and read value from a custom YAML file. So,
`@PropertySource` won't help here.
Refer to the [spring-docs](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config-yaml-shortcomings) for details about YAML Shortcomings.
However if you still wish to read values from a custom yaml, You will need to write a custom class ( Ex : `CustomYamlPropertySourceFactory` ) which implements `PropertySourceFactory` & inform `@PropertySource` to use this factory class.

```
@Component
    @PropertySource(value = "classpath:date.yml", factory = CustomYamlPropertySourceFactory.class) 
    public class SomeClass {

    @Value("${main.header.info}")
    private int info;
}
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-02-07 03:35:01

------------

You have to use `${main.header.info}` to inject the value from properties.
`@PropertySource` doesn't support the loading of `.yaml` or `yml` files.
Try to use `.properties` file for loading them using `@PropertySource`.
> There was an issue opened for this but Spring guys closed it. Many
developers opposed and were unhappy with this. They still demanded to
re-open this issue.
- Read here : [https://github.com/spring-projects/spring-framework/issues/18486](https://github.com/spring-projects/spring-framework/issues/18486)- Read [M. Deinum Article](https://deinum.biz/2018-07-04-PropertySource-with-yaml-files/) for a workaround of this problem to load YML files using `@PropertySource`.
Instead create `data.properties` file.
```
main.header.info=3600L
```

Modify the code to this :
```
@Component
@PropertySource("classpath:data.properties")
public class SomeClass {
    @Value("${main.header1.info}")
    private long info;
    ...
}
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-03-31 15:25:59

------------

`application.properties``YAML`
As many answers say here, there is no default way for Spring to parse `YAML` property files. The default `DefaultPropertySourceFactory` implementation is only capable of parsing `.properties` and `.xml` files.
You need to create a custom factory class implementing `PropertySourceFactory` to let the framework know how to parse a specific configuration source.
Here is a basic example on how to do that:
```
public class YamlPropertySourceFactoryExample implements PropertySourceFactory {

    @Override
    public PropertySource<?> createPropertySource(final String name, final EncodedResource resource) throws IOException {
        final Properties propertiesFromYaml = loadYamlIntoProperties(resource);
        final String sourceName = name != null ? name : resource.getResource().getFilename();
        return new PropertiesPropertySource(sourceName, propertiesFromYaml);
    }

    private Properties loadYamlIntoProperties(final EncodedResource resource) {

        final YamlPropertiesFactoryBean factory = new YamlPropertiesFactoryBean();
        factory.setResources(resource.getResource());
        factory.afterPropertiesSet();
        return factory.getObject();
    }
}
```

With it you are accomplishing two things:
- `YAML``Properties`- `PropertiesPropertySource``Properties`
After that you can reference it in a `factories` atribute of any `@PropertyResource`:
```
@PropertySource(value = "classpath:path/to/your/properties.yml", factory = YamlPropertySourceFactoryExample.class)
```


---


Edit: It cannot be used with `@TestPropertySource`, it does not support any `factories` at the moment.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-02-07 03:40:39

------------

You cannot use a yml file with `@PropertySource`.
> YAML files cannot be loaded by using the `@PropertySource` annotation. So, in the case that you need to load values that way, you need to use a properties file.
See [YAML Shortcomings](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-external-config-yaml-shortcomings) documentation.


------------
    
    