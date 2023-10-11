
# Post \#58894875 [Link](https://stackoverflow.com/questions/58894875/)

## How to delete a database in pgadmin

**Vote**: 13 (343/702) **Views**: 25767 (257/702) 

**Internal ID** \#2-5-373

Created at 2019-11-16 20:26:03

Tags: `java` `sql` `database`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

When i try to create other database with the name "eCommerce" in pgadmin 4 this message appears 



I try to delete the others databases but is not working and appears



What should i do?


----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2019-11-16 22:26:14

------------

You simply need to terminate the connections before deleting the database :) 

```
//Terminate all Connections on HostDB
     SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE datname = 'Database_you_want_to_delete';
```


After that deleting shall work without an further problems. 

Edit: For further information please take a look at this thread: [Kill a postgresql session/connection](https://stackoverflow.com/questions/5108876/kill-a-postgresql-session-connection)


------------
    
    
## Answer \#1

 Vote: 34

Created at 2020-11-18 08:10:42

------------

It's impossible to drop a database in pgAdmin4 by right-clicking on it and selecting Delete/Drop from the context menu. Because as soon as you right-click on it pgAdmin opens a connection to that database.
But there is another way in pgAdmin4:

1. Close connections to the databases you would like to delete by right-clicking on it and selecting "Disconnect database..."
2. Left-click on "Databases" (One up in the hierarchy: The folder that contains all your databases)
3. Select tab "Properties" on the right hand side
4. There you can select all your databases you would like to delete and click on the trashcan icon:


[](https://i.stack.imgur.com/s64F4.png)


------------
    
    
## Answer \#2

 Vote: 3

Created at 2021-02-10 14:53:47

------------

, I think, is 
> simply closing any opened tabs related to that database in pgAdmin solves it.
In your case, 


------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-03-04 10:10:03

------------

You can not drop the currently open database. Run query `DROP DATABASE db_name;` in `Query Tool` from another database.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-05-25 21:57:27

------------

Using pgAdmin, I followed @softi's solution but tweaked it a little bit and it worked
First, Close connections to the database(s) you would like to delete by right-clicking on it and selecting "Disconnect database..."
Left-click on "Databases" (One up in the hierarchy: The folder that contains all your databases)
Select "Dashboard" tab on the right hand side and terminate the session(s) of the database(s) you want to delete/drop
Then go to the "Properties" tab (next to the Dashboard tab), then select the database(s) you would like to delete and click on the trashcan icon above the list


------------
    
    