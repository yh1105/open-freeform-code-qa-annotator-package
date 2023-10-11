
# Post \#70098641 [Link](https://stackoverflow.com/questions/70098641/)

## Upgrading ASP.NET Core 5.0 IdentityServer4 to 6.0 error - no such table: Keys

**Vote**: 1 (672/702) **Views**: 1900 (611/702) 

**Internal ID** \#2-6-387

Created at 2021-11-24 15:20:08

Tags: `c#` `identityserver4` `.net-6.0` `duende-identity-server`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

After upgrading ASP.NET Core 5.0 with IdentityServer4 to 6.0 error - no such table: Keys
```
14:50:02.0033786|Failed executing DbCommand (4ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
SELECT "k"."Id", "k"."Algorithm", "k"."Created", "k"."Data", "k"."DataProtected", "k"."IsX509Certificate", "k"."Use", "k"."Version"
FROM "Keys" AS "k"
WHERE "k"."Use" = 'signing'
14:50:02.0179085|An exception occurred while iterating over the results of a query for context type 'xx.com.Data.AppDbContext'.
Microsoft.Data.Sqlite.SqliteException (0x80004005): SQLite Error 1: 'no such table: Keys'.
```

I cant find any doc on migration from .net 5 to 6 for IdentityServer
 


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2021-11-25 13:09:54

------------

Quick way to resolve this (sqlite):
```
CREATE TABLE Keys (
    Id                TEXT    NOT NULL
                              CONSTRAINT PK_Keys PRIMARY KEY,
    Version           INTEGER NOT NULL,
    Created           TEXT    NOT NULL,
    Use               TEXT,
    Algorithm         TEXT    NOT NULL,
    IsX509Certificate INTEGER NOT NULL,
    DataProtected     INTEGER NOT NULL,
    Data              TEXT    NOT NULL
);

CREATE INDEX IX_Keys_Use ON Keys (
    "Use"
);

CREATE INDEX sqlite_autoindex_Keys_1 ON Keys (
    Id COLLATE BINARY
);
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-12-04 00:50:56

------------

Disable the management of certificates via database (Table 'Keys' doesn't exists). You can stay loading certificates from any other available source.
```
services.AddIdentityServer(options => {
    options.KeyManagement.Enabled = false;
})
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-01-16 00:52:36

------------

Here's the correct schema for the Keys table
```
CREATE TABLE Keys (
    Id                nvarchar(450)    NOT NULL CONSTRAINT PK_Keys PRIMARY KEY,
    Version           INTEGER NOT NULL,
    Created           datetime2    NOT NULL,
    [Use]               nvarchar(450),
    Algorithm         nvarchar(100)    NOT NULL,
    IsX509Certificate INTEGER NOT NULL,
    DataProtected     INTEGER NOT NULL,
    Data              nvarchar(Max)    NOT NULL
);

CREATE INDEX IX_Keys_Use ON Keys (
    [Use]
);

CREATE INDEX sqlite_autoindex_Keys_1 ON Keys (
    Id 
);
```



------------
    
    