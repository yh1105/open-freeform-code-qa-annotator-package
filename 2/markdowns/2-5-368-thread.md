
# Post \#69449905 [Link](https://stackoverflow.com/questions/69449905/)

## How to enable Spring Boot Live Dev Tools on IntelliJ 2021.2 to rebuild classes after modifications and deploy changes to server?

**Vote**: 14 (336/702) **Views**: 7313 (479/702) 

**Internal ID** \#2-5-368

Created at 2021-10-05 11:49:50

Tags: `spring` `spring-boot` `intellij-idea`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

This is a tutorial on how to enable Dev Tools project on IntelliJ 2021.2 and observe the changes in code without having to restart the Tomcat server.


----------
        
## Answer \#0

**Accepted** Vote: 32

Created at 2021-10-05 11:49:50

------------

In order to make it work, you need to:
1)Have devtools enable in maven or gradle. In maven it looks like :
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
     <scope>runtime</scope><!-- -->
    <optional>true</optional>
</dependency>
```

2)In IntellijIDEA: go in settings(ctrl +alt+s) -> Build,Execution,Deployment -> compiler, check "Build project automatically"

[](https://i.stack.imgur.com/JqRmW.png)
You can now restart your intelliJ IDE and start your application.


------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-10-07 18:43:58

------------

1.file->settings->Build,Execution,Deployment->compiler->click->Build project automatically->apply->ok
2.
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
     <scope>runtime</scope><!-- -->
    <optional>true</optional>
</dependency>
```

3.file->settings->advance option->allow auto make start even if developed application in currently running->apply->ok
4.restart IDE


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-12-27 04:10:40

------------



1. Add the POM dependency.


```
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
```


1. In IntellijIDEA: File -> Settings... -> Build, Execution, Deployment -> Compiler: Check "Build project automatically"
2. In IntellijIDEA: File -> Settings... -> Advanced Settings: Check "Allow auto-make to start even if developed application is currently running"




1. Add the "LiveReload" extension to your browser. Eg: if you are using Chrome Brower add the "LiveReload" extension by livereload.com.
2. Initially when you start your app, load in a browser tab and hover over the 'LiveReload' extension to ensure it is enabled. The tooltip should show as "LiveReload is connected, click to disable Has access to this site". LiveReload Tooltip


Once these are set up, to ensure both Automatic Restart and LiveReload;

1. Do changes to both the Static files and Java files in the application that is already running.
2. Build the project (Build -> 'Build Project' or Ctrl+F9).
3. Observe the application automatically restarting with "LiveReload server is running on port .. " in the IDE console and the browser is refreshed.




------------
    
    