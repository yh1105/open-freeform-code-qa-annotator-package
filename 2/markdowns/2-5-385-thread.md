
# Post \#71427323 [Link](https://stackoverflow.com/questions/71427323/)

## Gradle sync failed: Plugin [id: 'com.android.application', version: '7.1.2', apply: false] was not found in any of the following sources:

**Vote**: 4 (532/702) **Views**: 12637 (388/702) 

**Internal ID** \#2-5-385

Created at 2022-03-10 16:12:00

Tags: `java` `android` `android-studio` `gradle`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I have upgraded my Android Studio and after that I'm facing this error:
```
Gradle sync failed: Plugin [id: 'com.android.application', version: '7.1.2', apply: false] was not found in any of the following sources:
                - Gradle Core Plugins (plugin is not in 'org.gradle' namespace)
                - Plugin Repositories (could not resolve plugin artifact 'com.android.application:com.android.application.gradle.plugin:7.1.2')
                Searched in the following repositories:
                Gradle Central Plugin Repository
                Google
                MavenRepo (24 s 586 ms)
```

I have upgraded JDK to 11 but it didn't fix the error. What else can I do?


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2022-03-11 06:47:57

------------


After trying some other solutions I figured out that I didn't delete proxies in `gradle-wrapper.properties`. That was preventing my project from downloading dependencies.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-10-02 14:03:56

------------

That happen with the newer gradle versions, Go to Settings -> Plugins -> settings -> HTTP proxy settings then switch it to (auto detect proxy settings) then save and sync the project, that will solve the problem.
Check the images below for quick help
[https://i.stack.imgur.com/BSCHA.jpg](https://i.stack.imgur.com/BSCHA.jpg)
[https://i.stack.imgur.com/g4CEO.png](https://i.stack.imgur.com/g4CEO.png)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-11-02 07:08:01

------------

Disconnect the VPN, close and then open Android Studio
If you are in Iran or the Netherlands, be sure to use the American or British VPN


------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-04-07 13:52:04

------------

I faced the same problem when I intentionally updated to gradle 7.1.3 today, I just restarted the android studio and changed the gradle version to 7.1.2 and it worked.


------------
    
    
## Answer \#4

 Vote: -2

Created at 2022-03-27 05:31:51

------------

go tp setting -> then Plugins -> in plugins -> then click on three dot -> then Enable all downloaded plugins
Try this it will definitely work.
[enter image description here](https://i.stack.imgur.com/Bt4kt.jpg)


------------
    
    