
# Post \#60739653 [Link](https://stackoverflow.com/questions/60739653/)

## Gdown is giving Permission error for particular file,although it is opening up fine manually

**Vote**: 23 (239/702) **Views**: 22444 (279/702) 

**Internal ID** \#1-3-189

Created at 2020-03-18 12:31:53

Tags: `python` `google-drive-api`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am not able to download file using gdown package.It is giving permission error.
But when i am opening it manually.It is giving no such error and opening up fine.
Here is the code i am using and link

```
import gdown
url='https://drive.google.com/uc?id=0B1lRQVLFjBRNR3Jqam1menVtZnc'
output='letter.pdf'
gdown.download(url, output, quiet=False)
```


Error is 
Permission denied: [https://drive.google.com/uc?id=0B1lRQVLFjBRNR3Jqam1menVtZnc](https://drive.google.com/uc?id=0B1lRQVLFjBRNR3Jqam1menVtZnc)
Maybe you need to change permission over 'Anyone with the link'?


----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2020-12-17 19:24:00

------------

If you're working with big files (in my case was a >1gb file), you can solve by copying the url from 'Download anyway' button in Google Drive.


------------
    
    
## Answer \#1

 Vote: 59

Created at 2022-02-18 16:20:26

------------

In my case, I ran the following command and try using `gdown`, and problem was solved:
```
pip install --upgrade --no-cache-dir gdown
```

If you are using google-colab, try:
```
!pip install --upgrade --no-cache-dir gdown
```

then:
`!gdown --id [id of your file]`


------------
    
    
## Answer \#2

 Vote: 10

Created at 2022-11-24 02:29:48

------------

instead of
`gdown <drive-id>`
type
`gdown "<drive-id>&confirm=t"`
for large files.


------------
    
    
## Answer \#3

 Vote: 2

Created at 2021-07-23 07:45:29

------------

Create your downloadable zip folder and make it  and change " to .
Finally use:
```
!gdown --id 'id of the file'
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2022-11-25 19:43:15

------------

`pip install --upgrade --no-cache-dir gdown`
didn't work for me.
You can try pre-released version of this :
`pip install -U --no-cache-dir gdown --pre`
This worked for me.


------------
    
    