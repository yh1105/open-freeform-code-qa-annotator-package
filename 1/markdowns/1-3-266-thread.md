
# Post \#65431326 [Link](https://stackoverflow.com/questions/65431326/)

## Django app on Azure not getting static files

**Vote**: 7 (449/702) **Views**: 4788 (528/702) 

**Internal ID** \#1-3-266

Created at 2020-12-23 21:46:57

Tags: `django` `azure` `azure-web-app-service` `django-staticfiles`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Got  my Django project on a Azure webapp, but when I call on SSH terminal:
> Python manage.py collectstatic
It says 252 files copied but my static files are not visible on my templates and static folder in wwwroot its empty...Here's my wwwroot structure:
```
wwwroot
|---Myproject
|---manage.py
|---oryx-manifest.toml
|---hostingstart.html
|---static //With all my static files
├── myapp
│   ├── migrations
│   ├── __pycache__
│   ├── static
|   |   |---Images
|   |   |   |--myimage.png
|   |   |   |--myimage2.png
│   └── templates
```

And this is my settings.py:
```
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
STATIC_URL = '/static/'
STATICFILES_DIRS = (
   ('myapp', os.path.join(BASE_DIR, 'myapp', 'static')),
)
STATICFILES_FINDERS = (
  'django.contrib.staticfiles.finders.FileSystemFinder',
  'django.contrib.staticfiles.finders.AppDirectoriesFinder',
)
```

Any idea why or what am I doing wrong ?, does Azure collect different ?
EDIT> When I go to my website my images don´t show...Im calling them like this on template:
```
{% load static %}
<img src="{% static 'Images/myimage.png' %}" /><br>
```

EDIT 2 /////
In wwwroot creates indeed a folder with all my statics, but when I load my template they don´t show, in wen console I get this error for myimage.png and myimage2.png :
```
Failed to load resource: the server responded with a status of 404 (Not Found)
```



----------
        
## Answer \#0

**Accepted** Vote: 11

Created at 2021-01-20 20:42:45

------------

Found it !!
Just had to add this:  + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT) to url patterns like this:
```
from django.conf import settings
from django.conf.urls.static import static
urlpatterns = [
        path('myapp/', include('myapp.urls')),
        path('admin/', admin.site.urls),
    ] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

And it did the trick, hope it helps to anyone else!!


------------
    
    
## Answer \#1

 Vote: 6

Created at 2021-08-04 20:02:00

------------

Django does not serve static files in production mode.
The only time it serves static files is when you turn DEBUG = True which is in development mode and this is not recommended in a production setting.
Django recommends to serve static files via CND or any other webserver. However if your website is minimal and does not get high volume traffic you can serve your static files using Django whitenoise and here is how you do it.
The below solution works on python 3.7 and Django 3.2

```
pip install whitenoise
```


```
BASE_DIR = Path(__file__).resolve().parent.parent
```


```
STATIC_ROOT = BASE_DIR / 'staticfiles'
STATIC_URL = '/static/'
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```


```
MIDDLEWARE = [
  'django.middleware.security.SecurityMiddleware',
  'whitenoise.middleware.WhiteNoiseMiddleware',
  # ...
]
```

`{% load static %}`
```
<link rel="stylesheet" type="text/css" href="{% static 'appname/styles.css' %}">
```


```
python manage.py collectstatic
```


Some additional resources to read further:
[whitenoise](http://whitenoise.evans.io/en/stable/index.html)
[Django on Azure - beyond "hello world"](https://tonybaloney.github.io/posts/django-on-azure-beyond-hello-world.html)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-01-20 07:38:11

------------

Few points to note:
Below line will change
```
STATIC_ROOT = (os.path.join(BASE_DIR, 'Myproject/static_files/'))
```

to
```
STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
```

> 

---


Below remove the line `os.path.join(BASE_DIR, 'static/')`...
```
STATICFILES_DIRS = (
os.path.join(BASE_DIR, 'static/'), #Not Required since you already mentioned it in STATIC_URL
('myapp', os.path.join(BASE_DIR, 'myapp', 'static')),
)
```


## Reason:


- `STATIC_ROOT`
> The absolute path to the directory where collectstatic will collect
static files for deployment.If the staticfiles contrib app is enabled (default) the collectstatic management command will collect static files into this
directory. See the howto on managing static files for more details
about usage.
- `STATICFILES_DIRS`
> This setting defines the additional locations the staticfiles app will
traverse if the FileSystemFinder finder is enabled, e.g. if you use
the collectstatic or findstatic management command or use the static
file serving view.

Check this link for help on that >> [https://docs.djangoproject.com/en/3.1/howto/deployment/wsgi/modwsgi/](https://docs.djangoproject.com/en/3.1/howto/deployment/wsgi/modwsgi/)

---





------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-01-15 04:59:02

------------

I would recommend checking out Whitenoise for serving your static files with Django. I haven't had an issue serving them since integrating Whitenoise. Try swapping it out with your current static file finders setup.
[http://whitenoise.evans.io/en/stable/django.html](http://whitenoise.evans.io/en/stable/django.html)


------------
    
    