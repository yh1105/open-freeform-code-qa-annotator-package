
# Post \#60353491 [Link](https://stackoverflow.com/questions/60353491/)

## "Unrecognized option: --add-opens" Hyperledger Besu

**Vote**: 3 (575/702) **Views**: 28418 (238/702) 

**Internal ID** \#2-5-352

Created at 2020-02-22 15:05:36

Tags: `java` `windows` `hyperledger`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I want to get started with Hyperledger Besu, after following the steps of the [official documentation here](https://besu.hyperledger.org/en/stable/HowTo/Get-Started/Starting-node/) and running the following command:

`bin\besu --help`

I get the following error:

```
C:\Users\user\Desktop\besu-1.3.9>bin\besu --help
Unrecognized option: --add-opens
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
```


I get the same thing when running `bin\besu` or `bin\besu -help`.

I don't know if the problem is with java's installation or with hyperledger besu trying to run unvalid/unrecognised option `bin\besu --add-opens`. I tried uninstalling then reinstalling java but this did not solve the issue, here is java's version:

```
C:\Users\user>java -version
Picked up _JAVA_OPTIONS: -Xmx512m
java version "1.8.0_241"
Java(TM) SE Runtime Environment (build 1.8.0_241-b07)
Java HotSpot(TM) Client VM (build 25.241-b07, mixed mode)
```


Any help would be appreciated!


----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2020-02-22 15:22:25

------------

I have no prior experience with the library which is in discussion, but I have just had a look at the [official docs](https://besu.hyperledger.org/en/stable/HowTo/Get-Started/Install-Binaries/) of the library which you are trying to use, and the installation instructions state that you require Java 11+ to complie
> Hyperledger Besu requires:MacOS High Sierra  or  versions; earlier versions are not supported.
From your question, it looks like your system is running Java8. Trying bumping it up to java 11 and try starting the binary again.


------------
    
    
## Answer \#1

 Vote: 1

Created at 2020-03-26 03:23:26

------------

The script you're running adds `--add-opens java.base/sun.security.provider=ALL-UNNAMED` to the java command line that starts Besu. As others have noted, the `--add-opens` parameter was made available in Java 9; all it does is prevent a warning from being issued when any class uses reflection to access an internal JDK class (in this case, the Sun security package). I've been starting Besu  this parameter for weeks with no problems, so you could have simply removed the parameter from the Besu start script or -- better -- upgraded to at least the Java 11 or higher version that Besu expects. According to Oracle, this command line parameter will be removed eventually, but that's a problem for the Besu maintainers, not you.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-11-17 08:11:15

------------

change the java home version path from jdk8 path to jdk11 in environment variable
as
JAVA_HOME : {jdk11 directory path}


------------
    
    
## Answer \#3

 Vote: 0

Created at 2023-03-03 11:44:04

------------

> Simple Solution if one is using Tomcat to deploy their project. Go to
the Run Configuration of Tomcat. Find this argument and remove the
same. No need to change the Java path if you still want to use Java 8.


------------
    
    