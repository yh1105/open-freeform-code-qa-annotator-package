
# Post \#64155277 [Link](https://stackoverflow.com/questions/64155277/)

## Errno - 13 Permission denied: '/media/ - Django

**Vote**: 3 (575/702) **Views**: 7824 (468/702) 

**Internal ID** \#1-3-279

Created at 2020-10-01 12:20:02

Tags: `python` `django` `ubuntu` `django-models` `permission-denied`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am using Django 3.1 in ubuntu,
I got an error while uploading media files
```
PermissionError at /admin/main/artist/1/change/
[Errno 13] Permission denied: '/media/artists'

Exception Type: PermissionError
Exception Value:    
[Errno 13] Permission denied: '/media/artists'
Exception Location: /usr/lib/python3.8/os.py, line 223, in makedirs
Python Executable:  /home/rahul/.local/share/virtualenvs/music-69qL54Ia/bin/python
```

This code works in windows, but not in ubuntu
Settings.py
```
STATIC_URL = '/static/'
STATICFILES_DIRS = [BASE_DIR / 'static']

MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / '/media/'
```

Models.py
```
class Artist(models.Model):
    image = models.ImageField(upload_to='artists/%Y/%m/%d/', default='demo-artist.jpg', null=True, blank=True)
```

I tried this but didn't work
```
https://stackoverflow.com/questions/21797372/django-errno-13-permission-denied-var-www-media-animals-user-uploads
```



----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2020-10-30 15:11:20

------------

I got the same error and debugged it using the `shell`
In your `settings.py` file:
Change:
```
MEDIA_ROOT = BASE_DIR / '/media/'
# here, MEDIA_ROOT = '/media/'
```

To:
```
MEDIA_ROOT = BASE_DIR / 'media/'
# here, MEDIA_ROOT = 'path-to-project/media/'
```

I think this happens because you are trying to join `your project level dir` to the `/media/` directory that exists in linux for mounting media. And would cause permission denied because `root` has the write permissions, you probably aren't running everything with `sudo`. So, instead you can remove the first `\` to make the directory relative.


------------
    
    
## Answer \#1

 Vote: 3

Created at 2020-10-01 12:40:55

------------

```
mkdir --mode=777 -pv /home/rahul/.local/share/virtualenvs/music-69qL54Ia/{admin/main/artist/1/change,media/artists}

chmod -R 777 /home/rahul/.local/share/virtualenvs/music-69qL54Ia
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-10-01 13:18:02

------------

Maybe you forgot to add the MEDIA_ROOT to your urls.py.
For more info checkout the [docs](https://docs.djangoproject.com/en/3.1/howto/static-files/#serving-files-uploaded-by-a-user-during-development)
```
urlpatterns = [
    # ... the rest of your URLconf goes here ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

Note: This is not suitable for production use. if thats the case you can checkout the [docs](https://docs.djangoproject.com/en/3.1/howto/static-files/deployment/)


------------
    
    