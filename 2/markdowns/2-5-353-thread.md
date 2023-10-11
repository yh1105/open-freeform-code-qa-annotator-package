
# Post \#64907675 [Link](https://stackoverflow.com/questions/64907675/)

## Including profiles in spring boot 2.4.0 version

**Vote**: 44 (143/702) **Views**: 52857 (158/702) 

**Internal ID** \#2-5-353

Created at 2020-11-19 08:09:47

Tags: `java` `spring` `spring-boot` `profile` `spring-profiles`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

As a developer, I use the default `dev` profile in my local development environment. Here is part of my `application-dev.properties` file:
```
# Profiles    
spring.profiles.include=auth
```

Previously I  used Spring Boot 2.3.0.RELEASE and the `spring.profiles.include` property included `auth` profile at runtime.
But after I migrated to Spring Boot 2.4.0, I don't get the `auth` profile enabled. `spring.profiles.include` property doesn't seem to work as before.
Please tell me how I can configure my profiles so that I get the same result as before migration. (I would not like to use profile groups here)
Thanks in advance!


----------
        
## Answer \#0

**Accepted** Vote: 66

Created at 2020-11-19 12:43:46

------------

In case your configuration processing has changed in incompatible ways and you wish to use the "legacy" processing way, you can re-enable it by setting:
```
spring.config.use-legacy-processing=true
```

or alternatively, using YAML:
```
spring:
  config:
    use-legacy-processing: true
```

which should revert the configuration processing to the `2.3.x` equivalent. Do note, however, that this property exists solely to ease the migration of profile configurations from `2.3.x` to `2.4.x` and will likely be deprecated and removed in a future major release, so you should still try to migrate ASAP. To understand the reason for this change and some additional information, read on.
Of note in `2.4.0` are the following two paradigms:
> So in Spring Boot 2.4 we’re planning to make two significant changes to the way the properties and YAML files are loaded:
1. Documents will be loaded in the order that they’re defined.
2. Profiles can no longer be activated from profile specific documents.


This change has in fact made the what-overrides-what-when logic considerably simpler to digest, but leads to having to disable some functionality. For example:
```
my.prop: test

---
spring.profiles: prodprops
my.prop: prod

---
spring.profiles: prod
# no longer works - activating a profile from a profile-specific document!
spring.profiles.include: prodprops
```

would lead to an exception as the configuration attempts to activate a profile from a profile-specific document, which is not allowed anymore.
To cover this use case (and others),  have been added as a feature. This means that to enable your previous behaviour, you would need to create a profile group as follows:
```
spring.profiles.group.<group>=dev, auth
```

or alternatively, in YAML:
```
spring:
  profiles:
    group:
      <group>: dev, auth
```

Where `<group>` is the name of your chosen profile group. Note that you can define multiple groups, all of which should have different names. If you then start your application using the `<group>` profile, all the profiles that are part of that group should be activated.
As a side-note, Spring Boot `2.4.0` additionally added support for multi-document  files, which look as follows:
```
test=value
spring.profiles.active=local
#---
spring.config.activate.on-profile=dev
test=overridden value
```

Note the document separator (`#---`). This allows you to have similar overriding logic in `.properties` files as in `.yml` files.
Again, this and other information is provided in the relevant [update post](https://spring.io/blog/2020/08/14/config-file-processing-in-spring-boot-2-4).
 If prior deprecations are any indicator, the property should be removed in `2.5.0` at the earliest or `2.6.0` at the latest, with the latter being more likely (and a deprecation as of `2.5.x`).


------------
    
    
## Answer \#1

 Vote: 8

Created at 2021-01-18 13:29:21

------------

You can use `spring.config.import` using `classpath:`
```
spring.config.import=classpath:application-DEV.yml,classpath:application-auth.yml
```



------------
    
    
## Answer \#2

 Vote: 7

Created at 2021-05-17 06:54:41

------------

Although we have an accepted answer above. But I would share my solution via multiple files.
I'm having multiple config files in my project
```
./
application.yml
application-auth.yml
application-mockauth.yml
application-datasource.yml
```

The body of application-auth.yml or application-datasource.yml are the same as we're implementing before spring boot 2.4. Minor adjustment will be located inside application.yml
```
spring:
  profiles:
    group:
      "dev": "datasource,mockauth"
      "prod": "datasource,auth"
```

Instead of spring.profiles.include, you will group related config with environment name (dev, prod...).


------------
    
    
## Answer \#3

 Vote: 2

Created at 2020-11-25 09:37:37

------------

You can also use `spring.config.import` to import configuration from other file according this documentation [Config file processing in Spring Boot 2.4](https://spring.io/blog/2020/08/14/config-file-processing-in-spring-boot-2-4).


------------
    
    