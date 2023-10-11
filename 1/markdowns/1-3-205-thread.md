
# Post \#57270470 [Link](https://stackoverflow.com/questions/57270470/)

## Django-filter, how to make multiple fields search? (with django-filter!)

**Vote**: 23 (239/702) **Views**: 26414 (250/702) 

**Internal ID** \#1-3-205

Created at 2019-07-30 11:21:31

Tags: `python` `django` `django-filter`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

How can I make multiple fields search with Django-filter from model like:

```
class Location(models.Model):
    loc = models.CharField(max_length=100, blank=True)
    loc_mansioned = models.CharField(max_length=100, blank=True)
    loc_country = models.CharField(max_length=100, blank=True)
    loc_modern = models.CharField(max_length=100, blank=True)
```


I need one input field on my website, that can search over all fields of Location model


----------
        
## Answer \#0

**Accepted** Vote: 47

Created at 2019-07-30 11:30:56

------------

You can probably create a custom filter and do something like this:
```
from django.db.models import Q
import django_filters


class LocationFilter(django_filters.FilterSet):
    q = django_filters.CharFilter(method='my_custom_filter', label="Search")

    class Meta:
        model = Location
        fields = ['q']

    def my_custom_filter(self, queryset, name, value):
        return queryset.filter(
            Q(loc__icontains=value) |
            Q(loc_mansioned__icontains=value) | 
            Q(loc_country__icontains=value) | 
            Q(loc_modern__icontains=value)
        )
```

This would filter by any of of those fields. You can replace the `icontains` with whatever you want.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-07-01 09:47:10

------------

This is perfect. I'm trying to do a dynamic filter, with a switch to get one more field in the search if checked. Something like this:
```
def my_custom_filter(self, queryset, name, value):
    return Reference.objects.filter(
        Q(ref_title__icontains=value))

def my_custom_filter_with_description(self, queryset, name, value):
    return Reference.objects.filter(
        Q(ref_title__icontains=value) | Q(complete_description__icontains=value))
```

But I have no clue how to link the switch to the class


------------
    
    
## Answer \#2

 Vote: 1

Created at 2019-07-30 11:32:24

------------

Due that you've defined Location as an object, to filter by multiple fields just use the `filter` method.

```
filterlocation = Location.objects.filter(loc=formloc, loc_mansioned=formlocmansioned, loc_country=formloccountry, loc_modern=formlocmodern)
```


But you need to implement a better way to use this filters, so only the result that have all conditions will be displayed.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-12-05 16:32:35

------------

Another solution, since the other one was not working directly:
```
@staticmethod
def filter_stock(qs, name, value):
    return qs.filter(
        Q(ticker__exact=value) | Q(company__iexact=value)
    )
```



------------
    
    