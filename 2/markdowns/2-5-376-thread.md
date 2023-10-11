
# Post \#54137286 [Link](https://stackoverflow.com/questions/54137286/)

## Error: Java: invalid target release: 11 - IntelliJ IDEA

**Vote**: 187 (22/702) **Views**: 369358 (13/702) 

**Internal ID** \#2-5-376

Created at 2019-01-10 21:38:59

Tags: `java` `intellij-idea` `java-11`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I am trying to build an application which was built using java 8, now it's upgraded to java 11. I installed [Java 11](https://www.oracle.com/technetwork/java/javase/downloads/index.html) using [an oracle article](https://docs.oracle.com/en/java/javase/11/install/installation-jdk-microsoft-windows-platforms.html#GUID-A7E27B90-A28D-4237-9383-A58B416071CA) in my  and I use  as my IDE.

I changed my system environment variables and set the 

```
JAVA_HOME to C:\Program Files\Java\jdk-11.0.1
```


And added that to the `Path` variable.

```
C:\>java -version
java version "11.0.1" 2018-10-16 LTS
Java(TM) SE Runtime Environment 18.9 (build 11.0.1+13-LTS)
Java HotSpot(TM) 64-Bit Server VM 18.9 (build 11.0.1+13-LTS, mixed mode)
```


When I build my application in IntelliJ, this is what I get:

```
Information:java: Errors occurred while compiling module 'test-domain_main'
Information: javac 1.8.0_171 was used to compile java sources
Information:1/10/2019 4:21 PM - Compilation completed with 1 error and 0 warnings in 1s 199ms
Error:java: invalid target release: 11
```


This is what I've tried so far:


1. I changed .idea/compiler.xml target values from 8 to 11 but that didn't help. Also, verified the Target bytecode version in settings > Build, Execution, Deployment > Compiler > Java Compiler and all my modules are set to 11.
2. Went to file > Project Structure > SDKs *(currently I have 1.7 and 1.7 listed)* > Add new SDK > JDK > After that, I selected C:\Program Files\Java\jdk-11.0.1 But it errors out with "The selected directory is not a valid home for JDK"



I am not sure if I installed the wrong JDK 11, because in my `C:\Program Files\Java\`, I see separate JDK and JRE folders for `1.7` and `1.8` but only JDK folder for `11.0.1`

Or is it something else I need to change? 


----------
        
## Answer \#0

**Accepted** Vote: 320

Created at 2019-02-06 20:29:34

------------

I've got the same issue as stated by Gryu.
Same Intellij 2018 3.3
I was able to start my project by setting (like stated by Grigoriy)
```
File->Project Structure->Modules ->> Language level to 8 ( my maven project was set to 1.8 java)
```

AND
```
File -> Settings -> Build, Execution, Deployment -> Compiler -> Java Compiler -> 8 also there
```

I hope it would be useful


------------
    
    
## Answer \#1

 Vote: 68

Created at 2019-01-16 10:36:36

------------

- - To resolve this issue I've changed  ->>
Language level to 10. - And check  ->> 
and  versions. I have 11 there.- Now I don't get these notifications and the error.It could be useful for someone like me, having the most recent Idea and getting the same error.


------------
    
    
## Answer \#2

 Vote: 50

Created at 2019-10-18 08:00:43

------------

1 of the 2 methods will fix the problem:
 (recommended)
Specify the Java version in your build file.
E.g., For Gradle, go to build.gradle file and specify the source and target compatability like mentioned in the [docs](https://docs.gradle.org/current/userguide/java_plugin.html#sec:java-extension) and click on the refresh button in the Gradle tool window.
For example, to set source and target compatability to Java version 17:
```
java {
    sourceCompatibility = JavaVersion.VERSION_17
    targetCompatibility = JavaVersion.VERSION_17
}
```


If building a project through a build system (Maven, Gradle etc..) via command-line works but IntelliJ show `Invalid target release` error, then do the following,

1. Close IntelliJ
2. Go to the directory of the project
3. Delete the .idea/ directory
4. Start IntelliJ with the project's directory


This will re-create the .idea/ directory and will no longer show the error.
NOTE: Any repository specific IntelliJ settings that you have added would be deleted when the .idea/ directory is deleted and they will be re-created with the defaults.


------------
    
    
## Answer \#3

 Vote: 42

Created at 2020-11-14 19:13:43

------------

Below changes worked for me and hope the same works for you.

1. Change the version of Java from 11 to 8 in pom.xml file <java.version>1.8</java.version>
2. Go to, File -> Settings -> Build, Execution, Deployment -> Compiler -> Java Compiler Here, in Module column you can see your project listed and in Target bytecode version column, Java version for the project is already specified(mostly 11), change it to 8
3. Go to, File -> Project Structure -> Modules. Here, in Source tab, you can see Language level option, choose 8 - Lambdas, type annotations etc.. Finally, choose Apply and OK, you're good to go.




------------
    
    
## Answer \#4

 Vote: 23

Created at 2019-01-10 22:30:18

------------

Please [update](http://www.jetbrains.com/idea/download/index.html) to IntelliJ IDEA 2018.x to get Java 11 support. Your IntelliJ IDEA version was released before Java 11 and doesn't support this Java version.


------------
    
    
## Answer \#5

 Vote: 18

Created at 2021-01-06 21:00:03

------------

January 6th, 2021
This is what worked for me.
Go to File -> Project Structure and select the "Dependencies" tab on the right panel of the window. Then change the "Module SDK" using the drop-down like this. Then apply changes.
![image 1](https://i.stack.imgur.com/R8gxS.png)


------------
    
    
## Answer \#6

 Vote: 17

Created at 2019-12-04 13:35:55

------------

There is also the possibility of Maven using a different version of JDK, in that case you can set Maven to use the project default JDK version. 

[](https://i.stack.imgur.com/D2AWt.png)


------------
    
    
## Answer \#7

 Vote: 16

Created at 2021-07-13 13:06:48

------------

In newer versions of Intellij, check that jdk 11 is correctly configured:

1. Right click on your project then Open Module Settings. Check the settings listed below: SDKs (Under Platform Settings) > Add the jdk 11 you want located on your computer. Modules (Under Project Settings) > Set language level to jdk 11 for sources and dependencies. Project (Under Project Settings) > Select jdk 11 as Project SDK.
2. Then File > Settings > Java Compiler. Check the following settings: Use compiler: Javac Project bytecode version: 11 Per-module bytecode version: the target bytecode version for all modules must be set to 11.




------------
    
    
## Answer \#8

 Vote: 12

Created at 2022-02-19 15:34:40

------------

My solution:

1. Go to File > Settings > Build, Execution, Deployment > Build Tools > Gradle
2. Select Gradle JVM: [SDK VERSION]
3. Compile


[](https://i.stack.imgur.com/kdiRP.png)


------------
    
    
## Answer \#9

 Vote: 9

Created at 2019-04-20 09:18:52

------------

Got this same error after updating my Idea and I resolved it as follows: 

File >> Project Structure... >> Project 

“Project language level:” was set to “11 - Local variable syntax for lambda parameters” 
so I changed it to “SDK default (8 - Lambdas, type annotations etc.)” then applied the change 
and the error was resolved.


------------
    
    
## Answer \#10

 Vote: 8

Created at 2020-10-25 16:17:14

------------

I was recently facing the same problem.
This Error was showing on my screen after running my project main file.

Follow the steps to resolve this error

1. File->Project Structure -> Project
2. Click New button under Project SDK: Add the latest SDK and Click OK.


[](https://i.stack.imgur.com/qRMIE.png)

[](https://i.stack.imgur.com/094W1.png)
[](https://i.stack.imgur.com/QERgB.png)



------------
    
    
## Answer \#11

 Vote: 5

Created at 2020-08-10 08:29:39

------------

If you are in Springboot please check the java version in the pom.xml file
```
<properties>
   <java.version>11</java.version>
</properties>
```

If this version doesn't match with your default version(Java) of the computer, that error can be happen.


------------
    
    
## Answer \#12

 Vote: 4

Created at 2020-07-15 16:27:25

------------

I had the issue because I'm currently upgrading my code base to JDK 11 and am switching between 8 and 11 in the branches. It seems IntelliJ doesn't like this.

If you've selected the currect JDK in your project structure (`Ctrl+Shift+Alt+S`) and still get the error, invalidate your cache `File > Invalidate Caches / Restart...`.
After restarting the IDE the error went away in my case.


------------
    
    
## Answer \#13

 Vote: 3

Created at 2019-10-19 01:38:17

------------

For me, I was having the same issue but it was with `java v8`, I am using a different version of java on my machine for my different projects. While importing one of my project I got the same problem. To check the configuration I checked all my SDK related settings whether it is in `File->Project->Project Structure / Modules` or in the Run/Debug configuration setting. Everything I set to `java-8` but still I was getting the same issue. While checking all of the configuration files I found that compiler.xml in .idea is having an entry for the bytecodeTargetLevel which was set to 11. Here if I change it to 8 even though it shows the same compiler output and removing `<bytecodeTargetLevel target="11" />` from `compiler.xml` resolve the issue. 


------------
    
    
## Answer \#14

 Vote: 3

Created at 2021-04-13 14:20:10

------------

Try this
```
<properties>
    <maven.compiler.target>1.8</maven.compiler.target>
    <maven.compiler.source>1.8</maven.compiler.source>
</properties>
```



------------
    
    
## Answer \#15

 Vote: 2

Created at 2019-09-03 11:52:26

------------

I Could say I had similar issue. My case: I switched from new version to old version of the project in workspace, try to run single junit which require recompil, recompilation error was thrown with invalid target. 

From Projects settings (F4) in IntelliJ everything was looking good and java was set to 1.7. But when I try recompile from IDE error was thrown because of wrong target level. After checking I found that in one of the iml file language was set to JDK_11. After manual change to JDK_1_7 everything back to normal. 

Worth to also manual check lang level in *iml files created by IDE.


------------
    
    
## Answer \#16

 Vote: 2

Created at 2020-06-24 17:05:11

------------

My project module was set to 8 but the pom.xml was set to 11.  When I changed from 8 to 11 in the module, it worked.


------------
    
    
## Answer \#17

 Vote: 2

Created at 2020-07-25 07:33:40

------------

i also got same error , i just change the java version in pom.xml from 11 to 1.8 and it's work fine.


------------
    
    
## Answer \#18

 Vote: 2

Created at 2020-10-07 08:56:37

------------

In your pom.xml file  inside that <java.version> write "8" instead write "11" ,and RECOMPILE your pom.xml file
And tadaaaaaa it works !


------------
    
    
## Answer \#19

 Vote: 2

Created at 2020-10-17 19:27:00

------------

I tried all the above and found this secret sauce

1. make sure pom.xml specifies your desired jdk.
2. make sure maven specifies your desired jdk.
3. make sure Projects specifies your desired jdk.
4. make sure Modules specifies your integer jdk AND Dependencies specifies your jdk. hth.




------------
    
    
## Answer \#20

 Vote: 2

Created at 2021-01-12 15:49:04

------------

2021 Jan.12th
the whole point for me is to explicitly specify the version as java 11 in my pom.xml, and I solved the problem by taking the following steps.

1. open terminal and input: java -version, then i got 1.8, and i went to .bash_profile to switch the java version to 11.2 as i have multiple java version installed. please remember "restart" your terminal and check your java version again to confirm switch is successful.
2. Then i went to File -> Project Structure to make sure my IntelliJ using the same version as my env, which is 11.2.
3. RESTART your Intellij, mvn clean install, solved, hope this could help someone that has same issue, thanks.




------------
    
    
## Answer \#21

 Vote: 2

Created at 2021-04-02 00:14:18

------------

```
1. pom.xml (start with this important)
    - <java.version>1.8</java.version>
    * Enable auto import on pop up
    * Wait until it loads

2. Project Structure
    - Project
        - Project SDK - 1.8 
        - Project language Level - 8
    - Modules
        - Sources
            - Language level - 8
            - Dependencies - Project SDK 1.8

3. Delete idea folder
4. Close window
5. Reopen window (make sure it loads at bottom)
```



------------
    
    
## Answer \#22

 Vote: 2

Created at 2021-09-27 12:37:30

------------

If your project is built using gradle, you should update:
```
File > Settings > Build, Execution, Deployment > Build Tools > Gradle
```

And update `Gradle JVM` with version 11.


------------
    
    
## Answer \#23

 Vote: 1

Created at 2019-06-20 10:08:20

------------

Just had same error. Problem was that I had empty `package-info.java file`. As soon as I added package name inside it worked...


------------
    
    
## Answer \#24

 Vote: 1

Created at 2020-08-07 23:14:07

------------

I also got a similar error but in my case it was Error:java: invalid source release: 11... So I just changed all the annotations of java 11 to java 8 and even if the annotation was java 8, I just leaved it like that. I hope it helps you to fix the issue.


------------
    
    
## Answer \#25

 Vote: 1

Created at 2020-10-04 17:41:44

------------

I changed file -> project structure -> project settings -> modules   In the source tab,  I set the Language Level from : 14, or 11, to:  "Project Default".  This fixed my issue.


------------
    
    
## Answer \#26

 Vote: 1

Created at 2020-10-24 08:41:13

------------

I added these two lines to build.gradle file
```
compileJava.options.fork = true
                                              
compileJava.options.forkOptions.executable = 'C:\\Program Files\\Java\\jdk-11.0.8'
```

and it works
I am using windows and my project based on gradle
my jdk path ->  'C:\Program Files\Java\jdk-11.0.8'
please provide your jdk path


------------
    
    
## Answer \#27

 Vote: 1

Created at 2022-01-21 05:42:18

------------

I tried all the solutions above
- 
I set all the above to java 8 (or 1.8) as that is the library I have on my machine. It still did not work.
The issue was that the build.gradle file had
> sourceCompatibility = '11'
Changed this to 8 and it stopped throwing 'compile failed invalid release 11' error
solution reference: [github forum](https://github.com/gradle/gradle/issues/12803)


------------
    
    
## Answer \#28

 Vote: 1

Created at 2022-08-26 12:33:20

------------

For me, the problem causing this error message was that my JAVA_HOME environment variable was still pointing to the JDK 1.8 directory.
I made my JAVA_HOME environment variable pointing to the JDK 11 directory and it worked.


------------
    
    
## Answer \#29

 Vote: 1

Created at 2022-11-13 15:48:14

------------

Add this with maven compler plugin
```
<dependencies>
...
</dependencies>
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```



------------
    
    
## Answer \#30

 Vote: 1

Created at 2023-01-02 13:55:19

------------

Removing maven pom properties:
```
<maven.compiler.source>11</maven.compiler.source>
<maven.compiler.target>11</maven.compiler.target>
```

Solved it for me (so not setting it to 1.8 as suggested above)


------------
    
    
## Answer \#31

 Vote: 0

Created at 2021-07-15 07:50:48

------------

I got the same java 11 target error and my issue was                 maven-compiler-plugin
outdated version.
upgraded to lastest and everything worked


------------
    
    
## Answer \#32

 Vote: 0

Created at 2022-01-27 10:00:33

------------

Setting the right path for Java home in `gradle.properties` solved the issue for me
gradle properties path : C:\Users<username>.gradle\gradle.properties
```
org.gradle.java.home=yourjdk11homepath
Example : org.gradle.java.home=C\:/Program Files/Java/jdk-11.0.8
```



------------
    
    
## Answer \#33

 Vote: 0

Created at 2022-05-09 10:17:43

------------

If configuring the `Project Properties` never worked for you, you should also remember to change the SDK version for the maven or `Gradle` which could be pointing to the wrong path or the `JDK` version.
To do this:

1. Click on File | Settings | Build, Execution, Deployment | Build Tools
2. If maven, click maven then Runner, and set JRE to use project JDK.
3. If Gradle, Click Gradle then set the Gradle JVM.




------------
    
    
## Answer \#34

 Vote: 0

Created at 2022-10-04 10:05:05

------------

Ok, this question is pretty old and probably not relevant for the most of the developers, but I would like to show the working solution to this problem.
First - my understanding of the problem:
Normally (by default) IntelliJ uses it's internal compiler to compile the sources. Probably there's a way to specify the compiler, but I still haven't found the way to configure this. THIS IS JUST A GUESS: maybe IntelliJ tries to use the project JDK compiler and if this fails - uses the internal. In my case the IDEA is old version and the internal JRE (and compiler) is version 8. Of course this compiler won't be able to compile Java 11 sources - it issues error "java: invalid target release: 11"
The solution:
To make sure that JDK compiler is used - we must delegate the build job to Maven. Maven will use the proper javac executable and it will be able to compile Java 11 sources to whatever target version you want.
File -> Settings -> Build, Execution, Deployment -> Build Tools -> Maven -> Runner
then check "Delegate IDE build/run actions to maven"
That's it - now your old IntelliJ IDEA will be able to build/run projects with any new JDK version.


------------
    
    
## Answer \#35

 Vote: 0

Created at 2022-10-12 11:56:35

------------

Another solution worked for me, but the root cause was I messed up in maven configuration in the past.
After all suggestions my `JAVA_HOME` when I called it through terminal shown that I was using `Java 11`, but when I checked `mvn -v` I had got information that Maven uses `Java 1.8`... 
That was weird. I went through all suggested IntelliJ settings (Project Structure, Compiler version etc.) and everything was pointing to use `Java 11`
But finally I found I messed up that cofiguration in the past.

I'm using Mac and in my case the root cause was Overriding default system `JAVA_HOME` though Maven settings in `~/.mavenrc` configuration file.
Inside that I had overriden `JAVA_HOME` property pointing to `1.8` library.

The reason why I did it was a previous problems with maven on other project and I needed to force maven to use `Java 1.8` so when I switched to another project it caused finally problems.

I know it's very specific but maybe there is someone else who went through all suggestions and nothing worked, and overrided this variable too.


------------
    
    