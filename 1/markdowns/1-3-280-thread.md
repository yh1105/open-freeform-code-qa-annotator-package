
# Post \#58558989 [Link](https://stackoverflow.com/questions/58558989/)

## What does Django's @property do?

**Vote**: 76 (82/702) **Views**: 53804 (155/702) 

**Internal ID** \#1-3-280

Created at 2019-10-25 12:58:25

Tags: `python` `django`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

What is `@property` in Django?
Here is how I understand it: `@property` is a decorator for methods in a class that gets the value in the method.
But, as I understand it, I can just call the method like normal and it will get it. So I am not sure what exactly it does.
Example from the [docs](https://docs.djangoproject.com/en/3.1/topics/db/models/#model-methods):
```
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    birth_date = models.DateField()

    def baby_boomer_status(self):
        "Returns the person's baby-boomer status."
        import datetime
        if self.birth_date < datetime.date(1945, 8, 1):
            return "Pre-boomer"
        elif self.birth_date < datetime.date(1965, 1, 1):
            return "Baby boomer"
        else:
            return "Post-boomer"

    @property
    def full_name(self):
        "Returns the person's full name."
        return '%s %s' % (self.first_name, self.last_name)
```

What is the difference of if it is there vs if it isn't?


----------
        
## Answer \#0

**Accepted** Vote: 95

Created at 2019-10-25 13:05:02

------------

As you see, the function `full_name` returns a string with the persons first and last name.
What the `@property` decorator does, is declare that it can be accessed like it's a regular property.
This means you can call `full_name` as if it were a member variable instead of a function, so like this:
`name = person.full_name`
instead of
`name = person.full_name()`
You could also define a setter method like this:
```
@full_name.setter
def full_name(self, value):
     names = value.split(' ')
     self.first_name = names[0]
     self.last_name = names[1]
```

Using this method, you can set a persons full name like this:
```
person.full_name = 'John Doe'
```

instead of
```
person.set_full_name('John Doe')
```

P.S. the setter above is just an example, as it only works for names that consist of two words separated by a whitespace. In real life, you'd use a more robust function.


------------
    
    
## Answer \#1

 Vote: 11

Created at 2019-10-25 13:07:29

------------

In some languages users are encouraged to make attributes private and create public getter and setter methods, e.g. in some made up Python-like language with `private` and `public`:
```
class Foo:
    private bar

    public get_bar(bar):
        return self.bar  # or look it up in a database
                         # or compute it on the fly from other values
                         # or anything else

    public set_bar(new_bar):
        self.bar = new_bar
```

The argument is about providing a stable interface. If you want to change the inner workings of your class, e.g. to look it up from a database or compute it, users of the class won't have to change anything; they just keep calling the getter and setter.
In Python we don't really have private attributes, and we want simple syntax. So we flip it: programmers often directly access an object's attributes. But what if you want to change the internal behaviour? We don't want to change the class' interface.
`@property` lets you change how `bar` works internally without changing the external interface. Users of the class can still access `foo.bar`, but your internal logic can be completely different:
```
class Foo:
    def __init__(self, bar):
        self.bar = bar

def main():
    f = Foo()
    print(f.bar)

# Later we can change to something like this without breaking other code
class Foo:
    def __init__(self, bar):
        self.save_bar_to_database(bar)  # Or anything else

    @property
    def bar(self):
        return self.load_bar_from_database()
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-07-03 05:23:44

------------

It is a simple way, where you can get variables in the table and provide another variable which can be used directly as it was a variable.
```
@property
def total(self):
    total_price = self.products.price_each * self.quantity_prt
    return total_price
```

such as abouve function you can get number of products and price and ue a property and make a variable of total price.


------------
    
    