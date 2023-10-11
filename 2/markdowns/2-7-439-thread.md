
# Post \#56219962 [Link](https://stackoverflow.com/questions/56219962/)

## Composer require ext-zip fails

**Vote**: 25 (228/702) **Views**: 43371 (184/702) 

**Internal ID** \#2-7-439

Created at 2019-05-20 11:21:02

Tags: `php` `composer-php` `phpstorm`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I am told by PHPStorm that I need to composer require ext-zip, however, that command is failing...

PHPStorm says

[](https://i.stack.imgur.com/Ir6Qy.png)

The command I am issuing is

```
composer require ext-zip
```


results in

```
Your requirements could not be resolved to an installable set of packages.
```


and

```
Installation failed, reverting ./composer.json to its original content.
```



----------
        
## Answer \#0

**Accepted** Vote: 36

Created at 2019-05-20 11:37:39

------------

 - add ext-zip to your required section of composer.json:

```
{
    "require" : {
        "ext-zip": "*"
    }
}
```


 - install php-zip extension:



Uncomment this line in your php.ini

> ;extension=php_zip.dll



> sudo apt-get install php-zip

or

> sudo apt-get install php7.0-zip (make sure you typed YOUR php version, you can check your version by doing php -v command)

Then, you need to restart your web server.

> sudo service apache2 restart


------------
    
    
## Answer \#1

 Vote: 10

Created at 2019-05-20 11:50:36

------------

If your code runs OK - you've already got the zip extension installed on your machine. PHPStorm adds this suggestion to ensure that anywhere else that the project is deployed also has the right extensions too.

Manually adding the line in your composer.json file (`require` block) `"ext-zip": "*",` (and others that it can suggest, such as `ext-apc`, `ext-redis` and `ext-json`, as well as any others that you might be using) will make sure that when you deploy it composer can also check that the appropriate extra items are installed.

It's only a warning though, and you could ignore it - or you can allow composer to make sure that your servers are setup as they would be needed to run your code, and do things with zip-files. If your server doesn't have ext-zip installed, `composer install` would complain, and stop - saving issues later when you discover that code fails without the zip extension, et al.


------------
    
    
## Answer \#2

 Vote: 9

Created at 2019-05-20 11:38:57

------------

The given hint comes from PhpStorm, not from composer itself: your IDE has detected that your code uses a method (or in this case: the `ZipArchive` class) that is only available when the ZIP extension is enabled. But your `composer.json` did not contain that requirement so far.

So, PhpStorm asks you to add this requirement to the JSON file to make the requirements to run your code more precise. How you solve that requirement is up to you: the best way would be to install that extension, but that is out of composer's scope


------------
    
    