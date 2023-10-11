
# Post \#64218755 [Link](https://stackoverflow.com/questions/64218755/)

## Getting Error 403 in Google Colab with Tensorboard with Firefox

**Vote**: 17 (290/702) **Views**: 7866 (466/702) 

**Internal ID** \#1-3-268

Created at 2020-10-06 03:01:51

Tags: `python` `python-3.x` `firefox` `google-colaboratory` `tensorboard`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a tfevent file already present on my Drive and I have successfully connected it to Google Colab. After searching within the issues of Tensorboard Github, I found that I had to set dom.serviceWorkers.enabled to True which I have done. But on Google Colab after performing the two steps:

1. %load_ext tensorboard
2. %tensorboard --logdir path/to/logs


I am getting a Error 403 on the second step cell:[](https://i.stack.imgur.com/TuHkS.png)
I am using Firefox version 81.0.1 (64-bit) and my default mode is Private Window, so history and cache are cleared after I close all browser window.
Can someone help me with this?


----------
        
## Answer \#0

**Accepted** Vote: 21

Created at 2020-12-09 16:50:20

------------

It seems that Tensorboard needs you to enable third party cookies to run without returning a HTTP 403 (Forbidden) error.
I had the same issue using Chrome and fixed it by just allowing everything:
[](https://i.stack.imgur.com/gQSTym.png)
You can do the same on Firefox like so:
[](https://i.stack.imgur.com/ziKDOm.png)
You could also find out which exact cookie is needed and then just allow that one.


------------
    
    
## Answer \#1

 Vote: 7

Created at 2021-05-03 21:49:57

------------

For me, it worked on Firefox deactivating the .
[](https://i.stack.imgur.com/ENl6b.gif)


------------
    
    
## Answer \#2

 Vote: 4

Created at 2021-01-28 05:24:59

------------

I had faced the same issue as I had turned on '`Block third-party cookies`' in chrome settings. Please refer to details issue threads [here](https://github.com/tensorflow/tensorboard/issues/3186). I tried whitelisting of "googleusercontent.com", but it didn't work.
,
```
1. chrome://settings/
2. Under Privacy and security >> Cookies and other site data >> Allow all cookies
```


```
1. Turn off the shields
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-04-14 10:15:07

------------


```
!rm -rf ./logs/
```

Reload the tensorboard again and check if it works.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-05-02 09:10:31

------------

In Firefox, you also have to untick the 'Delete cookies and site data when Firefox is closed' option in Settings.


------------
    
    