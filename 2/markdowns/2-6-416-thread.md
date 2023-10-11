
# Post \#70565280 [Link](https://stackoverflow.com/questions/70565280/)

## Adding Entity Framework Model on Visual Studio 2022

**Vote**: 8 (428/702) **Views**: 19169 (310/702) 

**Internal ID** \#2-6-416

Created at 2022-01-03 11:41:47

Tags: `c#` `.net` `entity-framework`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I am using Visual Studio 2022 with.Net 6.0, I installed Entity Framework 6.4.4 after adding it when I create an Entity Framework model. I am getting a Popup message saying that.
> The project's target framework does not contain Entity Framework runtime assemblies. Please review the target framework information on the project's property page.
Can somebody tell us how to solve that problem?
[](https://i.stack.imgur.com/w8CnM.jpg)


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2022-01-05 09:32:34

------------

You can install .Net Framework 4.8.
.
You want to use EF 6 (not Core) and Data Model you have to use .Net Framework 4.8 or lower than that.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-03-07 12:16:33

------------

Ensure you have Target Framework as show.
[](https://i.stack.imgur.com/Ef3AC.png)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-08-14 05:56:33

------------

Try this to generate/update entire `DbContext` and models:
```
Scaffold-DbContext -Connection "Server=(local);Database=DefenderRRCart;Integrated Security=True;Trusted_Connection=True;" -Provider Microsoft.EntityFrameworkCore.SqlServer -OutputDir RRStoreContext.Models -context RRStoreContext -Project RR.DataAccess -force
```

See this post for more info.
[https://stackoverflow.com/a/41413291/4588756](https://stackoverflow.com/a/41413291/4588756)


------------
    
    