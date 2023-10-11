
# Post \#57105154 [Link](https://stackoverflow.com/questions/57105154/)

## Shortcut to extract Flutter widget from UI layout

**Vote**: 35 (176/702) **Views**: 35936 (206/702) 

**Internal ID** \#2-5-337

Created at 2019-07-19 03:30:08

Tags: `android-studio` `intellij-idea` `flutter` `widget`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `dart` `java`)

----------

**Notepad**


----------

If I have a complex layout that I want to simplify, what is the shortcut in Android Studio (or IntelliJ) to extract the widget out into a method? 

Example: 

I want to extract the three main widgets in the Stack.

```
class BodyWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Stack(
      children: <Widget>[
        Container(
          decoration: BoxDecoration(
            image: DecorationImage(
              image: AssetImage('assets/image.jpg'),
              fit: BoxFit.cover,
            ),
          ),
        ),
        Align(
          alignment: Alignment(-0.7, -0.7),
          child: Container(
            height: 300,
            child: RichText(
              text: TextSpan(
                text: 'My text',
                style: TextStyle(
                  color: Colors.white70,
                  fontSize: 30,
                ),
              ),
            ),
          ),
        ),
        Align(
          alignment: Alignment.bottomCenter,
          child: Text(
            'Some other text',
            style: TextStyle(
              color: Colors.white70,
              fontSize: 20.0,
              fontWeight: FontWeight.w900,
              letterSpacing: 5.0,
            ),
          ),
        ),
      ],
    );
  }
}
```


I could do it by hand but I'm looking for a shortcut.


----------
        
## Answer \#0

**Accepted** Vote: 71

Created at 2019-07-19 03:30:08

------------

I'm updating this answer for both Android Studio and VS Code


# Android Studio




### Method 1



Shortcut keys:

- - 

You can also accomplish the same thing by right clicking the widget name and choosing  from the context menu.


### Method 2



You can also extract a widget into a method or new widget from the Flutter Outline menu.


1. Click Flutter Outline on the top left side
2. Select the widget in the outline
3. Right click and choose Extract method... or Extract widget...
4. Give it a name



[](https://i.stack.imgur.com/ZnKdK.png)


# Visual Studio Code



Put your cursor on the widget name and press + on a Mac or + on a PC. Then choose  or  from the context menu.

![](https://i.stack.imgur.com/z99t3.png)


------------
    
    
## Answer \#1

 Vote: 28

Created at 2019-07-19 05:30:37

------------

To Extract particular code of Flutter in  to `varialbe`, `constant`, `method`, or as a `Widget`.



[](https://i.stack.imgur.com/Zo16P.png)



[](https://i.stack.imgur.com/2Q9RL.png)

`Shortcuts` vary as per your Android studio configuration but shortcuts are  also written over the there too ease your task.


------------
    
    
## Answer \#2

 Vote: 9

Created at 2021-07-11 18:30:00

------------

In my case `Ctrl+Alt+W` hotkey in Android Studio didn't work. To fix it just open Keymap Settings and re-set this hotkey - Android Studio will remove conflicting shortcuts and it became work.


------------
    
    
## Answer \#3

 Vote: 4

Created at 2020-01-14 20:44:18

------------

You can do this easily with command +.

In [this post](https://medium.com/flutter-community/flutter-ide-shortcuts-for-faster-development-2ef45c51085b) it is very well explained.


------------
    
    
## Answer \#4

 Vote: 2

Created at 2021-03-07 08:14:13

------------

In the android studio, you can extract widget by class
```
Ctrl + Alt + W
```

or extract widget by method
```
Ctrl + Alt + M
```



------------
    
    
## Answer \#5

 Vote: 1

Created at 2023-01-13 19:06:01

------------

To fix the issue of the Kotlin shortcut overriding the "Extract New Widget" command, go to "Preferences > Keymap" and search for "Run Scratch File." Right-click and remove the Kotlin shortcut.


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-05-24 10:10:50

------------

The New Shortcut Key to Extract Widget In Android Studio is



------------
    
    