
# Post \#64776338 [Link](https://stackoverflow.com/questions/64776338/)

## Xdebug 3 - The setting 'xdebug.remote_***' has been renamed, see the upgrading guide

**Vote**: 22 (248/702) **Views**: 28625 (236/702) 

**Internal ID** \#2-7-437

Created at 2020-11-10 20:29:21

Tags: `php` `phpstorm` `xdebug`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I just installed Xdebug v3.0.0beta1 on my OSX and tried to use it on PhpStorm 2020.1, but I get this :
> Xdebug: [Config] The setting 'xdebug.remote_enable' has been renamed,
see the upgrading guide at
[https://xdebug.org/docs/upgrade_guide#changed-xdebug.remote_enable](https://xdebug.org/docs/upgrade_guide#changed-xdebug.remote_enable)
(See: [https://xdebug.org/docs/errors#CFG-C-CHANGED](https://xdebug.org/docs/errors#CFG-C-CHANGED)) Xdebug: [Config]
The setting 'xdebug.remote_host' has been renamed, see the upgrading
guide at
[https://xdebug.org/docs/upgrade_guide#changed-xdebug.remote_host](https://xdebug.org/docs/upgrade_guide#changed-xdebug.remote_host) (See:
[https://xdebug.org/docs/errors#CFG-C-CHANGED](https://xdebug.org/docs/errors#CFG-C-CHANGED)) Xdebug: [Config] The
setting 'xdebug.remote_mode' has been renamed, see the upgrading guide
at [https://xdebug.org/docs/upgrade_guide#changed-xdebug.remote_mode](https://xdebug.org/docs/upgrade_guide#changed-xdebug.remote_mode)
(See: [https://xdebug.org/docs/errors#CFG-C-CHANGED](https://xdebug.org/docs/errors#CFG-C-CHANGED)) Xdebug: [Config]
The setting 'xdebug.remote_port' has been renamed, see the upgrading
guide at
[https://xdebug.org/docs/upgrade_guide#changed-xdebug.remote_port](https://xdebug.org/docs/upgrade_guide#changed-xdebug.remote_port)
And the links provided lead to nothing but an image with bugs.
My question is what are the right settings to set and where to actually change them since i have nothing about xdebug in my php.ini file.


----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2020-11-10 22:09:39

------------

Xdebug 3 will be supported from PhpStorm  version only, which is currently in EAP stage (Early Access Program) and will be released in about 1 month time.
For the moment you will have to either stick to Xdebug 2.9 for your 2020.1 IDE version or try latest 2020.3 EAP build: [https://www.jetbrains.com/phpstorm/nextversion/](https://www.jetbrains.com/phpstorm/nextversion/)
Xdebug 3 is supported in the most recent  build: [https://blog.jetbrains.com/phpstorm/2020/11/phpstorm-2020-3-eap-6/](https://blog.jetbrains.com/phpstorm/2020/11/phpstorm-2020-3-eap-6/)

---


As for the Xdebug 3 upgrade in terms of making changes for Xdebug settings (php.ini) -- check this link: [https://xdebug.org/docs/upgrade_guide](https://xdebug.org/docs/upgrade_guide)

---



It works fine for me with Xdebug 3.0.0beta1, PHP 7.4 x64 on Windows 10 -- see this question: [https://stackoverflow.com/a/64820427/783119](https://stackoverflow.com/a/64820427/783119)
Those errors that you see indicate that you still have Xdebug 2 config values in your php.ini.


------------
    
    
## Answer \#1

 Vote: 39

Created at 2021-01-07 15:00:01

------------

PhpStorm 2020.3 supports XDebug3. There is a [detailed upgrade guide](https://xdebug.org/docs/upgrade_guide) on how to change your settings properly.
In my case (using Docker), I had to change the settings
from:
```
; v2.*

[Xdebug]
zend_extension=xdebug.so
xdebug.remote_enable=1
xdebug.remote_autostart=1
xdebug.remote_connect_back=off
xdebug.remote_host=host.docker.internal

;# 9000 is default (not required to set).
xdebug.remote_port=9000
```

to:
```
; v3.*

[Xdebug]
zend_extension=xdebug.so
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_host=host.docker.internal

;# 9003 is now the default (set this for old PhpStorm settings).
xdebug.client_port=9000
```



------------
    
    
## Answer \#2

 Vote: 6

Created at 2020-12-22 10:49:58

------------

My php -v
```
PHP 7.2.34-8+ubuntu20.04.1+deb.sury.org+1 (cli) (built: Oct 31 2020 16:57:33) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.2.34-8+ubuntu20.04.1+deb.sury.org+1, Copyright (c) 1999-2018, by Zend Technologies
    with Xdebug v3.0.1, Copyright (c) 2002-2020, by Derick Rethans
```

I got Xdebug v3 working with PHPStorm 2020.2.3 with these steps:

1. export XDEBUG_SESSION=PHPSTORM
2. Update php.init file xdebug.extension=/usr/lib/php/20200930/xdebug.so <== path to your xdebug.so file
#xdebug.remote_autostart=1
xdebug.start_with_request=yes
#xdebug.remote_connect_back=1
xdebug.discover_client_host=true
xdebug.remote_cookie_expire_time = 3600
#xdebug.remote_enable = 1
#xdebug.remote_host = localhost
xdebug.client_host=localhost
#xdebug.remote_port = 9000
xdebug.client_port = 9000
xdebug.remote_handler = dbgp
xdebug.idekey=PHPSTORM
xdebug.mode = debug
 '#' is old version (Xdebug 2.x)
3. Reset apache2: sudo service apache2 restart




------------
    
    
## Answer \#3

 Vote: 2

Created at 2020-12-11 12:47:45

------------

In my case I have PHP Storm 2020.2 and haven't changed anything in the IDE side, however, in the server side I have to change a few things
In order to enable Xdebug 3 you need to set `xdebug.mode = debug` for step debugging
In my particular server config I have set it like this:
- 
> zend_extension=xdebug.so
- 
> xdebug.mode = debug
Then in my .user.ini in the root of my website I have set this:
```
[xdebug]
xdebug.client_host = "10.0.0.2"
xdebug.client_port = 9001
xdebug.remote_connect_back = false
xdebug.log = '/var/www/clients/client1/web2/web/j39vm36/administrator/logs/xdebug.log'
xdebug.profiler_output_name = cachegrind.out.%t.%p
xdebug.output_dir = '/var/www/clients/client1/web2/web/j39vm36/administrator/logs/'
xdebug.var_display_max_depth = -1
xdebug.var_display_max_children = -1
xdebug.var_display_max_data = -1
```



------------
    
    