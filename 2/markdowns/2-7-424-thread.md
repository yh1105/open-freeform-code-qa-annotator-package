
# Post \#52231248 [Link](https://stackoverflow.com/questions/52231248/)

## Laravel showing "Failed to clear cache. Make sure you have the appropriate permissions"

**Vote**: 139 (36/702) **Views**: 181042 (43/702) 

**Internal ID** \#2-7-424

Created at 2018-09-08 01:34:29

Tags: `laravel` `composer-php`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

Laravel was displaying to me "Access denied for user 'homestead'@'localhost' (using password: YES)". One solution for this was clearing the cache and the config cache stored, all this with these three commands:

```
php artisan cache:clear
php artisan config:clear
php artisan config:cache
```


After `php artisan cache:clear`, terminal says:

`Failed to clear cache. Make sure you have the appropriate permissions.` (with red background)

Doing the second and third code (`php artisan config:clear` and `php artisan config:cache`) works fine! But it still gives me the error when typing the first line. Can anyone explain why? 


----------
        
## Answer \#0

**Accepted** Vote: 396

Created at 2018-09-14 11:00:51

------------

If the `data` directory doesn't exist under (`storage/framework/cache/data`), then you will have this error.    

This `data` directory doesn't exist by default on a fresh/new installation.

Creating the `data` directory manually at (`storage/framework/cache`) should fix this issue.


------------
    
    
## Answer \#1

 Vote: 131

Created at 2019-01-30 13:59:23

------------

Try deleting these cached files under bootstrap folder:
```
/bootstrap/cache/packages.php
/bootstrap/cache/services.php
/bootstrap/cache/config.php
```

Then run `php artisan cache:clear`


------------
    
    
## Answer \#2

 Vote: 55

Created at 2020-03-16 21:38:26

------------

Calling 

```
php artisan config:cache
```


before

```
php artisan cache:clear
```


fixed the issue.


------------
    
    
## Answer \#3

 Vote: 42

Created at 2018-11-17 17:34:08

------------

Just only add folder named `data` in `storage/framework/cache/` and try:

```
php artisan cache:clear
```



------------
    
    
## Answer \#4

 Vote: 34

Created at 2020-10-03 04:36:10

------------

[](https://i.stack.imgur.com/hMopa.png)
First try:
Check if there is a "data" folder inside "storage/framework/cache/".
If there is not, then create it manually. (create a new folder with name "data")
Option 2:
If there is a "data" folder inside "storage/framework/cache/".
Remove ALL existing folders inside there.
Then final, running this:
```
php artisan cache:clear
```

This should fix this issue: "Failed to clear cache. Make sure you have the appropriate permissions."


------------
    
    
## Answer \#5

 Vote: 32

Created at 2021-04-19 05:07:08

------------

Calling the following 4 commands should fix most of the permission issues on laravel.
```
sudo chown -R $USER:www-data storage
sudo chown -R $USER:www-data bootstrap/cache
chmod -R 775 storage
chmod -R 775 bootstrap/cache
```

Basically, `chown -R $USER:www-data` what this does is it set current user `$USER` as owner and `www-data` as group and `chmod -R 775` gives 7 to user,7 to group and 5 to other.
 You need to run above command from the laravel project directory, else, you need to provide full path like /var/www/project_name/storage


------------
    
    
## Answer \#6

 Vote: 20

Created at 2021-05-10 06:44:46

------------

In Laravel 8 I solved my issue by first running `composer dump-autoload` and then used `php artisan config:cache`


------------
    
    
## Answer \#7

 Vote: 8

Created at 2021-03-05 16:36:52

------------

Deleting and adding back the `./storage/framework/cache/data` folder worked for me.


------------
    
    
## Answer \#8

 Vote: 6

Created at 2020-10-14 10:17:56

------------

You should update the permission using the below steps:

1. Check user using command "whoami" then let output is "ironman"
2. Run below command if "data" folder exists in the cache directory


`sudo chown -R ironman:ironman storage/framework/cache/data/`
This will resolve your below issue[](https://i.stack.imgur.com/A2wZ7.png)


------------
    
    
## Answer \#9

 Vote: 3

Created at 2022-03-01 08:34:02

------------

Delete all subfolders under:
```
storage/framework/cache/data/
```



------------
    
    
## Answer \#10

 Vote: 2

Created at 2019-08-19 00:32:20

------------

I ran my project in a docker container, then later tried accessing it via laragon, I had similar issue, this was due to compiled configurations in `/bootstrap/cache/config.php`.

I fixed fit by running `php artisan config:clear`, this deletes the `/bootstrap/cache/config.php` file automatically.


------------
    
    
## Answer \#11

 Vote: 2

Created at 2020-08-31 20:36:39

------------

I had the same problem but noticed if you run php artisan config:clear it also by default runs cache:clear right after it so when you run it again there is not cache in it and gives that error. you only need to run php artisan config:clear. I am not sure why cache:clear fails when its ran alone but running the config:clear is a good alternative.
Here is a helpful alias i use to clear everything in the app.
```
laraclear='php artisan config:cache && php artisan config:clear && php artisan view:clear && php artisan route:clear && php artisan telescope:clear && php artisan debugbar:clear'
```

Remove any unwanted commands that you do not use in it.


------------
    
    
## Answer \#12

 Vote: 2

Created at 2021-08-14 18:56:37

------------

(For MAC Users)
Sometimes, it means current user don't have sufficient permission to the storage/framework/cache/data/ folder. Run

1. sudo chmod -R ug+rwx storage/framework/cache/data/


then

1. php artisan cache:clear


Hope sometimes it work for you.


------------
    
    
## Answer \#13

 Vote: 2

Created at 2022-02-25 19:50:48

------------

In my case the problem was I had 'CACHE_DRIVER=memcached' in .env but didn't have memcached installed.  Switching to the file driver or installing memcached fixed the problem for me.


------------
    
    
## Answer \#14

 Vote: 1

Created at 2018-09-08 08:53:50

------------

You may need to clear the autoloader with
`composer dump-autoload`

If that doesn't work you can manually remove the following non-tracked (usually) files to clear the autoloader and cache if they get jammed up:

`/bootstrap/cache/packages.php`

`/bootstrap/cache/services.php`


------------
    
    
## Answer \#15

 Vote: 1

Created at 2020-10-02 09:59:36

------------

Can't this solve it?
```
$php artisan optimize:clear
Compiled views cleared!
Application cache cleared!
Route cache cleared!
Configuration cache cleared!
Compiled services and packages files removed!
```



------------
    
    
## Answer \#16

 Vote: 1

Created at 2022-03-17 09:35:20

------------

For Laravel Homestead on Windows:
In your `.env` file, change your `CACHE_DRIVER` from `file` to `memcached`.
Run `php artisan cache:clear`.


------------
    
    
## Answer \#17

 Vote: 1

Created at 2022-12-04 10:59:34

------------




1. get user name by this command : whoami
2. Run command if "data" folderexists in the cache directory. sudo chown -R YourUSERName:YourUSERName storage/framework/cache/data/




------------
    
    
## Answer \#18

 Vote: 0

Created at 2020-10-28 17:04:23

------------

Had the same problem on my vagrant/homestead VM. All the other things in this thread didn't help.
The solution was `vagrant reload --provision`


------------
    
    
## Answer \#19

 Vote: 0

Created at 2021-04-11 11:13:14

------------

Giving  permission to the storage directory solved this problem for me.
```
sudo chmod -R 775 storage
```



------------
    
    
## Answer \#20

 Vote: 0

Created at 2022-02-15 11:16:38

------------

I am running my project in Homestead.
My environment :
> Ubuntu1~20.04+
Laravel 6.20.26
My output
[](https://i.stack.imgur.com/mYMXN.png)
display real fatal info
Edit `src/Illuminate/Filesystem/Filesystem.php 598:14`
original code:
```
if (! $preserve) {
    @rmdir($directory);
}
```

debug code
```
if (! $preserve) {
    rmdir($directory);
}
```

Then re-execute `php artisan cache:clear`
The output changed:
[](https://i.stack.imgur.com/gVwkM.png)
Then, I just resolve `Text file busy`


------------
    
    
## Answer \#21

 Vote: 0

Created at 2022-11-22 07:14:25

------------

Ensure `APP_URL` is properly set on `.env` file. Changing `http` to `https` in the `APP_URL` variable on `.env` file solved it for me.


------------
    
    
## Answer \#22

 Vote: 0

Created at 2023-01-20 07:00:44

------------


1. In my case, there is no cache available that's why the artisan is unable to delete or reset the cache.
2. First browse the website and allow it to create some cache then try to clear it.
3. You can check whether the cache is available or not in directory storage/framework/cache/*




------------
    
    
## Answer \#23

 Vote: -1

Created at 2021-03-05 14:01:51

------------

got the same error. try giving chmod 777 permission in the concerned folder


------------
    
    
## Answer \#24

 Vote: -2

Created at 2021-04-14 10:24:45

------------

My guess is you have a permission/ownership problem. Either set up the permissions correctly or recursively delete the cache folder manually and re-create it:
```
sudo rm -Rf storage/framework/cache
mkdir storage/framework/cache
```



------------
    
    
## Answer \#25

 Vote: -3

Created at 2018-09-08 01:40:37

------------

maybe you need to chmod 777 -R storage folder.
and i think it can also chown www-data:www-data  


------------
    
    
## Answer \#26

 Vote: -3

Created at 2021-01-05 05:48:37

------------

Incase non of these works for you, simply run your php artisan cache:clear command with sudo.
e.g. `sudo php artisan cache:clear`
Thank me later~!


------------
    
    