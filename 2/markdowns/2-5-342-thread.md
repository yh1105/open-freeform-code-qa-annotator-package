
# Post \#63312296 [Link](https://stackoverflow.com/questions/63312296/)

## Method setView is deprecated

**Vote**: 14 (336/702) **Views**: 10000 (415/702) 

**Internal ID** \#2-5-342

Created at 2020-08-08 05:57:05

Tags: `java` `android` `android-toast`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

While I'm doing custom toast on my app, I noticed that setView is deprecated.
[](https://i.stack.imgur.com/CwrA4.png)
Does anyone have a solution for this?
```
toast.setView(customView);
```



----------
        
## Answer \#0

**Accepted** Vote: 11

Created at 2020-08-29 08:59:44

------------

Since `setView` is deprecated:
> This method was deprecated in API level 30.
Custom toast views are deprecated. Apps can create a standard text toast with the `makeText(android.content.Context, java.lang.CharSequence, int)` method, or use a `Snackbar` when in the foreground. Starting from Android `Build.VERSION_CODES#R`, apps targeting API level `Build.VERSION_CODES#R` or higher that are in the background will not have custom toast views displayed.
This makes sense Toasts can be displayed on Top of other Apps, some Apps can trick users by creating custom Toasts on Top of other Apps for their advantage even if their App is on the Background. But if your App is in the Foreground your custom Toast will still be shown in all Android Versions.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-03-03 16:55:47

------------

The solution with setting a custom view on Toast is deprecated for API 30 and forward.
Documentation says
> This method was deprecated in API level 30. Custom toast views are
deprecated. . Starting from Android
Build.VERSION_CODES#R, apps targeting API level Build.VERSION_CODES#R
or higher that are in the background will not have custom toast views
displayed.
There is a walkaround for some cases though
```
Toast.makeText(applicationContext,
                HtmlCompat.fromHtml("<font color='red'>custom toast message</font>", HtmlCompat.FROM_HTML_MODE_LEGACY),
                Toast.LENGTH_LONG).show()
```

Html color tag can also be  `<font color='#ff6347'>`
For every modification that has to do with the displayed text, the above solution would be enough. You can for example make the text  by inserting `<b>my text</b>` or you maybe want to change the  with `<font font-family='...'> my text </font>` For all those changes that solution will be enough.
If you want to modify the container though with properties like  the only alternative is to use Snackbar. View can not be modified for Toast anymore.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-04-07 22:30:23

------------

As other answers already mentioned the reasons and to use snackbar/deafult toast, I will provide the alternative I use.
We may not able to customise the toast background but we can use [Spannable string](https://developer.android.com/reference/android/text/SpannableString.html) to customise the text displayed in the toast. The default toast background will be shown but using different span styles available under package: android.text.style, we can achieve custom text style in the toast message.
Example custom toast which shows toast with text color in green and text size of 200 pixels.
```
val spannableString = SpannableString("Custom toast")
spannableString.setSpan(
    ForegroundColorSpan(Color.GREEN), 0, spannableString.length, 0
)
spannableString.setSpan(
    AbsoluteSizeSpan(200), 0, spannableString.length, 0
)
val toast = Toast.makeText(context, spannableString, Toast.LENGTH_SHORT)
toast.show()
```

Spannable string reference: [spantastic text styling with spans](https://medium.com/androiddevelopers/spantastic-text-styling-with-spans-17b0c16b4568)
(PS: We could always show custom dialogs when app is running or custom notifications to show important messages to the users.)


------------
    
    
## Answer \#3

 Vote: 0

Created at 2023-02-08 18:25:38

------------

I wrote a short Kotlin extension function to do this using spannable.  Note that it returns a toast that you  need to "show".
```
fun Context.spToPix(sp: Int): Int =
    (sp * Resources.getSystem().displayMetrics.scaledDensity).toInt()
fun Context.fontSizeToast(
    fontSize: Int,
    mess: String,
    displayTime: Int = Toast.LENGTH_LONG
): Toast {
    val ssMess = SpannableString(mess)
    ssMess.setSpan(
        AbsoluteSizeSpan(spToPix(fontSize)),
        0, ssMess.length, 0
    )
    return Toast.makeText(this, ssMess, displayTime)
}
```



------------
    
    