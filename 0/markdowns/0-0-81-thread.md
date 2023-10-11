
# Post \#72564710 [Link](https://stackoverflow.com/questions/72564710/)

## React Native crash release build - FATAL EXCEPTION: create_react_context

**Vote**: 4 (532/702) **Views**: 5731 (506/702) 

**Internal ID** \#0-0-81

Created at 2022-06-09 18:01:50

Tags: `react-native`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

There is no problem in the development environment, but when I get the release build, the app crashes. (Android) I created a new project (v0.68.2), I didn't make any changes, the room crashed in the same way.
```
npx react-native run-android --variant=release
```

- 
```
FATAL EXCEPTION: create_react_context

java.lang.RuntimeException: Unable to load script. Make sure you're either running Metro (run 'npx react-native start') or that your bundle 'index.android.bundle' is packaged correctly for release.

FATAL EXCEPTION: create_react_context
Process: com.testreleasebuild, PID: 8291
java.lang.RuntimeException: Unable to load script. Make sure you're either running Metro (run 'npx react-native start') or that your bundle 'index.android.bundle' is packaged correctly for release.
    at com.facebook.react.bridge.CatalystInstanceImpl.jniLoadScriptFromAssets(Native Method)
    at com.facebook.react.bridge.CatalystInstanceImpl.loadScriptFromAssets(CatalystInstanceImpl.java:248)
    at com.facebook.react.bridge.JSBundleLoader$1.loadScript(JSBundleLoader.java:29)
    at com.facebook.react.bridge.CatalystInstanceImpl.runJSBundle(CatalystInstanceImpl.java:277)
    at com.facebook.react.ReactInstanceManager.createReactContext(ReactInstanceManager.java:1422)
    at com.facebook.react.ReactInstanceManager.access$1200(ReactInstanceManager.java:138)
    at com.facebook.react.ReactInstanceManager$5.run(ReactInstanceManager.java:1111)
    at java.lang.Thread.run(Thread.java:923)
```



----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2022-06-09 22:03:49

------------

The Android Gradle Plugin version was 7.0.4 when the project was first created. Android Studio version asked me to upgrade to 7.2.1, so I upgraded. It worked when I downgraded the version to 7.0.4.
android>build.gradle
```
classpath('com.android.tools.build:gradle:7.0.4')
```



------------
    
    
## Answer \#1

 Vote: 6

Created at 2022-10-14 02:41:01

------------

For my case, after upgrade my react-native project from version 0.69.x to 0.70.x. The Android debug build should work but release build got same issue. Change to use [Android Gradle plugin 7.2.2](https://developer.android.com/studio/releases/gradle-plugin#7-2-0) help me resolved this.
`android/build.gradle`
```
classpath 'com.android.tools.build:gradle:7.2.2'
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-08-17 22:28:33

------------

this helped me to solve it
[https://github.com/facebook/react-native/issues/29398#issuecomment-708545702](https://github.com/facebook/react-native/issues/29398#issuecomment-708545702)
```
//Fix for bundle not getting included in APK or AAB with android gradle plugin 4+
//Seems like there's a task ordering issue and the assets get computed before the bundle is copied
//This forces the copy task to run before merge resources
project.afterEvaluate {
        tasks.findAll { task ->
            task.name.startsWith('merge') && task.name.endsWith('Resources')
        }.each { t -> t.dependsOn "copyReleaseBundledJs" }
}
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-06-09 18:47:15

------------

This link may be useful : [App Crash in Release Build](https://github.com/facebook/react-native/issues/30761)
And url for similar question: [Duplicate question](https://stackoverflow.com/questions/65795530/release-app-crash-fatal-exception-create-react-context-react-native)


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-09-15 08:59:00

------------

Later i found solution to run this command
```
npx react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
```

above command modify index.android.bundle and create it if not exist,
problem mostly is that file  not exist.
> if you have got a problem with assets after run this command remove
created assets will work ie remove created drawable folders


------------
    
    