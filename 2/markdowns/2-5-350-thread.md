
# Post \#59316518 [Link](https://stackoverflow.com/questions/59316518/)

## Gradle task assembleDebug failed with exit code 1 (Runtime Exception)

**Vote**: 4 (532/702) **Views**: 24284 (268/702) 

**Internal ID** \#2-5-350

Created at 2019-12-13 05:18:34

Tags: `java` `android` `gradle` `flutter` `dart`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `dart` `java`)

----------

**Notepad**


----------

while starting a flutter application on vs code i am getting the following errors.

> Launching lib\main.dart on RMX1801 in debug mode...
  Exception in thread "main" java.lang.RuntimeException: Timeout of 120000 reached waiting for exclusive access to file: C:\Users\sanjay.gradle\wrapper\dists\gradle-5.6.2-all\9st6wgf78h16so49nn74lgtbb\gradle-5.6.2-all.zip
      at org.gradle.wrapper.ExclusiveFileAccessManager.access(ExclusiveFileAccessManager.java:61)
      at org.gradle.wrapper.Install.createDist(Install.java:48)
      at org.gradle.wrapper.WrapperExecutor.execute(WrapperExecutor.java:128)
      at org.gradle.wrapper.GradleWrapperMain.main(GradleWrapperMain.java:61)
  [!] Gradle threw an error while trying to update itself. Retrying the update...
  Exception in thread "main" java.lang.RuntimeException: Timeout of 120000 reached waiting for exclusive access to file: C:\Users\sanjay.gradle\wrapper\dists\gradle-5.6.2-all\9st6wgf78h16so49nn74lgtbb\gradle-5.6.2-all.zip
      at org.gradle.wrapper.ExclusiveFileAccessManager.access(ExclusiveFileAccessManager.java:61)
      at org.gradle.wrapper.Install.createDist(Install.java:48)
      at org.gradle.wrapper.WrapperExecutor.execute(WrapperExecutor.java:128)
      at org.gradle.wrapper.GradleWrapperMain.main(GradleWrapperMain.java:61)
  [!] Gradle threw an error while trying to update itself. Retrying the update...
  Gradle task assembleDebug failed with exit code 1
  Exited (sigterm)

I am not getting why this is occurring . What can be the reason for this and how can this be solved?


----------
        
## Answer \#0

**Accepted** Vote: 23

Created at 2020-01-03 05:54:16

------------

It is caused by the Gradle distribution version used. To fix this, either downgrade your Gradle version to the previous or upgrade it to the next stable one. These are the [Gradle distribution](https://services.gradle.org/distributions/) versions available.

Go to 

> android / gradle / wrapper / gradle-wrapper.properties

and replace the current Gradle version this  (or choose another from [Gradle distribution](https://services.gradle.org/distributions/))

> distributionUrl=https://services.gradle.org/distributions/gradle-6.0.1-all.zip

[](https://i.stack.imgur.com/bvzPx.png)

I upgraded my Gradle version from 5.6.2 to 6.0.1, and it works fine with me.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2020-01-07 09:48:41

------------

I was also experiencing a similar problem. The following worked for me !

C:\Users\uejaz.gradle\wrapper\dists\gradle-5.6.2-all\9st6wgf78h16so49nn74lgtbb
Move to this directory and delete gradle-5.6.2-all.zip and gradle-5.6.2 folder

Goto [https://downloads.gradle-dn.com/distributions/gradle-5.6.2-all.zip](https://downloads.gradle-dn.com/distributions/gradle-5.6.2-all.zip) and download the zip file
Replace the .zip file with the one you had deleted earlier and extract the folder "gradle-5.6.2" in the same directory. I hope it will solve all your gradle-related problems.

Note: if your gradle version is different than mine, just change the version in the link provided above to download your gradle version.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-06-23 04:50:41

------------

For VS Code. if All answers are not working. do this.
I was finally able to solve the problem. The problem is with the gradle and low internet bandwidth.
Just delete the C:\Users\USERNAME.gradle\wrapper\dists\gradle-5.6.2-all
and download it manually from [https://downloads.gradle-dn.com/distributions/gradle-5.6.2-all.zip] or delete the old gradle files and let them download manually by using flutter run Be sure to check your gradle version before proceeding.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-02-09 16:02:00

------------

I solved this error by upgrading the Gradle version.

Just go to:

```
android > grader\wrapper > Gradle-wrapper.properties
```


and then replace the current Gradle version to the new one:

```
distributionUrl=https\://services.gradle.org/distributions/gradle-5.1.1-all.zip
```



You can see the Gradle versions in this page: [https://gradle.org/releases](https://gradle.org/releases)


------------
    
    
## Answer \#4

 Vote: 1

Created at 2020-10-15 19:04:51

------------

The steps worked for me are as follows

1. Go to wrapper file in android
2. Delete 'gradle' file inside wrapper file
3. Then delete 'gradlew' file
4. Go to 'gradle-wrapper.properties'
5. Replace distributionUrl with belo link https://services.gradle.org/distributions/gradle-6.4.1-all.zip
6. Exit from android studio and re-open


Hope this would be useful


------------
    
    
## Answer \#5

 Vote: 0

Created at 2020-02-24 06:43:57

------------

Open the flutter projects's android directory from Android Studio . It will automatically shows you to delete old gradle version and update to current version.


------------
    
    
## Answer \#6

 Vote: 0

Created at 2021-10-24 07:43:43

------------


1. Open terminal
2. Go to project directory
3. Run Below command


> flutter clean
> flutter pub get
> flutter run
This solved my problem.


------------
    
    