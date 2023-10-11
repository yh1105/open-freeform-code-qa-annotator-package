
# Post \#65744877 [Link](https://stackoverflow.com/questions/65744877/)

## NoReverseMatch at / Reverse for 'profile' with arguments '('',)' not found. 1 pattern(s) tried: ['profile/(?P<name>[^/]+)/$']

**Vote**: 3 (575/702) **Views**: 4346 (536/702) 

**Internal ID** \#1-3-247

Created at 2021-01-15 23:31:09

Tags: `django`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am a beginner in learning code and am working on making a simple django site where users can write comments but I keep getting this error
My urls.py
```
from django.urls import path

from . import views

urlpatterns = [
    path("", views.index, name="index"),
    path("login", views.login_view, name="login"),
    path("logout", views.logout_view, name="logout"),
    path("register", views.register, name="register"),
    path("profile/<str:name>/", views.profile, name="profile")
]
```

views.py
```
class NewPostForm(forms.Form):
    title = forms.CharField(label="Title")
    description = forms.CharField(widget=forms.Textarea)

def index(request):
    if request.method == "POST":
        form = NewPostForm(request.POST)
        if form.is_valid():
            title = form.cleaned_data['title']
            description = form.cleaned_data['description']
            author = request.user

            post = NewPost(title=title, description=description, author=author)
            post.save()
            return HttpResponseRedirect(reverse("index"))
        else:
            return render(request, "network/index.html", {
                "form": form
            })

    return render(request, "network/index.html", {
        "form": NewPostForm(),
        "posts": NewPost.objects.all()
    })
```

models.py
```
from django.contrib.auth.models import AbstractUser
from django.db import models


class User(AbstractUser):
    pass

class NewPost(models.Model):
    title = models.CharField(max_length=32)
    description = models.CharField(max_length=256)
    author = models.ForeignKey(User, on_delete=models.CASCADE, null=True)
```

and my index.html
```
{% extends "network/layout.html" %}

{% load static %}
{% load crispy_forms_tags %}

{% block body %}
    <div class="form-group">
        <form method="post" action="{% url 'index' %}">
            {% csrf_token %}
            {{form | crispy}}
            <button class="btn btn-primary"> Post </button>
        </form>
    </div>

    {% for post in posts %}
        <div class="card">
            <div class="card-body"> Title: {{post.title}} </div>
            <div class="card-body"> Description: {{post.description}}</div>
            <p> {{post.author.username}} </p>
            <div class="card-body">
                <a href="{% url 'profile' post.author.username %}">
                    @{{post.author.username}}
                </a>
            </div>
        </div>
    {% endfor %}
{% endblock %}
```

But I keep getting
NoReverseMatch at / Reverse for 'profile' with arguments '('',)' not found. 1 pattern(s) tried: ['profile/(?P[^/]+)/$']


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2021-01-16 00:42:49

------------

It looks like the error is occurring with
```
<a href="{% url 'profile' post.author.username %}">
```

Make sure that each of these `posts` that you are looping through actually has an author and that each author has a username. Django won't be able to properly construct the url if `post.author.username` is `None`.


------------
    
    
## Answer \#1

 Vote: 1

Created at 2021-01-16 00:04:35

------------

in this url : path("profile/str:name/", views.profile, name="profile")
do you have an view called profile?
and what does name in str:name refer to? username maybe? or author
you need to provide more details,


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-08-06 19:22:29

------------

This is because you have an project with author I faced the similar issue.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-01-04 21:31:27

------------

Maybe you need to check your admin panel if there is a post without author that's probably the problem


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-04-18 20:43:21

------------


It's means something missing in database thats you are working for in loops .Django won't be able to properly construct the url  if somthing is blank. First go to the Django admit panel and find whats are missing.Example:Author,User,Admin etc.


------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-12-02 11:25:12

------------

If you define a post  assigning a profile (owner) to it, you get this error. in other words, something that Django wants (in your database) !

You must go to the admin panel and check all posts that are defined, None of them should be without an owner.


------------
    
    