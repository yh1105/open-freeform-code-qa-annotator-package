
# Post \#68779189 [Link](https://stackoverflow.com/questions/68779189/)

## Why do I get this DBeaver error when importing data from a CSV file?

**Vote**: 5 (499/702) **Views**: 19485 (308/702) 

**Internal ID** \#1-3-233

Created at 2021-08-13 23:17:45

Tags: `python` `postgresql` `csv` `dbeaver`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `csv` `python` `sql`)

----------

**Notepad**


----------

I'm a student, and I'm working on a project. The premise is that this program I'm working on takes as input the days (M-F) on which a student is enrolled, the number of hours per day they are enrolled, and which course they are enrolled in. Then, it queries a PostgreSQL database for the amount of progress hours (the "par value" of how long an assignment should take) for each module, and runs an algorithm, outputting the approximate due dates that students should shoot for in order to be done with their course by the deadline (which is set for the entire course rather than for individual assignments).
Now, I've had the database working in the past, but for the next deliverable I am overhauling the program to add support for students selecting their program, which will then populate an option menu with the appropriate courses. To do this, I need to add more test data. In the course of importing additional CSV files into a schema, an error arose:
> Can't load entity attributesReason:
Can't find target attribute [Sequence]
In the CSV file, "Sequence” is the name of one of the columns.
What does this error mean? And, how might I go about resolving it? I have looked over the CSV and I can’t find anything obviously wrong; the heading is there, along with all the associated data. I could in theory create a connection in DBeaver directly with the CSV files rather than importing their data into a database, and I’ve tested doing it that way and it does work as expected, but the Python module I’m using to query the database (psycopg2) doesn’t seem to be able to find it if I do it like that as opposed to using a traditional database (which is how I did it prior to altering my database).
Edit: In response to Adrian's comment, a sample of the CSV data is as follows:
```
PK,Active,Sequence,Name,Number,Hours,Start Date,Stop Date,Modified When,Modified By
183328,TRUE,1,ASP.NET Core Fundamentals,1,2.00,07-01-2020,06-30-2299,06-12-2020 09:09:27 AM,Laraine.Moellendorf
183329,TRUE,2,F-Q1,2,3.00,07-01-2020,06-30-2299,06-12-2020 09:09:27 AM,Laraine.Moellendorf
183330,TRUE,3,Assignment - Clock-in Station,3,5.00,07-01-2020,06-30-2299,06-12-2020 09:09:27 AM,Laraine.Moellendorf
183331,TRUE,4,ASP.NET MVC Fundamentals,4,2.00,07-01-2020,06-30-2299,06-12-2020 09:09:27 AM,Laraine.Moellendorf
183332,TRUE,5,MVC-Q1,5,3.00,07-01-2020,06-30-2299,06-12-2020 09:09:27 AM,Laraine.Moellendorf
183333,TRUE,6,Wishlist Application,6,5.00,07-01-2020,06-30-2299,06-12-2020 09:09:27 AM,Laraine.Moellendorf
183334,TRUE,7,ASP.NET Web APIs,7,2.00,07-01-2020,06-30-2299,06-12-2020 09:09:27 AM,Laraine.Moellendorf
183335,TRUE,8,API-Q1,8,3.00,07-01-2020,06-30-2299,06-12-2020 09:09:27 AM,Laraine.Moellendorf
183336,TRUE,9,Starchart API,9,5.00,07-01-2020,06-30-2299,06-12-2020 09:09:27 AM,Laraine.Moellendorf
183337,TRUE,10,Dependency Injection,10,2.00,07-01-2020,06-30-2299,06-12-2020 09:09:27 AM,Laraine.Moellendorf
```

I'm not importing this data into an existing table; rather, DBeaver has a feature where one can import a CSV without having an existing table, and a table with matching field headings and data types will be generated to place the data into. So, I do not have a schema to provide.
The DDL that is producing the error is below:
```
CREATE TABLE "Software Development".newtable (
    pk integer NULL,
    active boolean NULL,
    "Sequence" integer NULL,
    "name" varchar(32) NULL,
    "Number" integer NULL,
    hours real NULL,
    "Start Date" varchar(10) NULL,
    "Stop Date" varchar(10) NULL,
    "Modified When" varchar(22) NULL,
    "Modified By" varchar(19) NULL
);
```

In answer to Adrian's last question, DBeaver can link directly to a CSV file rather than importing its data into a table within a database. That was what I meant by it not being a "traditional database".


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2021-09-01 14:43:44

------------

I've upgraded to DDeaver version  and everything is working now. I was getting the error trying to export a query to a SQLite table.
So the solution is to upgrade DBeaver.


------------
    
    
## Answer \#1

 Vote: 7

Created at 2021-08-17 20:06:29

------------

This appears to be an issue for dBeaver 21.1.4 ([https://github.com/dbeaver/dbeaver/issues/13542](https://github.com/dbeaver/dbeaver/issues/13542)).  The current workaround is to downgrade to the previous version (21.1.3).


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-08-29 21:08:53

------------

My workaround has been to use something like "seq_number" for the CSV and column in dBeaver, and then rename the column back to "sequence" after. It's not pretty, but it works. I just did this for the same problem with a column named "depth".


------------
    
    