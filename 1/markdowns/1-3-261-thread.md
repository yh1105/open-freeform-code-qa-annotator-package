
# Post \#68680322 [Link](https://stackoverflow.com/questions/68680322/)

## Pytube: urllib.error.HTTPError: HTTP Error 410: Gone

**Vote**: 15 (318/702) **Views**: 17996 (321/702) 

**Internal ID** \#1-3-261

Created at 2021-08-06 10:46:21

Tags: `python` `http` `http-error` `pytube`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I've been getting this error on several programs for now.
I've tried upgrading pytube, reinstalling it, tried some fixes, changed URLs and code, but nothing seems to work.
```
from pytube import YouTube

#ask for the link from user
link = input("Enter the link of YouTube video you want to download:  ")
yt = YouTube(link)

#Showing details
print("Title: ",yt.title)
print("Number of views: ",yt.views)
print("Length of video: ",yt.length)
print("Rating of video: ",yt.rating)
#Getting the highest resolution possible
ys = yt.streams.get_highest_resolution()

#Starting download
print("Downloading...")
ys.download()
print("Download completed!!")
```

and this is the error code:
```
File "C:\Users\Madjid\PycharmProjects\pythonProject\app2.py", line 6, in <module>
    yt = YouTube(link)
  File "C:\Users\Madjid\PycharmProjects\pythonProject\venv\lib\site-packages\pytube\__main__.py", line 91, in __init__
    self.prefetch()
  File "C:\Users\Madjid\PycharmProjects\pythonProject\venv\lib\site-packages\pytube\__main__.py", line 181, in prefetch
    self.vid_info_raw = request.get(self.vid_info_url)
  File "C:\Users\Madjid\PycharmProjects\pythonProject\venv\lib\site-packages\pytube\request.py", line 36, in get
    return _execute_request(url).read().decode("utf-8")
  File "C:\Users\Madjid\PycharmProjects\pythonProject\venv\lib\site-packages\pytube\request.py", line 24, in _execute_request
    return urlopen(request)  # nosec
  File "E:\Python\lib\urllib\request.py", line 214, in urlopen
    return opener.open(url, data, timeout)
  File "E:\Python\lib\urllib\request.py", line 523, in open
    response = meth(req, response)
  File "E:\Python\lib\urllib\request.py", line 632, in http_response
    response = self.parent.error(
  File "E:\Python\lib\urllib\request.py", line 555, in error
    result = self._call_chain(*args)
  File "E:\Python\lib\urllib\request.py", line 494, in _call_chain
    result = func(*args)
  File "E:\Python\lib\urllib\request.py", line 747, in http_error_302
    return self.parent.open(new, timeout=req.timeout)
  File "E:\Python\lib\urllib\request.py", line 523, in open
    response = meth(req, response)
  File "E:\Python\lib\urllib\request.py", line 632, in http_response
    response = self.parent.error(
  File "E:\Python\lib\urllib\request.py", line 561, in error
    return self._call_chain(*args)
  File "E:\Python\lib\urllib\request.py", line 494, in _call_chain
    result = func(*args)
  File "E:\Python\lib\urllib\request.py", line 641, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 410: Gone
```



----------
        
## Answer \#0

**Accepted** Vote: 23

Created at 2021-08-19 12:54:39

------------

Try to upgrade, there is a fix in version 11.0.0:
```
python -m pip install --upgrade pytube
```



------------
    
    
## Answer \#1

 Vote: 7

Created at 2021-08-06 13:23:45

------------

If you haven't already, install Git on your PC:
[https://git-scm.com/download/win](https://git-scm.com/download/win)
Then open the command window as admin and install this patch:
```
python -m pip install git+https://github.com/Zeecka/pytube@fix_1060
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2022-09-04 18:16:19

------------

I build mine from start to bottom, and ran into to same issue here are the code and steps.
Code:
```
from pytube import YouTube
from sys import argv



link = argv[1]
yt = YouTube(link)



yd = yt.streams.get_highest_resolution()

yd.download(r'C:/Users/adam/OneDrive/Desktop/video')
```

got error:
```
urllib.error.HTTPError: HTTP Error 410: Gone
```

STEPS:

1. python -m pip install --upgrade pytube
2. python3 -m pip install git+https://github.com/pytube/pytube
3. Ran as ADMIN.


worked fine!


------------
    
    
## Answer \#3

 Vote: 3

Created at 2022-11-10 16:28:19

------------

I started a personal project a while ago that downloads YT videos (MP4's), and I ran into the same issue, among others. All of the answers were helpful, but it took like 2-3 of them  to resolve my issue:
First, I had to download Git For Windows: ([https://git-scm.com/download/win](https://git-scm.com/download/win))
Then, I  my already installed PyTube package with the below command. This installs PyTube 10.9.3:
```
`python -m pip install git+https://github.com/Zeecka/pytube@fix_1060`
```

Finally, I re-installed the latest version of PyTube (this command installs the ):
```
`pip install --upgrade pytube`
```

Not sure exactly, what causes PyTube to flip out when you first install, but downgrading, then upgrading worked for me.
For any wondering, final-product code is as seen below. Videos downloaded at 720p, with their title as the name of the saved file.
```
from pytube import YouTube 
  
#where to save 
SAVE_PATH = "C:/YOUR/DESIRED/FILE/PATH/" 
  
#link of the video to be downloaded 
link = input("Enter URL >> ")
try:
    yt = YouTube(link)
    mp4_files = yt.streams.filter(file_extension="mp4")
    mp4_720p_files = mp4_files.get_by_resolution("720p")
    mp4_720p_files.download(SAVE_PATH) 
except Exception as e: 
    print("ERROR: ", e)
```

Sources:
HTTP Error 410 'Gone' - [https://www.youtube.com/watch?v=rrRuBrUrnCw](https://www.youtube.com/watch?v=rrRuBrUrnCw)
Git for Windows - [https://git-scm.com/download/win](https://git-scm.com/download/win)


------------
    
    
## Answer \#4

 Vote: 2

Created at 2022-09-28 05:16:56

------------

This is a bug of the pytube version. You might install an older version of it. Upgrade pytube to 11+ will resolve the issue.
- Open the terminal and execute the following command to upgrade the version```
pip install --upgrade pytube
```

[](https://i.stack.imgur.com/h7LAQ.png)
[https://github.com/pytube/pytube/issues/1243](https://github.com/pytube/pytube/issues/1243)


------------
    
    
## Answer \#5

 Vote: 0

Created at 2021-12-22 15:34:47

------------

You can get the latest version from the official GitHub repository
[https://github.com/pytube/pytube](https://github.com/pytube/pytube)
```
python3 -m pip install git+https://github.com/pytube/pytube
```

[Updated to pytube 11.0.2](https://i.stack.imgur.com/7kBO1.png)


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-07-16 17:14:41

------------

I am answering this because the solutions I found on the internet didn't work for me (the ones above too).
I even tried to install an other fix done ssuwani on GitHub using:
```
pip install git+https://github.com/ssuwani/pytube
```

(I use python 3.8.10 and Ubuntu 20.04.4 LTS)
I just used `sudo su` to enter admin mode, ran my program and it worked. To be honest, I don't know why, but if it can help that's nice :)
Warning, don't enter any commands when you're in admin mode because you could mess up your system


------------
    
    