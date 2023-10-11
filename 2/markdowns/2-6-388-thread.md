
# Post \#61396213 [Link](https://stackoverflow.com/questions/61396213/)

## Entity Framework - [Keyless] Data Annotation Missing

**Vote**: 6 (473/702) **Views**: 8824 (440/702) 

**Internal ID** \#2-6-388

Created at 2020-04-23 20:16:04

Tags: `c#` `entity-framework-core` `data-annotations`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

According with the Microsoft Documentation [Here](https://learn.microsoft.com/en-us/ef/core/modeling/keyless-entity-types?tabs=data-annotations), I should have access to the Attribute for [Keyless] so I can define my Model has Keyless, so that in my DBContext I could have something like:

```
public DbSet<MyKeylessClass> KeylessModel { get; set; }
```


And use `_context.KeylessModel.FromSqlRaw(...)`, without having the need to add a PK to it.
I have the reference to `System.ComponentModel.DataAnnotations` and all the Attributes except Keyless are there, what am I missing here?


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2020-04-24 06:11:09

------------

You've probably missed the following part of the  at the very beginning of the linked documentation topic

> The `[Keyless]` Data Annotation became available in EFCore 5.0.

"became"? "in EFCore 5.0"? Come on, there is no EFCore 5.0 at this time. 

Anyway, looks like they are proactively documenting future things, so just be aware of that when something is missing or is different than the documented.


------------
    
    
## Answer \#1

 Vote: 6

Created at 2020-04-24 01:03:59

------------

Well, the official EF Core 3.0 release documentation doesn't say a word about supporting that attribute yet, instead for keyless types they instruct to use  method: 

```
ModelBuilder.Entity<MyKeylessClass>().HasNoKey()
```


You can read more [here](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-3.0/breaking-changes#query-types-are-consolidated-with-entity-types)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-02-27 20:09:46

------------

It's there now if you have ef 5.x but it's not under regular data annotations namespace, try this:
`[Microsoft.EntityFrameworkCore.Keyless]`


------------
    
    