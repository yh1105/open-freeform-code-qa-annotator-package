
# Post \#68030588 [Link](https://stackoverflow.com/questions/68030588/)

## Xdebug failed install on Mac m1

**Vote**: 6 (473/702) **Views**: 6417 (495/702) 

**Internal ID** \#2-7-446

Created at 2021-06-18 06:50:53

Tags: `php` `xdebug`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I'm trying to install Xdebug on my Mac m1, I followed this page([https://xdebug.org/docs/install](https://xdebug.org/docs/install)) to install. This is the step I followed:
step1 => go to cmd: arch -x86_64 sudo pecl install xdebug
step2 => go to php.ini delete this line of code
```
zend_extension="xdebug.so"
```

step3 => go to php.ini add this
```
[xdebug]
zend_extension=/opt/homebrew/lib/php/pecl/20190902/xdebug.so
xdebug.mode=debug
xdebug.client_host=127.0.0.1
xdebug.client_port="9003"
```

step4 => go to cmd: php -v

[](https://i.stack.imgur.com/Kd87U.png)



----------
        
## Answer \#0

**Accepted** Vote: 17

Created at 2021-06-18 07:58:51

------------

What happened here is that you built a `x86_64` build of the extension, but homebrew likely used the `arm64e` architecture. These architectures are not compatible.
You can verify what your PHP's architecture is with:
```
file `which php`
```

If that says `arm64e`, then you need the original command from the documentation:
```
sudo pecl install xdebug
```

And if it's `x86_64`, then you need the command modified for Apple's dual architecture:
```
arch -x86_64 sudo pecl install xdebug
```

For what it's worth, the docs say: On Apple M1 hardware, you  instead need to use..., not .


------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-09-23 08:28:50

------------

If you encounter this:
- `which php``Mach-O 64-bit executable arm64`- `sudo pecl install xdebug`
Try this:
```
arch -arm64e sudo pecl install xdebug
```

Note: `arch -arm64` shown bad cpu type error in my case.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-05-21 07:32:28

------------


### Solution now on M1


if `pecl install xdebug` doesn't work. run it with `sudo` command.
don't try the arch x84 command as it wont work anymore, since php is now build in arm cpu architecture.
Solution:
```
sudo pecl install xdebug
```



------------
    
    