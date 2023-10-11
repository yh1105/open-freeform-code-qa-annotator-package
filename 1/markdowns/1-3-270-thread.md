
# Post \#64807163 [Link](https://stackoverflow.com/questions/64807163/)

## ImportError: cannot import name '...' from partially initialized module '...' (most likely due to a circular import)

**Vote**: 96 (63/702) **Views**: 243177 (27/702) 

**Internal ID** \#1-3-270

Created at 2020-11-12 15:48:06

Tags: `python` `django` `importerror` `python-module` `circular-dependency`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I'm upgrading an application from Django 1.11.25 (Python 2.6) to Django 3.1.3 (Python 3.8.5) and, when I run `manage.py makemigrations`, I receive this messasge:
```
File "/home/eduardo/projdevs/upgrade-intra/corporate/models/section.py", line 9, in <module>
    from authentication.models import get_sentinel**

ImportError: cannot import name 'get_sentinel' from partially initialized module 'authentication.models' (most likely due to a circular import) (/home/eduardo/projdevs/upgrade-intra/authentication/models.py)**
```

My models are:

```
from django.conf import settings
from django.contrib.auth.models import AbstractUser, UserManager
from django.db import models
from django.db.models.signals import post_save
from django.utils import timezone

from corporate.constants import GROUP_SUPPORT
from corporate.models import Phone, Room, Section
from library.exceptions import ErrorMessage
from library.model import update_through_dict
from .constants import INTERNAL_USER, EXTERNAL_USER, SENTINEL_USERNAME, SPECIAL_USER, USER_TYPES_DICT


class UserProfile(models.Model):
    user = models.OneToOneField(
        'User',
        on_delete=models.CASCADE,
        unique=True,
        db_index=True
    )
    ...
    phone = models.ForeignKey('corporate.Phone', on_delete=models.SET_NULL, ...)
    room = models.ForeignKey('corporate.Room', on_delete=models.SET_NULL, ...)
    section = models.ForeignKey('corporate.Section', on_delete=models.SET_NULL, ...)
    objects = models.Manager()
    ...

class CustomUserManager(UserManager):

    def __init__(self, type=None):
        super(CustomUserManager, self).__init__()
        self.type = type

    def get_queryset(self):
        qs = super(CustomUserManager, self).get_queryset()
        if self.type:
            qs = qs.filter(type=self.type).order_by('first_name', 'last_name')
        return qs

    def get_this_types(self, types):
        qs = super(CustomUserManager, self).get_queryset()
        qs = qs.filter(type__in=types).order_by('first_name', 'last_name')
        return qs

    def get_all_excluding(self, types):
        qs = super(CustomUserManager, self).get_queryset()
        qs = qs.filter(~models.Q(type__in=types)).order_by('first_name', 'last_name')
        return qs

class User(AbstractUser):
    type = models.PositiveIntegerField('...', default=SPECIAL_USER)
    username = models.CharField('...', max_length=256, unique=True)
    first_name = models.CharField('...', max_length=40, blank=True)
    last_name = models.CharField('...', max_length=80, blank=True)
    date_joined = models.DateTimeField('...', default=timezone.now)
    previous_login = models.DateTimeField('...', default=timezone.now)

    objects = CustomUserManager()
    ...
    def get_profile(self):
        if self.type == INTERNAL_USER:
            ...
        return None

    def get_or_create_profile(self):
        profile = self.get_profile()
        if not profile and self.type == INTERNAL_USER:
            ...
        return profile

    def update(self, changes):
        ...

class ExternalUserProxy(User):
    objects = CustomUserManager(type=EXTERNAL_USER)

    class Meta:
        proxy = True
        verbose_name = '...'
        verbose_name_plural = '...'

class InternalUserProxy(User):
    objects = CustomUserManager(type=INTERNAL_USER)

    class Meta:
        proxy = True
        verbose_name = '...'
        verbose_name_plural = '...'

def create_profile(sender, instance, created, **kwargs):
    if created and instance.type == INTERNAL_USER:
        try:
            profile = UserProfile()
            profile.user = instance
            profile.save()
        except:
            pass

post_save.connect(create_profile, sender=User)

def get_sentinel():
    try:
        sentinel = User.objects.get(username__exact=SENTINEL_USERNAME)
    except User.DoesNotExist:
        settings.LOGGER.error("...")
        from django.contrib.auth.models import Group
        sentinel = User()
        sentinel.username = SENTINEL_USERNAME
        sentinel.first_name = "..."
        sentinel.last_name = "..."
        sentinel.set_unusable_password()
        sentinel.save()
        technical = Group.objects.get(name=GROUP_SUPPORT)
        sentinel = User.objects.get(username__exact=SENTINEL_USERNAME)
        sentinel.groups.add(technical)
        sentinel.save()
    return sentinel
```

`__init__.py`
```
...
from .section import Section
...
```


```
from django.conf import settings
from authentication.models import get_sentinel
from .room import Room

class Section(models.Model):
    ...
    boss = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.SET(get_sentinel), ...)
    surrogate = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.SET(get_sentinel), ...)
    room = models.ForeignKey(Room, on_delete=models.SET_NULL, ...)
    is_subordinate_to = models.ForeignKey('self', on_delete=models.SET_NULL, ...)
    ...
```

What am I doing wrong?


----------
        
## Answer \#0

**Accepted** Vote: 40

Created at 2020-11-12 18:34:34

------------

You have a circular import.
`authentication/models` imports `corporate/models`, which imports `corporate/models/section`, which imports `authentication/models`.
You can't do that.


------------
    
    
## Answer \#1

 Vote: 207

Created at 2021-02-04 22:54:55

------------

For future readers, this can also happen if you name a python file the same name as a dependency your project uses.
For example:
I cannot have a file named retrying.py that is using the retrying package.
Assuming I had the retrying package in my project, I could not have a file called retrying.py with the below contents:
```
from retrying import retry
print("HI")
```

A similar error with the message "most likely due to a circular import" would occur.
The same contents would work fine if I renamed the file to "retrying_example1.py"


------------
    
    
## Answer \#2

 Vote: 6

Created at 2021-09-12 14:55:47

------------

When importing code from other files, it helps if you spell out  subpackage where the thing you want to import comes from. Let's say you have the following file structure:
```
mypackage/
  subpackage/
    __init__.py
    helper.py
  main/
    work.py
```

If:
- `__init__.py``helper.py`- `work.py`- `subpackage/helper.py`
Then rather than doing:
```
from ..subpackage import thing_i_need
```

You should instead do:
```
from ..subpackage.helper import thing_i_need
```

For reasonable code, this should help you avoid some of the circular dependency problems, as now you're no longer relying on `__init__.py` to fully finish.


------------
    
    
## Answer \#3

 Vote: 5

Created at 2022-04-19 19:12:15

------------

I received this error when I tried to do a relative import.  I had two models files:
`utils.models`:
```
class BaseModel(models.Model):
    ...
```

`main.models`:
```
from .models import BaseModel
...
```

The problem was fixed when, in `main.models`, I changed it to:
```
from utils.models import BaseModel
```



------------
    
    
## Answer \#4

 Vote: 3

Created at 2021-12-09 10:33:51

------------

In my case the problem was that I defined the function in the x.py file and in the x.py file I import models from the modals.py file and in the modals.py file I tried to import this function I was trying to set the default value after querying the tables with this function


------------
    
    
## Answer \#5

 Vote: 2

Created at 2022-11-09 17:56:04

------------

I got the same error below:
> AttributeError: partially initialized module 'math' has no attribute
'pi' (most likely due to a circular import)
Because I created `math.py`, imported `math` in it and use `math.pi` in it as shown below:
```
# "math.py"

import math

print(math.pi)
```

So, I changed `math.py` to `my_math.py`:
```
# "my_math.py"

import math

print(math.pi)
```

Then, the error was solved:
```
3.141592653589793
```



------------
    
    
## Answer \#6

 Vote: 1

Created at 2023-01-09 08:35:59

------------

I was getting the same error.
I was doing this:( i.e. importing my `models` before blueprint object creation and model uses the `db` object defined in the `app.py` file. And this main_blueprint is getting registered in app.py.)
```
from ..models.user_model import User as user_model
main = Blueprint('main', __name__)
```

I fixed it by importing my model after blueprint object creation.
```
main = Blueprint('main', __name__)
# always import models after blueprint object creation.
from ..models.user_model import User as user_model
```



------------
    
    
## Answer \#7

 Vote: 0

Created at 2022-11-04 10:48:19

------------

One way to resolve circular imports is by splitting a file in the circle into multiple files in such a way that the files are no longer importing each other. In this specific case, I  it would be resolved if `get_sentinel` was moved to a separate file.

```
def get_sentinel():
    ...
```


```
from django.conf import settings
from authentication.models_utils import get_sentinel
from .room import Room

class Section(models.Model):
...
```



------------
    
    
## Answer \#8

 Vote: 0

Created at 2023-01-03 07:42:27

------------

The order of imports in `__init__.py` matters:

```
from .a import A
from .b import B
```


```
from models.b import B

...
```

This will give `ImportError: cannot import name 'B' from partially initialized module 'models' (most likely due to a circular import) (/models/__init__.py)`
To resolve, the import of `B` should come before the import of `A` in `__init__.py`


------------
    
    
## Answer \#9

 Vote: 0

Created at 2023-02-19 20:31:04

------------

For me I got this error because I created two controllers `clients.py` and `users.py` that import from each other causing 
```
from app.controllers.users import get_user_manager, UserManager
ImportError: cannot import name 'get_user_manager' from partially initialized module 'app.controllers.users' (most likely due to a circular import)
```

Here is the senarion for more clarification
- Controller `client.py` import `get_user_manager`, `UserManager` from `users.py` to be used in one of it is functions `registerClient`- Implemented `sendForgetPasswordMailClient` in `client.py` that do not use the import above- In `users.py` I called `sendForgetPasswordMailClient` from `clients.py` that already import from `users.py` causing 
client.py
```
...
from app.controllers.users import get_user_manager, UserManager


async def registerClient(client_password: ClientPasswordCreate, client: UserCreate, user_db: UserManager = Depends(get_user_manager), db: Session = Depends(get_async_session)):
   ...

async def sendForgetPasswordMailClient(client_mail, client_name, link):
   ...
```

users.py
```
...
from app.controllers.clients import sendForgetPasswordMailClient

class UserManager(BaseUserManager[UserCreate, UserDB]):
    ...
    async def on_after_forgot_password(
        self, user: UserDB, token: str, request: Optional[Request] = None
    ):
        link = f'{APP_LINK}?token={token}'
        sendForgetPasswordMailClient(user.mail, user.firstName, link)

async def get_user_manager(user_db: SQLAlchemyUserDatabase = Depends(get_user_db)):
    yield UserManager(user_db)
```

The solution was simply to move `sendForgetPasswordMailClient` that does not contain any import from `users.py` to another file so I can call it freely from `users.py` without any issue.


------------
    
    
## Answer \#10

 Vote: -1

Created at 2023-02-01 15:33:13

------------

I had the same error. Here is my before and after solution. The circular import error makes sense.
```
from resources.file import blp as Home

app = Flask(__name__)
csrf = CSRFProtect(app)
```

Solution:
```
app = Flask(__name__)
csrf = CSRFProtect(app)

from resources.file import blp as Home
```



------------
    
    