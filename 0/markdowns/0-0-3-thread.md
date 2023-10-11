
# Post \#54415332 [Link](https://stackoverflow.com/questions/54415332/)

## Command Failed: gradlew.bat installDebug error whenever installing dependencies like navigation, firebase, icons etc in React-Native

**Vote**: 19 (269/702) **Views**: 168464 (47/702) 

**Internal ID** \#0-0-3

Created at 2019-01-29 06:48:54

Tags: `react-native` `react-navigation` `gradlew` `react-native-firebase`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

When ever i install any dependency in my react native project and when ever i use link command for e.g react-native link react-native-gesture-handler this causes me an error shown in the image [1].
It doesn't build the project shows the error gradlew.bat
The following code shows my package.json file
```
{
  "name": "navigations",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start",
    "test": "jest"
  },
  "dependencies": {
    "react": "16.6.3",
    "react-native": "0.58.3",
    "react-native-gesture-handler": "^1.0.15",
    "react-navigation": "^3.0.9"
  },
  "devDependencies": {
    "babel-core": "7.0.0-bridge.0",
    "babel-jest": "24.0.0",
    "jest": "24.0.0",
    "metro-react-native-babel-preset": "0.51.1",
    "react-test-renderer": "16.6.3"
  },
  "jest": {
    "preset": "react-native"
  }
}
```

![](https://i.stack.imgur.com/hmxzX.png)


----------
        
## Answer \#0

**Accepted** Vote: 39

Created at 2019-02-15 10:20:30

------------

If you have WINDOWS machine, please follow these steps:

```
Optional (
  Delete iOS, Android folders

  Run `react-native eject` ( that command will rebuild deleted folders for you )
)
```


Then :

1) `cd ./android`

2) `./gradlew.bat installDebug` ( with dot at the beggining )

Once it's successfully finished

3) `cd ../` ( go back to root folder ) 

4) `react-native run-android`

And fingers crossed for it to work as expected :) [for me it does]


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-03-31 08:21:18

------------

```
cd ./android
create file file local.properties
```

then past this code `sdk.dir=C\:\\Users\\muhammad ali\\AppData\\Local\\Android\\Sdk` change location to as per your sdk is located Done.
you already have local.properties file good.
find similar file of code `sdk.dir=C\:\\Users\\muhammad ali\\AppData\\Local\\Android\\Sdk` change location to as per your sdk is located Done.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2019-02-03 16:44:49

------------

I had the same issue today... Wondering if you solved it?

go to android folder then settings.gradle file

You may find it on line 3

'..\node_modules\react-native-gesture-handler\android'

and replace all these backward slashes with forward like this

'../node_modules/react-native-gesture-handler/android'

Let me know if this helps...


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-09-13 11:49:05

------------

Here is how I resolved this problem.

[](https://i.stack.imgur.com/gTu4U.png)

[](https://i.stack.imgur.com/EQKOt.png)
`npx react-native run-android`
If not build successfully. Then use the following step.


1. start metro bundler: npx react-native start --reset-cache --port 8084
2. run-android : npx react-native run-android


Or If still not built successfully. Then try following step.

Enjoy and Happy Coding...


------------
    
    
## Answer \#4

 Vote: 0

Created at 2019-02-06 04:15:23

------------

Make sure you already have installed python 2 and last version of jdk
And make sure you have already set ANDROID_HOME 


------------
    
    
## Answer \#5

 Vote: 0

Created at 2019-07-10 15:52:33

------------

I got this same issue in when creating splash screen set to the MainActivity.java. 
Then I downgrade the react native version (0.59.8). I didn't change the gradle version. 


------------
    
    
## Answer \#6

 Vote: 0

Created at 2021-07-21 14:21:39

------------

To solve this issue just change the compileSdkVersion in your build.gradle file.
Open  android > build.gradle file.
Change compileSdkVersion from 29 to 30.
Change targetSdkVersion from 29 to 30.
now run again.
/[if this not working then checkout this link](https://exerror.com/error-command-failed-gradlew-bat-appinstalldebug-preactnativedevserverport8081/)


------------
    
    
## Answer \#7

 Vote: 0

Created at 2022-04-19 12:36:31

------------

I had the same problem and used unusual solution but is helped me.
after create your react-native project, open android folder by android studio,
it will config your gradle Correctly and then you can run your app on simulator


------------
    
    
## Answer \#8

 Vote: 0

Created at 2023-01-05 11:37:02

------------

Sometimes this error is caused by the debug keystore at least in my case that was the issue.

I ran this command to generate a new debug keystore
Windows Users:
```
keytool -genkey -v -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

MAC / Linux Users:
```
$ keytool -genkey -v -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

Answer the prompt questions.
When the keystore is generated successfully go to `android/app` and place the keystore file there while updating your `android/app/build.gradle` file with:
```
signingConfigs {
        debug {
            storeFile file('the-keystore-name.keystore')
            storePassword 'the-keystore-prompt-password'
            keyAlias 'the-keystore-alise-name'
            keyPassword 'the-keystore-promtp-password'
        }

    }
```

Thanks it! Just run yarn android again it should build now.



------------
    
    
## Answer \#9

 Vote: -1

Created at 2022-09-13 18:07:34

------------

I went into android studio and edited my emulated devices' storage capacity (RAM, VM heap, Internal Storage, removed SD card) in advanced settings. I believe I bumped every setting up.
[Virtual Device settings](https://i.stack.imgur.com/ysNrq.png)
I'm not sure if this will solve your issue, however, it did resolve mine.
After that, I recreated a new project (npx react-native ...) then it worked like a charm.
As far as my computer goes, it has plenty of storage and ram so I wasn't too concerned with those limitations. Good luck
API = 33 
Android Studio Version = 2021.2


------------
    
    