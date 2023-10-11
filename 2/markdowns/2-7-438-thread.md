
# Post \#69218666 [Link](https://stackoverflow.com/questions/69218666/)

## Doctrine Migration class not found error when attempting to run down/rollback command

**Vote**: 4 (532/702) **Views**: 6566 (490/702) 

**Internal ID** \#2-7-438

Created at 2021-09-17 06:25:42

Tags: `php` `symfony` `doctrine-orm` `doctrine` `doctrine-migrations`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

When I run `bin/console doctrine:migrations:list` I see the Migration listed as:
`Application\Migrations\Version20210909072642`
I am attempting to rollback a migration and I have tried a few different versions:
```
bin/console --env=dev doctrine:migrations:execute 'Application\DoctrineMigrations\Version20210909072642' --down --no-interaction -vvv
bin/console --env=dev doctrine:migrations:execute Version20210909072642 --down --no-interaction -vvv
bin/console --env=dev doctrine:migrations:execute 20210909072642 --down --no-interaction -vvv
```

Has this feature changed with a recent [DoctrineMigrationsBundle](https://symfony.com/bundles/DoctrineMigrationsBundle/current/index.html) update?
Every time I run it I get the following error:
```
In MigrationClassNotFound.php line 15:
                                                          
  [Doctrine\Migrations\Exception\MigrationClassNotFound]  
  Migration class "20210909072642" was not found?
```

My Doctrine config looks like this:
```
doctrine_migrations:
    migrations_paths:
        'Application\Migrations': 'app/DoctrineMigrations'
    storage:
        table_storage:
            table_name: 'migration_versions'
```



----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2021-09-17 06:43:15

------------

[migrations_paths](https://www.doctrine-project.org/projects/doctrine-migrations/en/3.0/reference/configuration.html#configuration) in your configuration sets the namespace your migration is located under as `Application\Migrations` and not `Application\DoctrineMigrations`.
Run the migrate command with `Application\Migrations\Version20210909072642`.
```
bin/console --env=dev doctrine:migrations:execute \
'Application\Migrations\Version20210909072642' --down --no-interaction -vvv
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2022-09-22 13:12:31

------------

Sorry to dig up this post, but I had a similar concern with Symfony 6.
I did a test migration, and I had the same problem.
Here my migration list
```
+------------------------------------------+----------+---------------------+----------------+-------------+
| Migration Versions                                                                         |             |
+------------------------------------------+----------+---------------------+----------------+-------------+
| Migration                                | Status   | Migrated At         | Execution Time | Description |
+------------------------------------------+----------+---------------------+----------------+-------------+
| DoctrineMigrations\Version20220922124029 | migrated | 2022-09-22 14:41:13 | 3.352s         |             |
+------------------------------------------+----------+---------------------+----------------+-------------+
```

When i use the command `php bin/console d:m:e --down --no-interaction DoctrineMigrations\Version20220922124029`
I got the error message:
```
In MigrationClassNotFound.php line 15:
Migration class "DoctrineMigrationsVersion20220922124029" was not found?
```

Here the `\` is escaped, so we have to use the `\\` instead, like:
`d:m:e --down --no-interaction DoctrineMigrations\\Version20220922124029`
and now i got `[notice] Executing DoctrineMigrations\Version20220922124029 down` working.
Hope it helps someone in same case.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-11-24 17:25:27

------------

I had the same problem:
```
Migration class "20221124124122" was not found?
```

And was solved when i ran:

1. php bin/console d:m:list (To know how the interface see my migrations);
2. php bin/console d:m:execute 'DoctrineMigrations\VersionYYYYMMDDSSxxx' --up (To migrate the version. Remember to put the version in quotes).




------------
    
    