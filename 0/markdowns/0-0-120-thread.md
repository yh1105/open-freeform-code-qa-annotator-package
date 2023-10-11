
# Post \#57338968 [Link](https://stackoverflow.com/questions/57338968/)

## Error with react-native run-android after upgrade to react-native version 0.60.4

**Vote**: 10 (387/702) **Views**: 22513 (276/702) 

**Internal ID** \#0-0-120

Created at 2019-08-03 13:55:44

Tags: `java` `android` `react-native` `gradle` `android-gradle-plugin`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `java` `javascript`)

----------

**Notepad**


----------

I am upgrading react-native from 0.59.5 to 0.60.4 with my existing application. But facing a problem as below

```
* Where:
Build file '/home/bingl/Projects/blackstar/frontend/android/app/build.gradle'

* What went wrong:
Could not compile build file '/home/bingl/Projects/blackstar/frontend/android/app/build.gradle'.
> startup failed:
  General error during semantic analysis: Unsupported class file major version 57

  java.lang.IllegalArgumentException: Unsupported class file major version 57
    at groovyjarjarasm.asm.ClassReader.<init>(ClassReader.java:184)
    at groovyjarjarasm.asm.ClassReader.<init>(ClassReader.java:166)
    at groovyjarjarasm.asm.ClassReader.<init>(ClassReader.java:152)
    at groovyjarjarasm.asm.ClassReader.<init>(ClassReader.java:273)
    at org.codehaus.groovy.ast.decompiled.AsmDecompiler.parseClass(AsmDecompiler.java:81)
    at org.codehaus.groovy.control.ClassNodeResolver.findDecompiled(ClassNodeReso
```


And my build.gradle is 

```
android {
    compileSdkVersion 28
    buildToolsVersion "28.0.3"

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }

    defaultConfig {
        applicationId "com.frontend"
        minSdkVersion 16
        targetSdkVersion 28
        versionCode 1
        versionName "1.0"
    }
    splits {
        abi {
            reset()
            enable enableSeparateBuildPerCPUArchitecture
            universalApk false  // If true, also generate a universal APK
            include "armeabi-v7a", "x86", "arm64-v8a", "x86_64"
        }
    }
    buildTypes {
        release {
            minifyEnabled enableProguardInReleaseBuilds
            proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
        }
    }
    // applicationVariants are e.g. debug, release
    applicationVariants.all { variant ->
        variant.outputs.each { output ->
            // For each separate APK per architecture, set a unique version code as described here:
            // http://tools.android.com/tech-docs/new-build-system/user-guide/apk-splits
            def versionCodes = ["armeabi-v7a":1, "x86":2, "arm64-v8a": 3, "x86_64": 4]
            def abi = output.getFilter(OutputFile.ABI)
            if (abi != null) {  // null for the universal-debug, universal-release variants
                output.versionCodeOverride =
                        versionCodes.get(abi) * 1048576 + defaultConfig.versionCode
            }
        }
    }
```


I have absolutely no clue what this error is about and could not find much helpful articles. Please help me out.


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2019-08-04 04:29:09

------------

Looks like Gradle is currently erroring on builds using openJDK-13. 

Here's a [Github issue](https://github.com/gradle/gradle/issues/8681) thread.

Check the version of Java and JDK versions on your system by running the following command on in your terminal:

```
file /etc/alternatives/java /etc/alternatives/javac
```


OR

```
file `which java javac`
```


This will list the current installations on your system. If you see `openjdk-13` anywhere, you'll have to downgrade to `openjdk-8`.

Check this [link](https://askubuntu.com/a/1133226) for downgrading steps.


------------
    
    
## Answer \#1

 Vote: 15

Created at 2021-04-27 13:47:30

------------

For those who are here for `java.lang.IllegalArgumentException: Unsupported class file major version 60`
One of the reason for the same is:
Java version 16 installed which is not yet supported by react-native.
So For MacOS,
```
cd /Library/Java/JavaVirtualMachines
ls
```

- Check the available JDKs- Remove the jdk-16 if present using this,
`sudo rm -rf jdk-16.jdk/`
Basically, you can remove JDKs other than `adoptopenjdk-8.jdk` which will be present if you followed [React-Native Env Setup](https://reactnative.dev/docs/environment-setup).


------------
    
    
## Answer \#2

 Vote: 3

Created at 2020-07-20 11:15:26

------------

In this file  change  on this 


------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-11-22 19:24:18

------------

Also, you can use this super lightweight utility [jEnv](https://www.jenv.be/) to manage multiple versions of Java on your machine. I've used it for many years with good results.
```
$ jenv add /usr/lib/jvm/java-8-openjdk-amd64/
openjdk64-1.8.0.292 added
1.8.0.292 added
1.8 added

$ jenv add /usr/lib/jvm/java-16-openjdk-amd64/
openjdk64-16.0.1 added
16.0.1 added
16.0 added
16 added
```

Then place a file at `android/.java-version` with the contents:
```
1.8
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-06-23 06:23:30

------------

.
In `Activity.main`
```
Toast.makeText(this, "Your message, Toast.LENGTH_SHORT).show();
```

![checkout the output](https://i.stack.imgur.com/uIoL4.png)


------------
    
    