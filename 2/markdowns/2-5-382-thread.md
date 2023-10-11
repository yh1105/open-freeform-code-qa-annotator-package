
# Post \#67114506 [Link](https://stackoverflow.com/questions/67114506/)

## Error inflating class com.google.android.material.slider.Slider

**Vote**: 9 (409/702) **Views**: 5896 (503/702) 

**Internal ID** \#2-5-382

Created at 2021-04-15 18:55:32

Tags: `java` `android` `xml` `fragment` `android-inflate`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I want to inflate a layout containing a single seekBar. in this way, I've created a java class that extends fragment class and then I inflate the layout. but the following error occurred:
Binary XML file line #9: Binary XML file line #9: Error inflating class com.google.android.material.slider.Slider
```
warming.XML is in the following:

    <?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.slider.Slider
        android:layout_width="327dp"
        android:layout_height="37dp"
        android:layout_alignParentStart="true"
        android:layout_alignParentTop="true"
        android:layout_alignParentEnd="true"
        android:layout_alignParentBottom="true"
        android:layout_marginStart="42dp"
        android:layout_marginTop="217dp"
        android:layout_marginEnd="42dp"
        android:layout_marginBottom="349dp"
        android:valueFrom="0"
        android:valueTo="300"
        app:activeTrackColor="#ff4500"
        app:inactiveTrackColor="#fbb999" >

    </com.google.android.material.slider.Slider>
</RelativeLayout>
```

and firstPage.java that inflate layout warming.XML is in the following:
```
public class firstPage extends Fragment {
   @Nullable
   @Override
   public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
      return inflater.inflate(R.layout.warming,container,false);
   }
}
```

using the following code, I want to execute the firstPage.java class:
```
getSupportFragmentManager().beginTransaction().replace(R.id.fragment_container,new firstPage()).commit();
```

what's the matter? can anyone help me? I've searched for this problem in StackOverflow and some other websites, and there were similar questions but no one can solve my problem.

full Stack Trace is here:
```
04-16 12:45:37.991 18832-18832/? I/art: Late-enabling -Xcheck:jni
04-16 12:45:37.991 18832-18832/? I/art: VMHOOK: rlim_cur : 0 pid:18832
04-16 12:45:38.132 18832-18832/com.example.myapplication W/System: ClassLoader referenced unknown path: /data/app/com.example.myapplication-2/lib/arm
04-16 12:45:38.443 18832-18832/com.example.myapplication W/art: Before Android 4.1, method android.graphics.PorterDuffColorFilter androidx.vectordrawable.graphics.drawable.VectorDrawableCompat.updateTintFilter(android.graphics.PorterDuffColorFilter, android.content.res.ColorStateList, android.graphics.PorterDuff$Mode) would have incorrectly overridden the package-private method in android.graphics.drawable.Drawable
04-16 12:45:38.513 18832-18832/com.example.myapplication I/art: Rejecting re-init on previously-failed class java.lang.Class<androidx.core.view.ViewCompat$2>
04-16 12:45:38.513 18832-18832/com.example.myapplication I/art: Rejecting re-init on previously-failed class java.lang.Class<androidx.core.view.ViewCompat$2>
04-16 12:45:38.914 18832-18848/com.example.myapplication I/Adreno-EGL: <qeglDrvAPI_eglInitialize:379>: EGL 1.4 QUALCOMM build: Nondeterministic_AU_msm8974_LA.BF.1.1.3__release_AU (I3fa967cfef)
    OpenGL ES Shader Compiler Version: E031.28.00.02
    Build Date: 09/29/15 Tue
    Local Branch: mybranch14683032
    Remote Branch: quic/master
    Local Patches: NONE
    Reconstruct Branch: NOTHING
04-16 12:45:51.663 18832-18832/com.example.myapplication E/AndroidRuntime: FATAL EXCEPTION: main
    Process: com.example.myapplication, PID: 18832
    android.view.InflateException: Binary XML file line #8: Binary XML file line #8: Error inflating class com.google.android.material.slider.Slider
        at android.view.LayoutInflater.inflate(LayoutInflater.java:539)
        at android.view.LayoutInflater.inflate(LayoutInflater.java:423)
        at com.example.myapplication.firstPage.onCreateView(firstPage.java:16)
        at androidx.fragment.app.Fragment.performCreateView(Fragment.java:2600)
        at androidx.fragment.app.FragmentManagerImpl.moveToState(FragmentManagerImpl.java:881)
        at androidx.fragment.app.FragmentManagerImpl.moveFragmentToExpectedState(FragmentManagerImpl.java:1238)
        at androidx.fragment.app.FragmentManagerImpl.moveToState(FragmentManagerImpl.java:1303)
        at androidx.fragment.app.BackStackRecord.executeOps(BackStackRecord.java:439)
        at androidx.fragment.app.FragmentManagerImpl.executeOps(FragmentManagerImpl.java:2079)
        at androidx.fragment.app.FragmentManagerImpl.executeOpsTogether(FragmentManagerImpl.java:1869)
        at androidx.fragment.app.FragmentManagerImpl.removeRedundantOperationsAndExecute(FragmentManagerImpl.java:1824)
        at androidx.fragment.app.FragmentManagerImpl.execPendingActions(FragmentManagerImpl.java:1727)
        at androidx.fragment.app.FragmentManagerImpl$2.run(FragmentManagerImpl.java:150)
        at android.os.Handler.handleCallback(Handler.java:739)
        at android.os.Handler.dispatchMessage(Handler.java:95)
        at android.os.Looper.loop(Looper.java:168)
        at android.app.ActivityThread.main(ActivityThread.java:5885)
        at java.lang.reflect.Method.invoke(Native Method)
        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:797)
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:687)
     Caused by: android.view.InflateException: Binary XML file line #8: Error inflating class com.google.android.material.slider.Slider
        at android.view.LayoutInflater.createView(LayoutInflater.java:645)
        at android.view.LayoutInflater.createViewFromTag(LayoutInflater.java:764)
        at android.view.LayoutInflater.createViewFromTag(LayoutInflater.java:704)
        at android.view.LayoutInflater.rInflate(LayoutInflater.java:835)
        at android.view.LayoutInflater.rInflateChildren(LayoutInflater.java:798)
        at android.view.LayoutInflater.inflate(LayoutInflater.java:515)
        at android.view.LayoutInflater.inflate(LayoutInflater.java:423) 
        at com.example.myapplication.firstPage.onCreateView(firstPage.java:16) 
        at androidx.fragment.app.Fragment.performCreateView(Fragment.java:2600) 
        at androidx.fragment.app.FragmentManagerImpl.moveToState(FragmentManagerImpl.java:881) 
        at androidx.fragment.app.FragmentManagerImpl.moveFragmentToExpectedState(FragmentManagerImpl.java:1238) 
        at androidx.fragment.app.FragmentManagerImpl.moveToState(FragmentManagerImpl.java:1303) 
        at androidx.fragment.app.BackStackRecord.executeOps(BackStackRecord.java:439) 
        at androidx.fragment.app.FragmentManagerImpl.executeOps(FragmentManagerImpl.java:2079) 
        at androidx.fragment.app.FragmentManagerImpl.executeOpsTogether(FragmentManagerImpl.java:1869) 
        at androidx.fragment.app.FragmentManagerImpl.removeRedundantOperationsAndExecute(FragmentManagerImpl.java:1824) 
        at androidx.fragment.app.FragmentManagerImpl.execPendingActions(FragmentManagerImpl.java:1727) 
        at androidx.fragment.app.FragmentManagerImpl$2.run(FragmentManagerImpl.java:150) 
        at android.os.Handler.handleCallback(Handler.java:739) 
        at android.os.Handler.dispatchMessage(Handler.java:95) 
        at android.os.Looper.loop(Looper.java:168) 
        at android.app.ActivityThread.main(ActivityThread.java:5885) 
        at java.lang.reflect.Method.invoke(Native Method) 
        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:797) 
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:687) 
     Caused by: java.lang.reflect.InvocationTargetException
        at java.lang.reflect.Constructor.newInstance(Native Method)
        at android.view.LayoutInflater.createView(LayoutInflater.java:619)
        at android.view.LayoutInflater.createViewFromTag(LayoutInflater.java:764) 
        at android.view.LayoutInflater.createViewFromTag(LayoutInflater.java:704) 
        at android.view.LayoutInflater.rInflate(LayoutInflater.java:835) 
        at android.view.LayoutInflater.rInflateChildren(LayoutInflater.java:798) 
        at android.view.LayoutInflater.inflate(LayoutInflater.java:515) 
        at android.view.LayoutInflater.inflate(LayoutInflater.java:423) 
        at com.example.myapplication.firstPage.onCreateView(firstPage.java:16) 
        at androidx.fragment.app.Fragment.performCreateView(Fragment.java:2600) 
        at androidx.fragment.app.FragmentManagerImpl.moveToState(FragmentManagerImpl.java:881) 
        at androidx.fragment.app.FragmentManagerImpl.moveFragmentToExpectedState(FragmentManagerImpl.java:1238) 
        at androidx.fragment.app.FragmentManagerImpl.moveToState(FragmentManagerImpl.java:1303) 
        at androidx.fragment.app.BackStackRecord.executeOps(BackStackRecord.java:439) 
        at androidx.fragment.app.FragmentManagerImpl.executeOps(FragmentManagerImpl.java:2079) 
        at androidx.fragment.app.FragmentManagerImpl.executeOpsTogether(FragmentManagerImpl.java:1869) 
        at androidx.fragment.app.FragmentManagerImpl.removeRedundantOperationsAndExecute(FragmentManagerImpl.java:1824) 
        at androidx.fragment.app.FragmentManagerImpl.execPendingActions(FragmentManagerImpl.java:1727) 
        at androidx.fragment.app.FragmentManagerImpl$2.run(FragmentManagerImpl.java:150) 
        at android.os.Handler.handleCallback(Handler.java:739) 
        at android.os.Handler.dispatchMessage(Handler.java:95) 
        at android.os.Looper.loop(Looper.java:168) 
        at android.app.ActivityThread.main(ActivityThread.java:5885) 
        at java.lang.reflect.Method.invoke(Native Method) 
        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:797) 
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:687) 
     Caused by: java.lang.UnsupportedOperationException: Failed to resolve attribute at index 11: TypedValue{t=0x2/d=0x7f0300b4 a=3}
        at android.content.res.TypedArray.getColorStateList(TypedArray.java:482)
        at com.google.android.material.resources.MaterialResources.getColorStateList(MaterialResources.java:71)
        at com.google.android.material.slider.Slider.processAttributes(Slider.java:365)
        at com.google.android.material.slider.Slider.<init>(Slider.java:261)
        at com.google.android.material.slider.Slider.<init>(Slider.java:252)
        at java.lang.reflect.Constructor.newInstance(Native Method) 
        at android.view.LayoutInflater.createView(LayoutInflater.java:619) 
        at android.view.LayoutInflater.createViewFromTag(LayoutInflater.java:764) 
        at android.view.LayoutInflater.createViewFromTag(LayoutInflater.java:704) 
        at android.view.LayoutInflater.rInflate(LayoutInflater.java:835) 
        at android.view.LayoutInflater.rInflateChildren(LayoutInflater.java:798) 
        at android.view.LayoutInflater.inflate(LayoutInflater.java:515) 
        at android.view.LayoutInflater.inflate(LayoutInflater.java:423) 
        at com.example.myapplication.firstPage.onCreateView(firstPage.java:16) 
        at androidx.fragment.app.Fragment.performCreateView(Fragment.java:2600) 
        at androidx.fragment.app.FragmentManagerImpl.moveToState(FragmentManagerImpl.java:881) 
        at androidx.fragment.app.FragmentManagerImpl.moveFragmentToExpectedState(FragmentManagerImpl.java:1238) 
        at androidx.fragment.app.FragmentManagerImpl.moveToState(FragmentManagerImpl.java:1303) 
        at androidx.fragment.app.BackStackRecord.executeOps(BackStackRecord.java:439) 
        at androidx.fragment.app.FragmentManagerImpl.executeOps(FragmentManagerImpl.java:2079) 
        at androidx.fragment.app.FragmentManagerImpl.executeOpsTogether(FragmentManagerImpl.java:1869) 
        at androidx.fragment.app.FragmentManagerImpl.removeRedundantOperationsAndExecute(FragmentManagerImpl.java:1824) 
        at androidx.fragment.app.FragmentManagerImpl.execPendingActions(FragmentManagerImpl.java:1727) 
        at androidx.fragment.app.FragmentManagerImpl$2.run(FragmentManagerImpl.java:150) 
        at android.os.Handler.handleCallback(Handler.java:739) 
        at android.os.Handler.dispatchMessage(Handler.java:95) 
        at android.os.Looper.loop(Looper.java:168) 
        at android.app.ActivityThread.main(ActivityThread.java:5885) 
        at java.lang.reflect.Method.invoke(Native Method) 
        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:797) 
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:687) 
04-16 12:45:53.565 18832-18832/com.example.myapplication D/Process: killProcess, pid=18832
04-16 12:45:53.670 18832-18832/com.example.myapplication D/Process: com.android.internal.os.RuntimeInit$UncaughtHandler.uncaughtException:113 java.lang.ThreadGroup.uncaughtException:693 java.lang.ThreadGroup.uncaughtException:690
```

and in the following is the Build.gradle code:
```
plugins {
    id 'com.android.application'
}

android {
    compileSdkVersion 30

    defaultConfig {
        applicationId "com.example.myapplication"
        minSdkVersion 22
        targetSdkVersion 30
        versionCode 1
        versionName "1.0"

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {

    implementation 'androidx.appcompat:appcompat:1.2.0'
    implementation 'com.google.android.material:material:1.2.0-alpha02'
//    implementation 'com.google.android.material:material:1.3.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.0.4'
//    implementation 'com.android.support:design:28.+'
//    implementation 'com.google.android.material:material:1.3.0'
    testImplementation 'junit:junit:4.+'
    androidTestImplementation 'androidx.test.ext:junit:1.1.2'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.3.0'
}
```

and if you want to ask me "why don't you use the "material:1.3.0" version", I can say that because of rendering problem associated with Navigation Drawer Layout, according to some instruction in StackOverFlow, I changed it to "material:1.2.0-alpha02" and rendering problem was solved.
I ask that question in another post. you can access that question in the following link:
[https://stackoverflow.com/questions/67095956/com-google-android-materialmaterial1-2-0-alpha02-does-not-include-some-instr](https://stackoverflow.com/questions/67095956/com-google-android-materialmaterial1-2-0-alpha02-does-not-include-some-instr)
and here is Theme.xml:
```
<resources xmlns:tools="http://schemas.android.com/tools">
<!-- Base application theme. -->
<style name="Theme.MyApplication" parent="Theme.MaterialComponents.DayNight.DarkActionBar">
    <!-- Primary brand color. -->
    <item name="colorPrimary">@color/purple_200</item>
    <item name="colorPrimaryVariant">@color/purple_700</item>
    <item name="colorOnPrimary">@color/black</item>
    <!-- Secondary brand color. -->
    <item name="colorSecondary">@color/teal_200</item>
    <item name="colorSecondaryVariant">@color/teal_200</item>
    <item name="colorOnSecondary">@color/black</item>
    <!-- Status bar color. -->
    <item name="android:statusBarColor" tools:targetApi="l">?attr/colorPrimaryVariant</item>
    <!-- Customize your theme here. -->
</style>
```




----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2021-05-10 07:10:41

------------

well, after a lot of searching, finally I found out there is a simillar object to slider named  <androidx.appcompat.widget.AppCompatSeekBar> in android studio. so, you can use this object instead of "slider" object. so there is no inflating error in this way.


------------
    
    
## Answer \#1

 Vote: 19

Created at 2021-08-25 13:37:43

------------

I created the following theme:
```
<style name="MyMaterialTheme" parent="Theme.MaterialComponents.Light.NoActionBar">
    <!-- Add attributes here -->
    <item name="colorPrimary">@color/itimalia_pink_califoria</item>
</style>
```

then:
```
<com.google.android.material.slider.Slider
    android:id="@+id/slider"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:theme="@style/MyMaterialTheme"/>
```

and it works.


------------
    
    
## Answer \#2

 Vote: 4

Created at 2022-05-30 18:18:46

------------

If your application theme is not derived from Material Design, you can get past the runtime crash by assigning the following theme to the Slider widget.
android:theme="@style/Theme.MaterialComponents.DayNight.NoActionBar"


------------
    
    
## Answer \#3

 Vote: 3

Created at 2022-03-02 09:08:39

------------

It is due to your base app theme is not a child of the material theme. In my case, I was using the app theme of Appcompat child..but I was using a view that inherits everything from the MaterialComponenets theme. And it was throwing the error.
App theme:
```
<style name="AppTheme" parent="Theme.AppCompat.DayNight.NoActionBar">
   ...
</style>
```

So I created another style as,
```
<style name="WD_Slider" parent="Theme.MaterialComponents.DayNight.NoActionBar">
    ...
</style>
```

and referred that style in slider view as,
```
android:theme="@style/WD_Slider"
```

With this approach, you can use any view which is not a child of your app theme and override the required theme exactly when you use it in XML at the view level.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-04-15 19:47:21

------------

You have to use `app:trackColorActive` and `app:trackColorInactive` instead of
`app:activeTrackColor` and `app:inactiveTrackColor`:
```
<com.google.android.material.slider.Slider
        ...
        app:trackColorActive="#ff4500"
        app:trackColorInactive="#fbb999"/>
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2021-11-04 15:19:16

------------

I had the same issue. Upgrading to `com.google.android.material:material:1.4.0` did not help. As stated above, the only solution was to set `android:theme` to something, and then it worked.


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-11-16 19:24:01

------------

This has given me exact tracking in kotlin
```
seekBar?.setOnSeekBarChangeListener(object : SeekBar.OnSeekBarChangeListener {
        override fun onProgressChanged(seekBar: SeekBar, progress: Int,
                                       fromUser: Boolean) {
            
            tvProgress.text = "CAD "+ progress.toString()
            val bounds: Rect = seekBar.thumb.bounds
            tvProgress.x = (seekBar.left + bounds.left + seekBar.thumbOffset).toFloat()

         }

        override fun onStartTrackingTouch(seekBar: SeekBar) {
         }

        override fun onStopTrackingTouch(seekBar: SeekBar) {
            seekBarValue = seekBar.progress
         }
    })




<RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:visibility="visible">

            <TextView
                android:id="@+id/tvProgress"
                android:layout_width="@dimen/_50sdp"
                android:layout_height="@dimen/_30sdp"
                 android:background="@drawable/bg_box_border_unselected"
                android:backgroundTint="@color/black"
                android:fontFamily="@font/open_sans_regular"
                android:gravity="center"
                android:paddingStart="@dimen/_3sdp"
                android:paddingEnd="@dimen/_3sdp"
                android:text="CAD 20"
                android:textColor="@color/white" />

            <androidx.appcompat.widget.AppCompatSeekBar
                android:id="@+id/seekBar"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_below="@+id/tvProgress"
                 android:max="28"
                android:maxHeight="@dimen/_5sdp"
                android:min="20"
                android:minHeight="@dimen/_5sdp"
                android:padding="@dimen/_10sdp"
                android:progress="20"
                android:progressDrawable="@drawable/custom_seekbar"
                android:thumb="@drawable/ic_thumb_seekbar" />
        </RelativeLayout>
```



------------
    
    