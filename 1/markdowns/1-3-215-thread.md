
# Post \#59596176 [Link](https://stackoverflow.com/questions/59596176/)

## When we should use "db_index=True" in Django?

**Vote**: 20 (261/702) **Views**: 14652 (363/702) 

**Internal ID** \#1-3-215

Created at 2020-01-05 00:57:22

Tags: `django` `django-models` `django-orm`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

When we should define `db_index=True` on a model fields ?
I'm trying to optimize the application & I want to learn more about `db_index`, in which conditions we should use it ?
The documentation says that using `db_index=True` on model fields is used to speed up the lookups with slightly disadvantages with storage and memory.
Should we use `db_index=True` only on those fields that have unique values like the primary field `id` ? 
What happens if we enabled indexing for those fields which are not unique and contains repetitive data ?


----------
        
## Answer \#0

**Accepted** Vote: 20

Created at 2020-01-05 01:15:16

------------

I would say you should use `db_index=True` when you have a field that is unique for faster lookups.
For example, if you a table `customers` with many records of `users` they'll each have their own unique `user_id`. When you create an index, a pointer is created to where that data is stored within your database so that the next look up against that column will give you a much more desirable time of query than say using their `first_name` or `last_name`.
Have a look [here](https://en.wikipedia.org/wiki/Database_index) to learn more about indexing


------------
    
    
## Answer \#1

 Vote: 4

Created at 2022-07-06 06:30:36

------------

When you set `db_index=True` on some field, queries based on that field would be much faster  instead .
Under the hood, it is usually implemented using [B-Tree](https://en.wikipedia.org/wiki/B-tree).
The trade-off for these accelerated queries is increased memory usage and time for writes. So the best use case for indexing would be if you have a read-heavy database that is often quired by non-primary field.


------------
    
    
## Answer \#2

 Vote: 3

Created at 2022-04-05 07:11:12

------------

You should use `db_index=True` when you use `unique=True`, there is a specific reason to use it,
By using this method you can boost a little bit of performance,
When we fire a query in SQL, finding starts from the top to bottom
Case: 'Without db_index=True':
It will search and filter till all bottom rows even if we find the data
Case: 'With db_index=True':
When Object finds it will just stop their
It will boost a little bit of performance


------------
    
    