
# Post \#73605230 [Link](https://stackoverflow.com/questions/73605230/)

## Composer error while Composer update in cpanel

**Vote**: 0 (696/702) **Views**: 569 (689/702) 

**Internal ID** \#2-7-433

Created at 2022-09-05 06:20:34

Tags: `php` `laravel` `composer-php` `laravel-8`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I have a Laravel project in, which laravel version is 8.75 and php version is 7.4.
When I upload my project in cpanel and go to terminal and run 'composer update' command ,it shows this type of error. I search this error but couldn't find any solution.
$ composer update
Composer is operating significantly slower than normal because you do not have the PHP curl extension enabled.
Loading composer repositories with package information
[Composer\Downloader\TransportException]
The "https://repo.packagist.org/packages.json" file could not be downloaded: allow_url_fopen must be enabled in php.ini (https:// wrapper is disabled in the server configuration by allow_url_fopen=0
Failed to open stream: no suitable wrapper could be found)
update [--with WITH] [--prefer-source] [--prefer-dist] [--prefer-install PREFER-INSTALL] [--dry-run] [--dev] [--no-dev] [--lock] [--no-install] [--no-autoloader] [--no-suggest] [--no-progress] [-w|--with-dependencies] [-W|--with-all-dependencies] [-v|vv|vvv|--verbose] [-o|--optimize-autoloader] [-a|--classmap-authoritative] [--apcu-autoloader] [--apcu-autoloader-prefix APCU-AUTOLOADER-PREFIX] [--ignore-platform-req IGNORE-PLATFORM-REQ] [--ignore-platform-reqs] [--prefer-stable] [--prefer-lowest] [-i|--interactive] [--root-reqs] [--] []...
See this image :
[enter image description here](https://i.stack.imgur.com/Xh1rl.png)


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2022-09-05 06:33:23

------------

You have to enable allow_url_fopen. here are the ref links how to do that
[allow_url_fopen cpanel](https://chemicloud.com/kb/article/how-to-enable-or-disable-allow_url_fopen-in-cpanel/)
[allow_url_fopen cpanel](https://support.cpanel.net/hc/en-us/articles/360053203413-Changing-the-allow-url-fopen-Setting-for-PHP)


------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-09-05 06:33:36

------------

You have to enable curl extension and allow_url_fopen in your php.ini file.

# Method 1


Open your Terminal and run below command to show php.ini path.
```
php --ini
```

[](https://i.stack.imgur.com/0uPSQ.png)
Open `php.ini` file then Search for `extension=curl` and Uncomment it by removing the semi-colon( ; ) in front of it.
```
// Before
;extension=curl

// After
extension=curl
```

then Search for `allow_url_fopen` and Change Value to On.
```
allow_url_fopen = On
```


# Method 2



1. Open your cPanel then Search for PHP and Click on MultiPHP INI Editor


[](https://i.stack.imgur.com/O58Jx.png)

1. Click on Editor Mode


[](https://i.stack.imgur.com/givUU.png)

1. Select Domain you are working on


[](https://i.stack.imgur.com/q8ezY.png)

1. Copy below lines and Paste in your PHP INI Editor


```
extension=curl
allow_url_fopen = On
```


1. Click on Save Button


[](https://i.stack.imgur.com/cAvZA.png)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-09-05 07:08:50

------------

You have to enable "`allow_url_fopen`" module through php in your cpanel, you can easily use the cpanel dashboard and navigate to "select php version" and there you can enable/disable all the modules you need, also you can change the php version if you so desire.
and as a recommendation, delete the composer.lock file and use `composer install` instead


------------
    
    