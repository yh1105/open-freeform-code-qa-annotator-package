
# Post \#58411279 [Link](https://stackoverflow.com/questions/58411279/)

## Java with maven wouldn't build: Cannot run program "cmd" "Malformed argument has embedded quote"

**Vote**: 40 (158/702) **Views**: 71018 (122/702) 

**Internal ID** \#2-5-348

Created at 2019-10-16 10:35:12

Tags: `java` `windows` `maven` `netbeans-11`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I've a fresh install of netbean 11.1. 
Now I'm trying to build a project but I get the following error output: 

> Cannot run program "cmd" (in directory "C:\projects\open"): Malformed
  argument has embedded quote: "C:\Program
  Files\NetBeans-11.1\netbeans\java\maven\bin\mvn.cmd"

```
cd C:\projects\open; "JAVA_HOME=C:\\Program Files\\Java\\jdk-11.0.5" cmd /c "\"\"C:\\Program Files\\NetBeans-11.1\\netbeans\\java\\maven\\bin\\mvn.cmd\" -DskipTests=true -Dmaven.ext.class.path=\"C:\\Program Files\\NetBeans-11.1\\netbeans\\java\\maven-nblib\\netbeans-eventspy.jar\" -Dfile.encoding=UTF-8 clean install\"" Cannot run program "cmd" (in directory "C:\projects\open"): Malformed argument has embedded quote: "C:\Program Files\NetBeans-11.1\netbeans\java\maven\bin\mvn.cmd" -DskipTests=true -Dmaven.ext.class.path="C:\Program Files\NetBeans-11.1\netbeans\java\maven-nblib\netbeans-eventspy.jar" -Dfile.encoding=UTF-8 clean install
```


I've build this project with netbeans 11.1 before but have a new pc.
and a fresh install, tho I'm sure there was no problem last time I tried to install everything.


----------
        
## Answer \#0

**Accepted** Vote: 73

Created at 2019-12-05 17:00:18

------------

Although this problem can be fixed by installing jdk1.8.0_221, but actually you don't need to downgrade your JDK. Just try these:


1. First exit netbeans IDE if it's open, then open netbeans configuration file from here: netbeans-Install-Dir/etc/netbeans.conf (Note: for me netbeans-Install-Dir is C:\Program Files\NetBeans 8.2\etc)
2. Add the below arguments



```
-J-Djdk.lang.Process.allowAmbiguousCommands=true
```



1. to the beginning of the string that you find at this line:



```
netbeans_default_options="-J-client -J-Xss2m -J-Xms32m ......."
```


Sample screenshot as shown below:
[](https://i.stack.imgur.com/KhCNs.png)
4. Save the change and start your netbeans IDE.


------------
    
    
## Answer \#1

 Vote: 62

Created at 2019-10-21 10:49:58

------------

I have checked the release notes for JDK 13.0.1 at [https://www.oracle.com/technetwork/java/javase/13-0-1-relnotes-5592797.html#JDK-8221858](https://www.oracle.com/technetwork/java/javase/13-0-1-relnotes-5592797.html#JDK-8221858)

This behavior is a regression from a security fix for JDK-8221858 (not public). Follow the link for a full description. The fix is part of JDK 8u231, JDk 11.0.5, 13.0.1 etc.

To resolve this problem, append `-J-Djdk.lang.Process.allowAmbiguousCommands=true` to `netbeans_default_options` in `<netbeans-dir>\etc\netbeans.conf`.


------------
    
    
## Answer \#2

 Vote: 11

Created at 2019-11-11 10:42:19

------------

I had this error with `adopt8-hotspot (AdoptOpenJDK-1.8.0_232-b09)` and `sbt-0.13.X` for an old project.

Problem solved after setting the environment variable in windows via 

```
set JAVA_TOOL_OPTIONS=-Djdk.lang.Process.allowAmbiguousCommands=true
```



------------
    
    
## Answer \#3

 Vote: 8

Created at 2019-10-16 11:30:09

------------

Did you by any chance install the latest critical update for java? jdk 1.7_241? I installed that today and was having the exact same problem. I downgraded my jdk version and it solved it completely.

EDIT: Apologies, I noticed you're using netbeans 11.1 so you probably have a later java version, in any case, you could try downgrading your java version to a previous release if you happened to update it today as the other version might have the same problem.


------------
    
    
## Answer \#4

 Vote: 6

Created at 2019-10-16 13:31:07

------------

At the time that I'm writing, I had only JDK13 installed only on my machine. After unzipped my netbeans I've got the same problem with the internal maven of my netbeans 11.1.

Apparently netbeans 11.1 does not work properly with the JDK13. I just installed the JDK 8 and the problem now is solved, without any additional configuration or parameter change.

Nontheless, since I need the JDK13, I have both installed but my netbeans.conf file is pointing to JDK8. Later, you can define the new Java platform in netbeans project configuration.

I hope this might help.

Cheers!


------------
    
    
## Answer \#5

 Vote: 4

Created at 2020-04-10 17:36:47

------------

just add -J-Djdk.lang.Process.allowAmbiguousCommands=true to netbeans_default_options in netbeans.conf


------------
    
    
## Answer \#6

 Vote: 1

Created at 2019-10-19 08:08:00

------------

It seems to happen with all recently released jdk version (11.0.5 and 13.0.1 at least), even with the latest Netbeans 11.2vc1 (and witnessed on NB 11.1 too).
Downgrade your jdk to 11.0.4 or 13.0.0. It fixed the issue for me


------------
    
    
## Answer \#7

 Vote: 1

Created at 2019-10-20 10:17:30

------------

The problem also occurs in version jdk-8u231. Fixing installing jdk-8u221 version


------------
    
    
## Answer \#8

 Vote: 0

Created at 2019-10-18 18:25:28

------------

Apache NetBeans 11.1 does indeed not support JDK 13, that's what the upcoming Apache NetBeans 11.2 is for -- latest beta: bit.ly/apache-netbeans-11-2-beta-3


------------
    
    
## Answer \#9

 Vote: 0

Created at 2019-10-19 08:51:53

------------

I had the same problem with oracle jdk 13.0.1 and netbeans 11.1, but it worked for me with openjdk 13.0.1 ([https://jdk.java.net/13/](https://jdk.java.net/13/)).


------------
    
    