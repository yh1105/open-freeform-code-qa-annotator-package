
# Post \#49940719 [Link](https://stackoverflow.com/questions/49940719/)

## How to log data to the Flutter console?

**Vote**: 169 (28/702) **Views**: 232300 (28/702) 

**Internal ID** \#2-5-335

Created at 2018-04-20 11:33:08

Tags: `intellij-idea` `console` `dart` `flutter`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `dart` `java`)

----------

**Notepad**


----------

I am a beginner and using IntelliJ IDEA, and I wanted to log data to the console?
I tried `print()` and `printDebug()`, but none of my data were showing in the Flutter console.


----------
        
## Answer \#0

**Accepted** Vote: 313

Created at 2018-04-20 11:36:10

------------

If you're inside a Flutter `Widget`, you can use [debugPrint](https://api.flutter.dev/flutter/foundation/debugPrint.html), e.g.,
```
import 'package:flutter/foundation.dart';

debugPrint('movieTitle: $movieTitle');
```

Or, use Dart's built in `log()` function
```
import 'dart:developer';

log('data: $data');
```



------------
    
    
## Answer \#1

 Vote: 36

Created at 2019-02-08 20:06:27

------------

The Dart  function outputs to the system console, which you can view using flutter logs (which is basically a wrapper around adb logcat).

If you output too much at once, then Android sometimes discards some log lines. To avoid this, you can use .

Found here: [https://flutter.io/docs/testing/debugging](https://flutter.io/docs/testing/debugging)


------------
    
    
## Answer \#2

 Vote: 34

Created at 2020-06-03 20:20:36

------------

log() from 'dart:developer'
- It doesn't seem to have a max length limits like `print()` or `debugPrint()`.- So it comes helpful when you want to log the whole API response.- And also helps in dart dev tools to show formatted logging.
```
import 'dart:developer';  //(auto import will do this even)
//example for api logging
  log("${response?.statusCode} :  ${response?.request?.path}",
          name: "Response", error: response.data);
```



------------
    
    
## Answer \#3

 Vote: 6

Created at 2021-07-30 18:45:22

------------

Alternatively, I have created a Logger for Flutter Apps: [https://github.com/hiteshsahu/Flutter-Logger](https://github.com/hiteshsahu/Flutter-Logger)
Simply copy `AppLog.dart` & add to yor project and use it like this:

1. AppLog.i("Info Message"); // Simple Info message
2. AppLog.i("Home Page", tag: "User Logging"); // Info message with identifier TAG



INPUT
```
AppLog.v("-----------------------------");
    AppLog.d("I am Debug Log With Default TAG");
    AppLog.i("I am Info Log With Default TAG");
    AppLog.w("I am Warn Log With Default TAG");
    AppLog.e("I am Error Log With Default TAG");
    AppLog.wtf("I am Failure Log With Default TAG");
    AppLog.v("I am Verbose Log With Default TAG");

   //With TAGS
    AppLog.v("-----------------------------");
    AppLog.d("I am Debug Log With Custom TAG", tag: "Awesome Widget");
    AppLog.i("I am Info Log With Custom TAG", tag: "Awesome Widget");
    AppLog.w("I am Warn Log With Custom TAG", tag: "Awesome Widget");
    AppLog.e("I am Error Log With Custom TAG", tag: "Awesome Widget");
    AppLog.wtf("I am Failure Log With Custom TAG", tag: "Awesome Widget");
    AppLog.v("I am Verbose Log With Custom TAG", tag: "Awesome Widget");
    AppLog.v("-----------------------------");
```

OUTPUT:
> ```
Restarted application in 347ms.
FlutterApp: -----------------------------
DEBUG|FlutterApp: I am Debug Log With Default TAG
INFOⓘ|FlutterApp: I am Info Log With Default TAG
WARN⚠️|FlutterApp: I am Warn Log With Default TAG
ERROR⚠️|️FlutterApp: I am Error Log With Default TAG
WTF¯\_(ツ)_/¯|FlutterApp: I am Failure Log With Default TAG
FlutterApp: I am Verbose Log With Default TAG
FlutterApp: -----------------------------
DEBUG|Awesome Widget: I am Debug Log With Custom TAG
INFOⓘ|Awesome Widget: I am Info Log With Custom TAG
WARN⚠️|Awesome Widget: I am Warn Log With Custom TAG
ERROR⚠️|️Awesome Widget: I am Error Log With Custom TAG
WTF¯\_(ツ)_/¯|Awesome Widget: I am Failure Log With Custom TAG
Awesome Widget: I am Verbose Log With Custom TAG
FlutterApp: -----------------------------
```


> AppLog.setLogLevel(log_priority); // logs below log_priority will be hidden
Where priority is always:
> VERBOSE<=log_priority<= FAILURE
> Priority Level: VERBOSE<DEBUG<INFO<WARNING<ERROR<FAILURE

Hide all info and Debug Logs:
```
AppLog.setLogLevel(AppLog.WARN);
AppLog.v("-----------------------------");
AppLog.d("Debug Log Will not be Visible");
AppLog.i("Info Log Will not be Visible");
AppLog.w("Warn Log Will be Visible");
AppLog.e("Error Log Will be Visible");
AppLog.wtf("Failure Log Will be Visible");
AppLog.v("Verbose Log  Will not be Visible");
AppLog.v("-----------------------------");
```

OUTPUT
> ```
WARN⚠️|FlutterApp: Warn Log Will be Visible
ERROR⚠️|️FlutterApp: Error Log Will be Visible
WTF¯\_(ツ)_/¯|FlutterApp: Failure Log Will be Visible
```

Feel free to use my logger or if you have some ideas for improvement please create PR or let me know I will improve it.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-08-28 06:38:50

------------

'''import 'dart:developer' as dev;'''
and then
dev.log('$var')


------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-10-27 11:52:17

------------

I replace to print command to `logPrint`
In Android Studio debugging `Emulator.EMULATOR` equal "" I will see to the logs
In production or testing I will set it to a number
```
const bool debugMode = (Emulator.EMULATOR=="");
void logPrint(String _s) {
    if(debugMode){
        print(_s);
    }
}
class Emulator {
    static const EMULATOR = String.fromEnvironment('EMULATOR', defaultValue: "");
}
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2023-03-01 10:02:25

------------

I recommend the package Logger :
[https://pub.dev/packages/logger](https://pub.dev/packages/logger)
This is the most used package for logging, and it clearly shows different levels of log in the console, with colors.


------------
    
    