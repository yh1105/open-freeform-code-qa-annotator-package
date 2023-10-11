
# Post \#69696321 [Link](https://stackoverflow.com/questions/69696321/)

## IntelliJ - Invalid source release: 17

**Vote**: 122 (41/702) **Views**: 99975 (87/702) 

**Internal ID** \#2-5-346

Created at 2021-10-24 11:32:37

Tags: `java` `gradle` `intellij-idea` `java-17`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I've created a new Java project in IntelliJ with Gradle that uses Java 17. When running my app it has the error `Cause: error: invalid source release: 17`.

I've installed `openjdk-17` through `IntelliJ` and set it as my `Project SDK`.
The `Project language level` has been set to `17 - Sealed types, always-strict floating-point semantics`.
[](https://i.stack.imgur.com/h6OBn.png)
In `Modules -> Sources` I've set the `Language level` to `Project default (17 - Sealed types, always strict floating-point semantics)`.
[](https://i.stack.imgur.com/WxHWG.png)
In `Modules -> Dependencies` I've set the `Module SDK` to `Project SDK openjdk-17`.
[](https://i.stack.imgur.com/0DMAk.png)
In `Settings -> Build, Execution, Deployment -> Compiler -> Java Compiler` I've set the `Project bytecode version` to `17`.
[](https://i.stack.imgur.com/jZxak.png)

```
plugins {
    id 'org.springframework.boot' version '2.5.6'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
}

group = 'com.app'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '17'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-websocket'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    implementation 'com.fasterxml.jackson.core:jackson-core:2.13.0'
    implementation 'com.fasterxml.jackson.core:jackson-databind:2.13.0'
}

test {
    useJUnitPlatform()
}
```

I've looked at all of the answers [here](https://stackoverflow.com/questions/46280859/intellij-idea-error-java-invalid-source-release-1-9) but I can't seem to fix this. I must be missing something but I can't find it. I've not had any problems using Java 8 or 11.
How do I resolve this?


----------
        
## Answer \#0

**Accepted** Vote: 271

Created at 2021-12-03 13:28:36

------------

In intellij just set Gradle JVM to Java version 17.
"File -> Settings.. -> Build, Execution, Deployment -> Build Tools -> Gradle"
there select your project and set Gradle JVM to your java 17.0.1


------------
    
    
## Answer \#1

 Vote: 34

Created at 2022-03-15 08:48:44

------------

Set Gradle JVM to target build jvm
File -> Settings.. ->[](https://i.stack.imgur.com/Qzhlk.png)


------------
    
    
## Answer \#2

 Vote: 33

Created at 2022-07-06 21:48:06

------------

A picture is worth a thousand words!

[](https://i.stack.imgur.com/iVbHB.png)


------------
    
    
## Answer \#3

 Vote: 22

Created at 2021-10-24 11:47:33

------------

The message typically entails that your  environment variable points to a different  version.
Here are the steps to follow:
- - - `echo $JAVA_HOME`- `echo %JAVA_HOME%`- - `export JAVA_HOME=/path/to/openjdk-17`- `set JAVA_HOME=path\to\openjdk-17`- - `sourceCompatibility`
You should be able to build your project.

## EDIT: Gradle Toolchain


You may need also to instruct  to use a different  than the one it uses itself by setting the  plugin toolchain to your target version:
```
// build.gradle
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(17)
    }
}
```



------------
    
    
## Answer \#4

 Vote: 3

Created at 2022-03-17 13:16:49

------------

There could be many reasons due to which this error is thrown by intellij
- 
```
echo $JAVA_HOME
```

if JAVA_HOME is not correct, update the JAVA_HOME environment variable.
- 
```
File > Project Structure > Project Setting > Project -> SDK and Language level should match

File > Project Structure > Project Setting > Modules -> Language Level should match with SDK
```

- 
```
Preferences > Build, Execution and Deployment > Gradle > Gradle JVM should match with SDK (Preferences > Build, Execution and Deployment > maven if you're using maven)

Preferences > Build, Execution and Deployment > Compiler > Java Compiler > Project Bytecode version (this should match with your SDK as well)
```



------------
    
    
## Answer \#5

 Vote: 2

Created at 2021-10-24 11:58:35

------------

Set JAVA_ HOME TO JDK 17  and check this by Opening cmd -> javac. This should return the current version of java set in your machine


------------
    
    
## Answer \#6

 Vote: 2

Created at 2022-08-24 20:02:30

------------

in build.gradle set sourceCompatibility = '11' not sourceCompatibility = '17'


------------
    
    
## Answer \#7

 Vote: 1

Created at 2021-10-25 08:47:20

------------

If you start your project from a main method directly invoked from Intelij then the missing dialog execution configurations may be the cause of the  error.
Always check the execution configurations to make sure the correct JRE folder is plugged in. In latest versions of jdk there is not a specific jre folder but the complete jdk package, so make sure it points at this folder.
[](https://i.stack.imgur.com/D4Qcy.png)
[](https://i.stack.imgur.com/oKiVJ.png)


------------
    
    
## Answer \#8

 Vote: 1

Created at 2022-02-15 20:45:08

------------

you can define `org.gradle.java.home` in  `~/.gradle/gradle.properties` like:
```
org.gradle.java.home=/path/to/jdk17
```

this will cover both `gradle` cli and default value for IntelliJ (if not specified otherwise)
(windows users file at `C:\Users\<username>\.gradle\gradle.properties`)


------------
    
    
## Answer \#9

 Vote: 1

Created at 2022-02-26 10:30:44

------------

I was running into this issue, , while the 
The reason is probably, that I had downloaded the JDK from the relatively new way of doing it directly from the IDE
[](https://i.stack.imgur.com/GrsXo.png)
My guess is, that this JDK with its path that is set here
[](https://i.stack.imgur.com/btVvN.png)
Is only known to IntelliJ and likely dynamically  when running a run configuration, and .
I tried to figure out what is happening, so I've added this task which in both cases prints the same path to an older local JDK.
```
tasks.build.doFirst {
    println "used jdk path: " + System.getenv('JAVA_HOME')
}
```

> C:\Program
Files\GraalVM\graalvm-ce-java11-21.1.0
Afterwards I've , run configuration and terminal execution. Now the , while  the same , and the  with
> ERROR: JAVA_HOME is set to an invalid directory: C:\Program
Files\GraalVM\graalvm-ce-java11-21.1.0Please set the JAVA_HOME variable in your environment to match the
location of your Java installation.
I had expected the error in both cases, but it seems the JAVA_HOME directory is  and in some way other than with the env variable, IntelliJ has to tell Gradle to use the right JDK from the .



------------
    
    
## Answer \#10

 Vote: 0

Created at 2023-02-15 15:34:55

------------

Had the same problem, tried everything said above but no luck.
Then saw this path `C:\Program Files\Common Files\Oracle\Java\javapath` present in Systesm variable `path`. Deleting it solved the issue.
: Don't forget to restart the terminal.


------------
    
    