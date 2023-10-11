
# Post \#41209349 [Link](https://stackoverflow.com/questions/41209349/)

## require(vendor/autoload.php): failed to open stream

**Vote**: 254 (9/702) **Views**: 1003817 (1/702) 

**Internal ID** \#2-7-442

Created at 2016-12-18 14:33:50

Tags: `php` `email` `composer-php` `phpmailer`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I know that this issue has been posted many times, but for me it seems to be a different problem.
Indeed, this error
> Warning: require(vendor/autoload.php): failed to open stream: No such file or directory in C:\xampp\htdocs\site_web\send_mail.php on line 3Fatal error: require(): Failed opening required 'vendor/autoload.php' (include_path='C:\xampp\php\PEAR') in C:\xampp\htdocs\site_web\send_mail.php on line 3
appears at the begining of my code from this line:
```
require 'vendor/autoload.php';
```

So, I guess there must be a /vendor/autoload.php file somewhere in my computer (I have installed composer and ran `composer require phpmailer/phpmailer`).
So, I looked for this file using: `dir /s autoload.php` in the Windows command line, and found one here: `C:\Windows\SysWOW64\vendor\autoload.php`,
but for me,  folder has nothing to see with , I don't see what I am missing here.


----------
        
## Answer \#0

**Accepted** Vote: 415

Created at 2016-12-18 15:01:05

------------

What you're missing is running `composer install`, which will import your packages and create the vendor folder, along with the autoload script.
Make sure your relative path is correct. For example the example scripts in PHPMailer are in `examples/`, below the project root, so the correct relative path to load the composer autoloader from there would be `../vendor/autoload.php`.
The autoload.php you found in `C:\Windows\SysWOW64\vendor\autoload.php` is probably a global composer installation – where you'll usually put things like phpcs, phpunit, phpmd etc.
`composer update` is  the same thing, and probably  what you want to use. If your code is tested with your current package versions then running `update` may cause breakages which may require further work and testing, so don't run `update` unless you have a specific reason to and understand exactly what it means. To clarify further – you should probably only ever run `composer update` locally, never on your server as it is reasonably likely to break apps in production.
I often see complaints that people can't use composer because they can't run it on their server (e.g. because it's shared and they have no shell access). In that case, you  still use composer: run it locally (an environment that has no such restrictions), and upload the local vendor folder it generates along with all your other PHP scripts.
Running `composer update`  performs a `composer install`, and if you do not currently have a `vendor` folder (normal if you have a fresh checkout of a project), then it will create one, and also overwrite any `composer.lock` file you already have, updating package versions tagged in it, and this is what is potentially dangerous.
Similarly, if you do not currently have a `composer.lock` file (e.g. if it was not committed to the project), then `composer install` also effectively performs a `composer update`. It's thus vital to understand the difference between the two as they are definitely  interchangeable.
It is also possible to update a single package by naming it, for example:
```
composer update ramsey/uuid
```

This will re-resolve the version specified in your `composer.json` and install it in your vendor folder, and update your `composer.lock` file to match. This is far less likely to cause problems than a general `composer update` if you just need a specific update to one package.
It is normal for libraries to  include a `composer.lock` file of their own; it's up to apps to fix versions, not the libraries they use. As a result, library developers are expected to maintain compatibility with a wider range of host environments than app developers need to. For example, a library might be compatible with Laravel 5, 6, 7, and 8, but an app using it might require Laravel 8 for other reasons.
Composer 2.0 removed any remaining inconsistencies between install and update results; if you're running composer 1.x you should definitely [upgrade](https://getcomposer.org/upgrade/UPGRADE-2.0.md).


------------
    
    
## Answer \#1

 Vote: 75

Created at 2017-11-27 17:17:27

------------

If you get the error also when you run 

```
composer install
```


Just run this command first

```
composer dump-autoload
```


This command will clean up all compiled files and their paths.


------------
    
    
## Answer \#2

 Vote: 25

Created at 2018-01-28 14:26:10

------------

@Bashir almost helped me but I needed:
```
composer update --no-scripts
```

Apparently this prevents any scripts from being included before executing artisan.
I found the answer here half-way down the page:
[https://laracasts.com/discuss/channels/general-discussion/fatal-error-class-illuminatefoundationapplication-not-found-in-pathtoprojectbootstrapappphp-on-line-14?page=0](https://laracasts.com/discuss/channels/general-discussion/fatal-error-class-illuminatefoundationapplication-not-found-in-pathtoprojectbootstrapappphp-on-line-14?page=0)


------------
    
    
## Answer \#3

 Vote: 14

Created at 2017-11-02 20:32:28

------------


## Proper autoload.php configuration:



:

Your autoload.php path is wrong. ie. `C:\Windows\SysWOW64\vendor\autoload.php`
To date: you need to change it to: `C:\Users\<Windows User Name>\vendor\autoload.php`


---




We will take [facebook/php-graph-sdk](https://github.com/facebook/php-graph-sdk/blob/5.x/docs/examples/facebook_login.md) as an example; change to `Package Name` as needed.


1. Install composer.exe
2. Open CMD Prompt. + R + type CMD
3. Run This command: composer require facebook/graph-sdk
4. Include path in your PHP page: require_once 'C:\Users\<Windows User Name>\vendor\autoload.php';
5. Define configuration Secrets and Access Token for your package...etc.
6. Happy codinig.




---





Installing composer on windows will set this default path for your pacakges; you can find them there and include the autoloader path:

```
C:\Users\<Windows User Name>\vendor
```


For the same question you asked; the answer was this path for [WAMP Server 64 BIT for Windows](https://sourceforge.net/projects/wampserver/files/latest/download).

Then simply in your PHP Application change this:

```
require_once __DIR__ . '/vendor/autoload.php';
```


To:

```
require_once 'C:\Users\<Windows User Name>\vendor\autoload.php';
```


[Find your windows username](http://itgroove.net/oh365eh/2015/04/12/how-to-find-your-user-name-on-your-pc/) under `C:\Users\`

Before all this, as pointed before in B) , you need to run this command:

```
composer require <package name>
```


for facebook php SDK for example:

```
composer require facebook/graph-sdk
```



---






------------
    
    
## Answer \#4

 Vote: 12

Created at 2018-04-26 14:25:30

------------

First make sure you have installed the composer.

```
composer install
```


If you already have installed then update the composer.

```
composer update
```



------------
    
    
## Answer \#5

 Vote: 9

Created at 2019-01-22 04:25:00

------------

If you have cloned your project from Github or got it from somewhere else,  you will encounter this error. That's because you are missing the  and other files. The  contains packages which are dependent to your project. The package dependencies are stored in `composer.json` file and the folder was  while .

Fix this error by simply running:

`composer install`

Then you will get all the assets needed for your project.


------------
    
    
## Answer \#6

 Vote: 8

Created at 2018-09-30 21:45:36

------------

First, review route inside index.php

```
require __DIR__.'/../vendor/autoload.php';

$app = require_once __DIR__.'/../bootstrap/app.php';
```


in my case the route did not work, I had to review the directories.


------------
    
    
## Answer \#7

 Vote: 6

Created at 2020-01-21 10:25:37

------------

For me 
Just run this command first

```
composer dump-autoload
```


to add vendor folder.

then run this command 

```
composer update --no-scripts
```


to update composer.


------------
    
    
## Answer \#8

 Vote: 3

Created at 2019-12-19 10:55:49

------------

Create composer.json file with requisite library for ex:

```
{
    "require": {
        "mpdf/mpdf": "^6.1"
    }
}
```


Execute the below command where composer.json exists:

```
composer install
```


In case of Drupal :

Use the web root folder of drupal to include autoload for ex:

```
define('DRUPAL_ROOT', getcwd());
require_once DRUPAL_ROOT . '/vendor/autoload.php';
```


In case of other systems:
Use the root folder variable or location to include the autoload.php


------------
    
    
## Answer \#9

 Vote: 2

Created at 2017-06-13 18:05:52

------------

I had this path in my machine:

```
C:/xampp5.0/htdocs/project-recordando-symfony/project-recordando-symfony
```


Then I ran `composer install` or/and `composer update` and it returned this error:

```
ErrorException ZipArchive::extractTo...
```


That error is because your path is too much long, I changed to:

```
C:/xampp5.0/htdocs/p-symfony/*
```


and worked!


------------
    
    
## Answer \#10

 Vote: 2

Created at 2017-12-20 09:38:52

------------

run `composer update`. That's it


------------
    
    
## Answer \#11

 Vote: 1

Created at 2018-05-18 18:44:46

------------

I was able to resolve by removing composer and reinstalling the proper way. Here is what I did:

- - - [https://getcomposer.org/doc/00-intro.md](https://getcomposer.org/doc/00-intro.md)[https://getcomposer.org/installer](https://getcomposer.org/installer)

I was then able to get composer install to work again. Found my answer at the bottom of this issue: [https://github.com/composer/composer/issues/5510](https://github.com/composer/composer/issues/5510)


------------
    
    
## Answer \#12

 Vote: 1

Created at 2019-07-19 04:51:14

------------

In your project folder, the  is missing so you got this error:

> Warning: require(vendor/autoload.php): failed to open stream: No such file or directory in

When you download the project through , the project is downloaded without the 

You need `/vendor` because all your packages are there, including all the classes Laravel uses. The vendor directory contains your Composer dependencies.

The solution is simple, 

```
composer update --no-scripts 
composer update
```


- `composer update --no-scripts``composer.json`- `composer update``composer.json`

With this command, you will re-create the  in your project and after that your project will start working normally.


------------
    
    
## Answer \#13

 Vote: 1

Created at 2020-03-03 19:42:14

------------

This error occurs because of missing some files and the main reason is "[Composer](https://getcomposer.org)"


[](https://i.stack.imgur.com/Ozo3T.jpg)

First Run these commands in CMD

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'e0012edf3e80b6978849f5eff0d4b4e4c79ff1609dd1e613307e16318854d24ae64f26d17af3ef0bf7cfb710ca74755a') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```


Then
Create a New project
Example:

```
D:/Laravel_Projects/New_Project
laravel new New_Project
```


After that start the server using

```
php artisan serve
```



------------
    
    
## Answer \#14

 Vote: 1

Created at 2022-11-24 10:56:01

------------

In my case, It was due to the non-fully installation of the project, because I didn't have enough space on my hard disk


------------
    
    
## Answer \#15

 Vote: 0

Created at 2019-12-13 11:29:49

------------

There will be a directory called "vendor" that needs to be in your root directory if you have a cloned repository and trying to set up that time this type of error occurring.

".gitingore" file has written code to not include vendor directory on GIT so after cloning GIT your project facing the issue of missing vendor directory. 

Once you add vendor directory your project will start working again.  


------------
    
    
## Answer \#16

 Vote: -3

Created at 2018-02-19 07:37:42

------------

Change the auto_prepend_file property on php.ini 

```
; Automatically add files before PHP document. 
;http://php.net/auto-prepend-file 
auto_prepend_file =
```



------------
    
    
## Answer \#17

 Vote: -3

Created at 2021-03-11 16:56:50

------------

In  linux first add github Personal access tokens

1. Navigate to GitHub's Personal Access Tokens page.
2. Hit "Generate new token" button.
3. Type something meaningful "Note", select "repo" as scope and hit "Generate token" button.
4. Take a note of the token.


5 type in terminal with you new "personal access token"
```
export COMPOSER_AUTH='{"github-oauth":{"github.com":"AB8cd4cab23a9d5399934a7d7698d3fa74e9cfAB"}}'
```

Run in terminal  `composer install`


------------
    
    