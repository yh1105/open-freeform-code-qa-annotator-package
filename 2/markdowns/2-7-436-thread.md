
# Post \#73155832 [Link](https://stackoverflow.com/questions/73155832/)

## Failure to install Laravel 9

**Vote**: 0 (696/702) **Views**: 922 (664/702) 

**Internal ID** \#2-7-436

Created at 2022-07-28 16:01:30

Tags: `laravel`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

Tying to install a Laravel 9 project.
Updated Composer from 2.0.8 to 2.3.10
Running `composer create-project laravel/laravel blog "9.*"`
Gives me : `Could not find package laravel/laravel with version 9.* in a version installable using your PHP version, PHP extens ions and Composer version.`


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2022-07-28 16:10:03

------------

Update your PHP version, because laravel 9 required php >= 8.0
[Server Requirements](https://laravel.com/docs/9.x/deployment#server-requirements)


------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-07-28 19:38:48

------------

PHP issue, update PHP at least to 8.0
OR
Use Laravel 8 ,that supports php 7.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-07-29 11:02:27

------------

Upgrade your php version 8.0 or more.
laravel 9 requard at least 8.0 php version.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-09-10 20:46:46

------------

upgrade you php version to < 8 and i recomde to upgrade your node js if you are using laragon node js is in 14.16.1 so upgrade it to it not an obligation but its better


------------
    
    