
# Post \#65001259 [Link](https://stackoverflow.com/questions/65001259/)

## Correct way to communicate the result of a background thread to the Ui Thread in Android

**Vote**: 22 (248/702) **Views**: 12435 (390/702) 

**Internal ID** \#2-5-358

Created at 2020-11-25 08:48:44

Tags: `java` `android` `multithreading` `android-asynctask` `threadpoolexecutor`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

This is one of the most confusing topics for me. .
Imagine I want to update some `TextView` with some information I just downloaded.There is 3 things I use when I need to perform background tasks:

## AsyncTask


Very easy to use, this one has the `onPostExecute()` method that will return the result directly to the UiThread so I can use a callback interface or do whatever I want. I liked this class but it's 

## ThreadPoolExecutor


This is what I actually use when need to perform background tasks and here comes my problem, the moment when I have to give the result to the UiThread. I have informed myself about `Looper` and `Handler` classes and about the `mainLooper`.
So, when I need to return some results I use the method `runOnUiThread()` that, as I have readed, just get the `Looper` of the Ui thread and post my `Runnable` to the queue.
Well this is working and I can communicate with the main thread but, I find it really ugly, and I am sure there is a more elegant way of doing it than populate all my code of "`runOnUiThread()`" methods. Also, if the background task need too much time, maybe the user already change of `Activity` or `Fragment` when the code inside `runOnUiThread()` runs what will cause `Exceptions` (I know using `LiveData` and `MVVM` pattern would solve this last problem but I am working in a legacy project and I can't refactor all the code so I am working with the clasical Activity mvc pattern)
 I really searched a lot but didn't find anything...

## Coroutines


I am actually working in a legacy project and I must use Java so can't use Kotlin `coroutines`, but I find them easy to use and so powerfull.
Any help would be appreciated!


----------
        
## Answer \#0

**Accepted** Vote: 23

Created at 2020-12-01 07:55:52

------------


In Android, when an application is launched, the system creates a thread of execution for the application, called main thread (also known as the UI thread). Google introduces the main thread and its responsible as below.
> The main thread has a very simple design: Its only job is to take and
execute blocks of work from a thread-safe work queue until its app is
terminated. The framework generates some of these blocks of work from
a variety of places. These places include callbacks associated with
lifecycle information, user events such as input, or events coming
from other apps and processes. In addition, app can explicitly enqueue
blocks on their own, without using the framework.Nearly any block of code your app executes is tied to an event
callback, such as input, layout inflation, or draw. When something
triggers an event, the thread where the event happened pushes the
event out of itself, and into the main thread’s message queue. The
main thread can then service the event.While an animation or screen update is occurring, the system tries to
execute a block of work (which is responsible for drawing the screen)
every 16ms or so, in order to render smoothly at 60 frames per second.
For the system to reach this goal, the UI/View hierarchy must update
on the main thread. However, when the main thread’s messaging queue
contains tasks that are either too numerous or too long for the main
thread to complete the update fast enough, the app should move this
work to a worker thread. If the main thread cannot finish executing
blocks of work within 16ms, the user may observe hitching, lagging, or
a lack of UI responsiveness to input. If the main thread blocks for
approximately five seconds, the system displays the Application Not
Responding (ANR) dialog, allowing the user to close the app directly.
To update a View, you must do it on the main thread, if you try to update in a background thread, the system will throw `CalledFromWrongThreadException`.

The main thread has a [Looper](https://developer.android.com/reference/android/os/MessageQueue) and a [MessageQueue](https://developer.android.com/reference/android/os/Looper) assigned with it. To update a View, we need to create a task then put it to the MessageQueue. To do that Android provides
[Handler](https://developer.android.com/reference/android/os/Handler) API which allows us to send a task to the main thread's MessageQueue for executing later.
```
// Create a handler that associated with Looper of the main thread
Handler mainHandler = new Handler(Looper.getMainLooper());

// Send a task to the MessageQueue of the main thread
mainHandler.post(new Runnable() {
    @Override
    public void run() {
        // Code will be executed on the main thread
    }
});
```

To help developers easy to communicate with the main thread from a background thread, Android offers several methods:
- [Activity.runOnUiThread(Runnable)](https://developer.android.com/reference/android/app/Activity#runOnUiThread(java.lang.Runnable))- [View.post(Runnable)](https://developer.android.com/reference/android/view/View#post(java.lang.Runnable))- [View.postDelayed(Runnable, long)](https://developer.android.com/reference/android/view/View#postDelayed(java.lang.Runnable,%20long))
Under the hood, they use Handler API to do their jobs.

[AsyncTask](https://developer.android.com/reference/android/os/AsyncTask)
This is a class that is designed to be a helper class around [Thread](https://developer.android.com/reference/java/lang/Thread) and Handler. It's responsible for:
- Create a thread or pool of thread to do a task in the background- Create a Handler that associated with the main thread to send a task to the main thread's MessageQueue.- It is deprecated from API level 30
[ThreadPoolExecutor](https://developer.android.com/reference/java/util/concurrent/ThreadPoolExecutor)
Create and handle a thread in Java is sometimes hard and might lead to a lot of bugs if developers do not handle it correctly. Java offers the ThreadPoolExecutor to create and manage threads more efficiently.
This API does not provide any method to update the UI.
[Kotlin Coroutines](https://developer.android.com/kotlin/coroutines)
Coroutines is a solution for asynchronous programming on Android to simplify code that executes asynchronously. But it only available for Kotlin.
> So my question is, what is the correct way of communicate the result
of background thread when this finish?.

 If a thread is bounded with Activity/Fragment:
- [Activity.runOnUiThread(Runnable)](https://developer.android.com/reference/android/app/Activity#runOnUiThread(java.lang.Runnable))
 If a thread has a reference to a view, such as Adapter class.
- [View.post(Runnable)](https://developer.android.com/reference/android/view/View#post(java.lang.Runnable))- [View.postDelayed(Runnable, long)](https://developer.android.com/reference/android/view/View#post(java.lang.Runnable))
 If a thread does not bound to any UI element, then create a Handler on your own.
```
Handler mainHandler = new Handler(Looper.getMainLooper);
```

 A benefit of using Handler is you can use it to do 2 ways communication between threads. It means from a background thread you can send a task to the main thread's MessageQueue and from the main thread, you can send a task to the background's MessageQueue.
[BroadcastReceiver](https://developer.android.com/reference/android/content/BroadcastReceiver)
This API is designed to allow Android apps can send and receive broadcast messages from the Android system, other apps or components (Activity, Service, etc) inside the app, similar to [publish-subscribe](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) design partern.
Because of the [BroadcastReceiver.onReceive(Context, Intent)](https://developer.android.com/reference/android/content/BroadcastReceiver#onReceive(android.content.Context,%20android.content.Intent)) method is called within the main thread by default. So you can use it to update the UI on the main thread. For example.
Send data from a background thread.
```
// Send result from a background thread to the main thread
Intent intent = new Intent("ACTION_UPDATE_TEXT_VIEW");
intent.putExtra("text", "This is a test from a background thread");
getApplicationContext().sendBroadcast(intent);
```

Receive data from activity/fragment
```
// Create a broadcast to receive message from the background thread
private BroadcastReceiver updateTextViewReceiver = new BroadcastReceiver() {
    @Override
    public void onReceive(Context context, Intent intent) {
        String text = intent.getStringExtra("text");
        myTextView.setText(text);
    }
};

@Override
protected void onStart() {
    super.onStart();
    // Start receiving the message
    registerReceiver(updateTextViewReceiver, new IntentFilter("ACTION_UPDATE_TEXT_VIEW"));
}

@Override
protected void onStop() {
    // Stop receving the message
    unregisterReceiver(updateTextViewReceiver);
    super.onStop();
}
```

This method is usually used to communicate between Android apps or Android apps with the system. Actually, you can use it to communicate between components in Android app, such as (Activity, Fragment, Service, Thread, etc.), but it requires a lot of code.
If you want a similar solution but less code, easy to use, then you can use the following method.
[EventBus](https://github.com/greenrobot/EventBus)
EventBus is a publish/subscribe event bus for Android and Java. If you want to execute a method that runs on the main thread, just mark it with `@Subscribe(threadMode = ThreadMode.MAIN)` annotation.
```
// Step 1. Define events
public class UpdateViewEvent {
    private String text;
    
    public UpdateViewEvent(String text) {
        this.text = text;
    }

    public String getText() {
        return text;
    }
}

// Step 2. Prepare subscriber, usually inside activity/fragment
@Subscribe(threadMode = ThreadMode.MAIN)  
public void onMessageEvent(MessageEvent event) {
    myTextView.setText = event.getText();
};

// Step 3. Register subscriber
@Override
public void onStart() {
    super.onStart();
    EventBus.getDefault().register(this);
}

// Step 4. Unregister subscriber
@Override
public void onStop() {
    super.onStop();
    EventBus.getDefault().unregister(this);
}

// Step 5. Post events from a background thread
UpdateViewEvent event = new UpdateViewEvent("new name");
EventBus.getDefault().post(event);
```

This is useful when you want to update a View when the activity/fragment is visible to users (they are interacting with your app).


------------
    
    
## Answer \#1

 Vote: 6

Created at 2020-11-29 09:59:11

------------

Since the very beginning (API 1) the android way of communication between threads has been [Handler](https://developer.android.com/reference/android/os/Handler). Actually `AsyncTask` is just a wrapper around a thread pool and it uses `Handler` also to communicate to the main thread, you can check out [the source code](https://cs.android.com/android/platform/superproject/+/master:frameworks/base/core/java/android/os/AsyncTask.java) and create you own wrapper similarly.
`Handler` is the very low-level primitive and I wouldn't say using `Handler` is ugly, but it definitely requires some knowledge of multithreading programming and makes the code more verbose. As you also mentioned there are a lot of issues arises, like your UI can be gone by the time the task is completed and you have to handle this on your side. That's always the case with the low-level primitives.
As you are looking for reputable sources, here is the [official documentation](https://developer.android.com/guide/background/threading#communicating-with-the-main-thread) on exactly this problem - communicating a result from background thread to the main thread in plain java.
So unfortunately there is no other - better and officially recommended - way of doing that. Of course there are plenty of java libraries like rxJava that are build on top of the same primitives but provides higher level abstractions.


------------
    
    
## Answer \#2

 Vote: 4

Created at 2020-12-02 20:51:43

------------

I personally use AsyncTask like this:

1. Setup a broadcastReceiver in my Activity or Fragment
2. Call the asyncTask with any needed arguments in an Object[] using the Executor of your choice.
3. Once the AsyncTask has completed I Bundle with the data or result, send a LocalBroadcast containing this bundle.
4. In my Fragment or activity receive the broadcast and handle the result. I have never had any issues with this method, I do understand some people are shying away from AsyncTask, but for most purposes and all I have come across this is a simple and reliable method.




------------
    
    