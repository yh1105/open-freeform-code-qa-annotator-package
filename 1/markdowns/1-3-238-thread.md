
# Post \#45724006 [Link](https://stackoverflow.com/questions/45724006/)

## Django - Reverse for '' not found. '' is not a valid view function or pattern name

**Vote**: 81 (77/702) **Views**: 262721 (26/702) 

**Internal ID** \#1-3-238

Created at 2017-08-16 22:36:26

Tags: `python` `django` `paypal` `render`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I'm working on my project for a course and I'm totally stuck right now. I'm creating a website to sell products through paypal and the paypal return/cancel pages are not rendering properly. I've followed the examples in my lessons and checked the code a hundred times but obviously there is something I'm missing.

I'm getting an error when I go to products, click buy now, login to paypal and then either cancel the purchase or buy the product and return to merchant's page.
paypal_return.html and paypal_cancel.html are 2 templates saved under templates/paypal/ 

Can anybody see what I'm missing? 

Here you can see the full code [https://github.com/IreneG5/spss_online](https://github.com/IreneG5/spss_online)

Traceback

```
Environment:


Request Method: GET
Request URL: http://127.0.0.1:8000/paypal-cancel/

Django Version: 1.11.4
Python Version: 2.7.12
Installed Applications:
['django.contrib.admin',
 'django.contrib.auth',
 'django.contrib.contenttypes',
 'django.contrib.sessions',
 'django.contrib.messages',
 'django.contrib.staticfiles',
 'django_forms_bootstrap',
 'paypal.standard.ipn',
 'home',
 'accounts',
 'paypal_store',
 'products']
Installed Middleware:
['django.middleware.security.SecurityMiddleware',
 'django.contrib.sessions.middleware.SessionMiddleware',
 'django.middleware.common.CommonMiddleware',
 'django.middleware.csrf.CsrfViewMiddleware',
 'django.contrib.auth.middleware.AuthenticationMiddleware',
 'django.contrib.messages.middleware.MessageMiddleware',
 'django.middleware.clickjacking.XFrameOptionsMiddleware']


Template error:
In template C:\Users\irene\Documents\Full-Stack-Developer\Stream3\Project\spss_online\templates\base.html, error at line 10
   Reverse for '' not found. '' is not a valid view function or pattern name.   1 : {% load staticfiles %}
   2 : <!DOCTYPE html>
   3 : <html lang="en">
   4 : <head>
   5 :     <meta charset="UTF-8">
   6 :     <title>Buy SPSS Online</title>
   7 :     <meta name="viewport" content="width=device-width, initial-scale=1.0">
   8 :     <meta name="description" content="">
   9 :     <meta name="author" content="">
   10 :     <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bootswatch/3.3.7/cerulean/bootstrap.min.css">
   11 :     <link rel="stylesheet" href="{% static 'css/style.css' %}">
   12 : 
   13 : 
   14 : </head>
   15 : <body>
   16 : <div class="container">
   17 :     <div class='container-fluid'>
   18 :         <nav id="menu" class="navbar navbar-inverse">
   19 :             <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#myNavbar">
   20 :                 <span class="icon-bar"></span>


Traceback:

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\core\handlers\exception.py" in inner
  41.             response = get_response(request)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\core\handlers\base.py" in _get_response
  187.                 response = self.process_exception_by_middleware(e, request)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\core\handlers\base.py" in _get_response
  185.                 response = wrapped_callback(request, *callback_args, **callback_kwargs)

File "C:\Users\irene\Documents\Full-Stack-Developer\Stream3\Project\spss_online\paypal_store\views.py" in paypal_cancel
  15.     return render(request, 'paypal/paypal_cancel.html', args)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\shortcuts.py" in render
  30.     content = loader.render_to_string(template_name, context, request, using=using)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\loader.py" in render_to_string
  68.     return template.render(context, request)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\backends\django.py" in render
  66.             return self.template.render(context)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\base.py" in render
  207.                     return self._render(context)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\base.py" in _render
  199.         return self.nodelist.render(context)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\base.py" in render
  990.                 bit = node.render_annotated(context)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\base.py" in render_annotated
  957.             return self.render(context)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\loader_tags.py" in render
  177.             return compiled_parent._render(context)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\base.py" in _render
  199.         return self.nodelist.render(context)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\base.py" in render
  990.                 bit = node.render_annotated(context)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\base.py" in render_annotated
  957.             return self.render(context)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\loader_tags.py" in render
  72.                 result = block.nodelist.render(context)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\base.py" in render
  990.                 bit = node.render_annotated(context)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\base.py" in render_annotated
  957.             return self.render(context)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\template\defaulttags.py" in render
  458.             url = reverse(view_name, args=args, kwargs=kwargs, current_app=current_app)

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\urls\base.py" in reverse
  91.     return force_text(iri_to_uri(resolver._reverse_with_prefix(view, prefix, *args, **kwargs)))

File "C:\Users\irene\virtualenv\spss_online_env\lib\site-packages\django\urls\resolvers.py" in _reverse_with_prefix
  497.         raise NoReverseMatch(msg)

Exception Type: NoReverseMatch at /paypal-cancel/
Exception Value: Reverse for '' not found. '' is not a valid view function or pattern name.
```



----------
        
## Answer \#0

**Accepted** Vote: 90

Created at 2017-08-18 23:22:49

------------

When you use the url tag you should use quotes for string literals, for example:

```
{% url 'products' %}
```


At the moment `product` is treated like a variable and evaluates to `''` in the error message. 


------------
    
    
## Answer \#1

 Vote: 48

Created at 2019-01-07 14:23:36

------------


1. The syntax for specifying url is {% url namespace:url_name %}. So, check if you have added the app_name in urls.py.
2. In my case, I had misspelled the url_name. The urls.py had the following content path('<int:question_id>/', views.detail, name='question_detail') whereas the index.html file had the following entry <li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>. Notice the incorrect name.




------------
    
    
## Answer \#2

 Vote: 24

Created at 2019-11-30 15:44:29

------------

I was receiving the same error when not specifying the `app` name before pattern name. 
In my case: 

`app-name` : Blog

`pattern-name` : post-delete

`reverse_lazy('Blog:post-delete')`  worked.


------------
    
    
## Answer \#3

 Vote: 9

Created at 2020-10-04 15:19:27

------------

Add store name to template like `{% url 'app_name:url_name' %}`
App_name = store
In urls.py,
`path('search', views.searched, name="searched"),`
`<form action="{% url 'store:searched' %}" method="POST">`


------------
    
    
## Answer \#4

 Vote: 7

Created at 2021-05-23 23:11:05

------------

Specify `app_name` in the  file of an app and use this app_name along with the string literal view name of the url in templates:
```
syntax --> {% url 'app_name:urlname' %}

app_name = "demo".   # right above url patterns in app
url_patterns = [
('login/', views.login, name = 'login')
]

<a href="{% url 'demo:login' %}".  # Use this in templatep
```

 use the `app_name` right above the `url_patterns` in urls.py file.


------------
    
    
## Answer \#5

 Vote: 6

Created at 2021-11-08 12:13:55

------------

You can use one of these.
If you did not (app_name)
This is the solution
in urls.py
```
urlpatterns = [
    path('', dashboard.as_view(), name='dashboard'),
]
```

in template.html
```
<a href="{% url 'dashboard' %}"></a>
```

If you did (app_name)
This is the solution
in urls.py
```
app_name = 'Blog'
urlpatterns = [
    path('', dashboard.as_view(), name='dashboard'),
]
```

in template.html
```
<a href="{% url 'Blog:dashboard' %}"></a>
```



------------
    
    
## Answer \#6

 Vote: 4

Created at 2019-05-08 02:46:11

------------

Fix `urlpatterns` in urls.py file

For example, my app name is "simulator",  

My URL pattern for `login` and `logout` looks like 

```
urlpatterns = [
    ...
    ...
    url(r'^login/$', simulator.views.login_view, name="login"),
    url(r'^logout/$', simulator.views.logout_view, name="logout"),
    ...
    ...

]
```



------------
    
    
## Answer \#7

 Vote: 3

Created at 2020-01-22 04:47:41

------------

In my case, what I did was a mistake in the url tag in the respective template. So, in my url tag I had something like 

{% url 'polls:details' question.id %}

while in the views, I had written something like:

def details(request, question_id):
    code here

So, the first thing you might wanna check is whether things are spelled as they shoould be. The next thing then you can do is as the people above have suggested.


------------
    
    
## Answer \#8

 Vote: 3

Created at 2021-03-18 05:07:57

------------

In my case, this error occurred because I forgot to add `app_name` along with `url_name`.
Below is my app `urls.py`
```
app_name = "wikis"
    urlpatterns = [
        path("", views.index, name="index"),
        path("wiki/<str:name>", views.get_page, name="get_page"),
       ]
```

Below is my function's return statement where I forgot to put `app_name` in reverse URL
```
return HttpResponseRedirect(reverse("get_page", kwargs={'name':title}))
```

Correct code would be
```
return HttpResponseRedirect(reverse("wikis:get_page", kwargs={'name':title}))
```



------------
    
    
## Answer \#9

 Vote: 1

Created at 2020-07-03 16:02:21

------------

In my case, I don't put namespace_name in the url tag ex: `{% url 'url_name or pattern name' %}.`
you have to specify the namespace_name like: `{% url 'namespace_name:url_name or pattern name' %}.`
 In project  `path('', include('blog.urls',namespace='blog')),` and in app's  you have to specify the app_name. like `app_name = 'blog'`. namespace_name is the app_name.


------------
    
    
## Answer \#10

 Vote: 1

Created at 2021-04-19 20:07:44

------------

*Always make sure when you use HttpResponseRedirect and reverse, the name you specify inside reverse is the same name you gave to your path inside urls.py
Thats the problem i had and i found out through trial and error*


------------
    
    
## Answer \#11

 Vote: 1

Created at 2021-07-31 04:12:44

------------

You can also get this error if you forget to add the app url in the project url


```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app.urls')),
]
```

and you have a 
so if you have html code in 
and you want to add in  an a tag with the url to an html code in 
You would have to include the store/urls.py in 

```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app.urls')),
    path('', include('store.urls')),
]
```



------------
    
    
## Answer \#12

 Vote: 1

Created at 2021-11-20 07:22:53

------------

I too faced the same issue while testing, for me it was caused when url was empty in html
```
<td><a href="{% url '' test.id %}"><i class="fa fa-times" aria-hidden="true"></i></a></td>
```

So after I changed it by putting the url name:
```
<td><a href="{% url 'task_delete' test.id %}"><i class="fa fa-times" aria-hidden="true"></i></a></td>
```



------------
    
    
## Answer \#13

 Vote: 0

Created at 2019-12-27 05:35:04

------------

If you dont define name in the path field, usually the error will come.
e.g.: `path('crud/',ABC.as_view(),name="crud")`


------------
    
    
## Answer \#14

 Vote: 0

Created at 2020-03-16 16:51:59

------------

Give the same name in urls.py

```
path('detail/<int:id>', views.detail, name="detail"),
```



------------
    
    
## Answer \#15

 Vote: 0

Created at 2020-12-31 18:56:32

------------

In my case, this error occurred due to a `mismatched` url name. e.g,
```
<form action="{% url 'test-view' %}" method="POST">
```

urls.py
```
path("test/", views.test, name='test-view'),
```



------------
    
    
## Answer \#16

 Vote: 0

Created at 2021-04-19 20:10:51

------------

*Always make sure when you use HttpResponseRedirect and reverse, the name you specify inside reverse is the same name you gave to your path inside urls.py
'return HttpResponseRedirect(reverse("index"))
path("index/",views.index,name="index")'
Thats the problem i had and i found out through trial and error*


------------
    
    
## Answer \#17

 Vote: 0

Created at 2021-07-04 05:30:00

------------

I have faced the same problem. The mistake was I declared same name for multiple  urlpatterns.
```
path('file', views.sender, name='sender'),
```

Here the name should be different and unique for different url.


------------
    
    
## Answer \#18

 Vote: 0

Created at 2021-10-18 11:50:35

------------

Let's say in your template home.html you are having a form that does a POST request, like:
```
<form id="form_id" action = "{% url 'home' %}" method = "post" > .. </form>
```

In your urls.py you have
```
url(r'^$', views.home_view, name='home')
```

in your view, you can check the passed data from template like this.
```
def home_view(request): print(request.POST)
```

it is important in urls.py to declare a  and on your action part on the form to include this url 


------------
    
    
## Answer \#19

 Vote: 0

Created at 2022-03-27 07:49:25

------------

I've been reading this for past two days to compare mine to see why it didn't work. it turns out a space between ":" matters:
mine had a  between ":" and "index":
```
<a href="{% url 'namespace: index' %}">Home</a>
```

thought this worth mentioning in case someone like me scratching their heads in the future:
No space around the colon, or anywhere before or after "namespace" and "index"


------------
    
    
## Answer \#20

 Vote: 0

Created at 2022-06-01 18:22:36

------------

The simple solution is
```
path('detail/', views.detail, name="This_is_the_solution"),
```

you have to give the name value to the url,
```
<a href="{% url 'This_is_the_solution' %}"></a>
```



------------
    
    
## Answer \#21

 Vote: 0

Created at 2022-07-08 17:10:42

------------

In Some cases:
You should not forgot to load the home page or base page by using
eg:
`{%extends "app_name/web_page.html"%>`


------------
    
    
## Answer \#22

 Vote: -1

Created at 2019-05-05 11:10:37

------------

The common error that I have find is when you forget to define 
your url in `yourapp/urls.py`

we don't want any suggetion!!
solution plz.. 


------------
    
    
## Answer \#23

 Vote: -1

Created at 2019-09-11 08:29:17

------------

On line 10 there's a space between `s` and `t`. It should be one word: `stylesheet`.


------------
    
    
## Answer \#24

 Vote: -1

Created at 2021-04-15 09:46:07

------------

appname=demo
url=/student

Make Sure you should not have space before or after : (colon)


------------
    
    