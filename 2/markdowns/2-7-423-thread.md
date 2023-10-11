
# Post \#63110475 [Link](https://stackoverflow.com/questions/63110475/)

## PhpStorm Debugger extension is not detected while using with PHPUnit

**Vote**: 33 (185/702) **Views**: 21298 (288/702) 

**Internal ID** \#2-7-423

Created at 2020-07-27 07:20:46

Tags: `php` `docker` `phpunit` `phpstorm` `xdebug`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I am using PHPUnit through PhpStorm with a remote interpreter from docker.
The Container is run through `docker-compose`
PHPUnit works. Xdebug works through the browser. In `docker-php-ext-xdebug.ini`, I have all the mandatory options, and I can see in the `CLI Interpreter Config` that PhpStorm does load this config.
BUT when I try to run PHPUnit with the debugger I get:
> PhpStorm Debugger extension is not detected
[](https://i.stack.imgur.com/A7YuV.png)

[](https://i.stack.imgur.com/uj2SX.png)

```
FROM php:7.4-fpm-alpine

# OS DEPENDENCIES
RUN apk update
RUN apk add --no-cache bash git curl libmcrypt libmcrypt-dev openssh-client icu-dev
RUN apk add --no-cache libxml2-dev freetype-dev libpng-dev libjpeg-turbo-dev zip libzip-dev g++ make autoconf
RUN apk add --no-cache postgresql-dev
RUN docker-php-source extract
RUN pecl install xdebug redis
RUN docker-php-ext-enable xdebug redis
RUN docker-php-source delete
RUN docker-php-ext-install -j$(nproc) pgsql
RUN docker-php-ext-install -j$(nproc) pdo_pgsql
RUN docker-php-ext-install soap intl zip
RUN docker-php-ext-install opcache

# XDEBUG CONFIGURATION
ARG XDEBUG_REMOTE_HOST
ARG XDEBUG_REMOTE_PORT
ARG XDEBUG_REMOTE_CONNECT_BACK
ARG XDEBUG_INI=/usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

RUN echo "error_reporting = E_ALL" >> ${XDEBUG_INI}
RUN echo "display_startup_errors=1" >> ${XDEBUG_INI}
RUN echo "display_errors=1" >> ${XDEBUG_INI}
RUN echo "xdebug.profiler_enable=1" >> ${XDEBUG_INI}
RUN echo "xdebug.remote_enable=1" >> ${XDEBUG_INI}
RUN echo "xdebug.remote_connect_back=$XDEBUG_REMOTE_CONNECT_BACK" >> ${XDEBUG_INI}
RUN echo "xdebug.idekey=\"PHPSTORM\"" >> ${XDEBUG_INI}
RUN echo "xdebug.remote_handler=dbgp" >> ${XDEBUG_INI}
RUN echo "xdebug.remote_port=$XDEBUG_REMOTE_PORT" >> ${XDEBUG_INI}
RUN echo "xdebug.remote_host=$XDEBUG_REMOTE_HOST" >> ${XDEBUG_INI}
RUN echo "xdebug.remote_autostart=1" >> ${XDEBUG_INI}

# COMPOSER INSTALLATION
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# EXORT COMPOSER GLOBAL PATH
ENV PATH="$PATH:$HOME/.composer/vendor/bin" #edited
```


```
dataapi:
    container_name: dataapi
    build:
      context: ${DOCKER_FILES_PATH}/privateapi/
      args:
        - XDEBUG_REMOTE_PORT=10000
        - XDEBUG_REMOTE_HOST=172.17.0.1 #DOCKER network IP
        - XDEBUG_REMOTE_CONNECT_BACK=0  #edited
    command: sh -c "composer install && bin/console doctrine:migrations:migrate --allow-no-migration -n && php-fpm"
```


Here is the result of phpinto() in phpunit:
```
sodium

sodium support => enabled
libsodium headers version => 1.0.18
libsodium library version => 1.0.18

SPL

SPL support => enabled
Interfaces => OuterIterator, RecursiveIterator, SeekableIterator, SplObserver, SplSubject
Classes => AppendIterator, ArrayIterator, ArrayObject, BadFunctionCallException, BadMethodCallException, CachingIterator, CallbackFilterIterator, DirectoryIterator, DomainException, EmptyIterator, FilesystemIterator, FilterIterator, GlobIterator, InfiniteIterator, InvalidArgumentException, IteratorIterator, LengthException, LimitIterator, LogicException, MultipleIterator, NoRewindIterator, OutOfBoundsException, OutOfRangeException, OverflowException, ParentIterator, RangeException, RecursiveArrayIterator, RecursiveCachingIterator, RecursiveCallbackFilterIterator, RecursiveDirectoryIterator, RecursiveFilterIterator, RecursiveIteratorIterator, RecursiveRegexIterator, RecursiveTreeIterator, RegexIterator, RuntimeException, SplDoublyLinkedList, SplFileInfo, SplFileObject, SplFixedArray, SplHeap, SplMinHeap, SplMaxHeap, SplObjectStorage, SplPriorityQueue, SplQueue, SplStack, SplTempFileObject, UnderflowException, UnexpectedValueException

sqlite3

SQLite3 support => enabled
SQLite Library => 3.32.1

Directive => Local Value => Master Value
sqlite3.defensive => 1 => 1
sqlite3.extension_dir => no value => no value

standard

Dynamic Library Support => enabled
Path to sendmail => /usr/sbin/sendmail -t -i

Directive => Local Value => Master Value
assert.active => 1 => 1
assert.bail => 0 => 0
assert.callback => no value => no value
assert.exception => 0 => 0
assert.quiet_eval => 0 => 0
assert.warning => 1 => 1
auto_detect_line_endings => 0 => 0
default_socket_timeout => 60 => 60
from => no value => no value
session.trans_sid_hosts => no value => no value
session.trans_sid_tags => a=href,area=href,frame=src,form= => a=href,area=href,frame=src,form=
unserialize_max_depth => 4096 => 4096
url_rewriter.hosts => no value => no value
url_rewriter.tags => form= => form=
user_agent => no value => no value

tokenizer

Tokenizer Support => enabled

xdebug

xdebug support => enabled
Version => 2.9.6
Support Xdebug on Patreon, GitHub, or as a business: https://xdebug.org/support

Debugger => enabled
IDE Key => PHPSTORM

Directive => Local Value => Master Value
xdebug.auto_trace => Off => Off
xdebug.cli_color => 0 => 0
xdebug.collect_assignments => Off => Off
xdebug.collect_includes => On => On
xdebug.collect_params => 0 => 0
xdebug.collect_return => Off => Off
xdebug.collect_vars => Off => Off
xdebug.coverage_enable => On => On
xdebug.default_enable => On => On
xdebug.dump.COOKIE => no value => no value
xdebug.dump.ENV => no value => no value
xdebug.dump.FILES => no value => no value
xdebug.dump.GET => no value => no value
xdebug.dump.POST => no value => no value
xdebug.dump.REQUEST => no value => no value
xdebug.dump.SERVER => no value => no value
xdebug.dump.SESSION => no value => no value
xdebug.dump_globals => On => On
xdebug.dump_once => On => On
xdebug.dump_undefined => Off => Off
xdebug.file_link_format => no value => no value
xdebug.filename_format => no value => no value
xdebug.force_display_errors => Off => Off
xdebug.force_error_reporting => 0 => 0
xdebug.gc_stats_enable => Off => Off
xdebug.gc_stats_output_dir => /tmp => /tmp
xdebug.gc_stats_output_name => gcstats.%p => gcstats.%p
xdebug.halt_level => 0 => 0
xdebug.idekey => PHPSTORM => PHPSTORM
xdebug.max_nesting_level => 256 => 256
xdebug.max_stack_frames => -1 => -1
xdebug.overload_var_dump => 2 => 2
xdebug.profiler_append => Off => Off
xdebug.profiler_enable => On => On
xdebug.profiler_enable_trigger => Off => Off
xdebug.profiler_enable_trigger_value => no value => no value
xdebug.profiler_output_dir => /tmp => /tmp
xdebug.profiler_output_name => cachegrind.out.%p => cachegrind.out.%p
xdebug.remote_addr_header => no value => no value
xdebug.remote_autostart => On => On
xdebug.remote_connect_back => Off => Off
xdebug.remote_cookie_expire_time => 3600 => 3600
xdebug.remote_enable => On => On
xdebug.remote_host => 172.17.0.1 => 172.17.0.1
xdebug.remote_log => no value => no value
xdebug.remote_log_level => 7 => 7
xdebug.remote_mode => req => req
xdebug.remote_port => 10000 => 10000
xdebug.remote_timeout => 200 => 200
xdebug.scream => Off => Off
xdebug.show_error_trace => Off => Off
xdebug.show_exception_trace => Off => Off
xdebug.show_local_vars => Off => Off
xdebug.show_mem_delta => Off => Off
xdebug.trace_enable_trigger => Off => Off
xdebug.trace_enable_trigger_value => no value => no value
xdebug.trace_format => 0 => 0
xdebug.trace_options => 0 => 0
xdebug.trace_output_dir => /tmp => /tmp
xdebug.trace_output_name => trace.%c => trace.%c
xdebug.var_display_max_children => 128 => 128
xdebug.var_display_max_data => 512 => 512
xdebug.var_display_max_depth => 3 => 3

xml

XML Support => active
XML Namespace Support => active
libxml2 Version => 2.9.10

xmlreader

XMLReader => enabled

xmlwriter

XMLWriter => enabled

Zend OPcache

Opcode Caching => Disabled
Optimization => Disabled
SHM Cache => Enabled
File Cache => Disabled
Startup Failed => Opcode Caching is disabled for CLI

Directive => Local Value => Master Value
opcache.blacklist_filename => no value => no value
opcache.consistency_checks => 0 => 0
opcache.dups_fix => Off => Off
opcache.enable => On => On
opcache.enable_cli => Off => Off
opcache.enable_file_override => Off => Off
opcache.error_log => no value => no value
opcache.file_cache => no value => no value
opcache.file_cache_consistency_checks => On => On
opcache.file_cache_only => Off => Off
opcache.file_update_protection => 2 => 2
opcache.force_restart_timeout => 180 => 180
opcache.huge_code_pages => Off => Off
opcache.interned_strings_buffer => 8 => 8
opcache.lockfile_path => /tmp => /tmp
opcache.log_verbosity_level => 1 => 1
opcache.max_accelerated_files => 10000 => 10000
opcache.max_file_size => 0 => 0
opcache.max_wasted_percentage => 5 => 5
opcache.memory_consumption => 128 => 128
opcache.opt_debug_level => 0 => 0
opcache.optimization_level => 0 => 0x7FFEBFFF
opcache.preferred_memory_model => no value => no value
opcache.preload => no value => no value
opcache.preload_user => no value => no value
opcache.protect_memory => Off => Off
opcache.restrict_api => no value => no value
opcache.revalidate_freq => 2 => 2
opcache.revalidate_path => Off => Off
opcache.save_comments => On => On
opcache.use_cwd => On => On
opcache.validate_permission => Off => Off
opcache.validate_root => Off => Off
opcache.validate_timestamps => On => On

zip

Zip => enabled
Zip version => 1.15.6
Libzip headers version => 1.6.1
Libzip library version => 1.6.1

zlib

ZLib Support => enabled
Stream Wrapper => compress.zlib://
Stream Filter => zlib.inflate, zlib.deflate
Compiled Version => 1.2.11
Linked Version => 1.2.11

Directive => Local Value => Master Value
zlib.output_compression => Off => Off
zlib.output_compression_level => -1 => -1
zlib.output_handler => no value => no value

Additional Modules

Module Name

Environment

Variable => Value
PATH => /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/.composer/vendor/bin
HOSTNAME => fb6d372627df
JETBRAINS_REMOTE_RUN => 1
IDE_PHPUNIT_PHPUNIT_PHAR => /var/www/privateapi/vendor/bin/.phpunit/phpunit-7.5-0/phpunit
IDE_PHPUNIT_VERSION => 7.5.20
PHPIZE_DEPS => autoconf         dpkg-dev dpkg       file        g++         gcc         libc-dev        make        pkgconf         re2c
PHP_INI_DIR => /usr/local/etc/php
PHP_EXTRA_CONFIGURE_ARGS => --enable-fpm --with-fpm-user=www-data --with-fpm-group=www-data --disable-cgi
PHP_CFLAGS => -fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64
PHP_CPPFLAGS => -fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64
PHP_LDFLAGS => -Wl,-O1 -pie
GPG_KEYS => 42670A7FE4D0441C8E4632349E4FDC074A4EF02D 5A52880781F755608BF815FC910DEB46F53EA312
PHP_VERSION => 7.4.7
PHP_URL => https://www.php.net/distributions/php-7.4.7.tar.xz
PHP_ASC_URL => https://www.php.net/distributions/php-7.4.7.tar.xz.asc
PHP_SHA256 => 53558f8f24cd8ab6fa0ea252ca8198e2650160649681ce5230c1df1dc2b52faf
PHP_MD5 =>  
HOME => /root
APP_ENV => test
KERNEL_CLASS => App\Kernel
APP_AUTH_API_HOST => http://authapi
DATABASE_URL_TEST => postgresql://postgres:postgres@172.17.0.1:5433/privateapi_test
CORS_ALLOW_ORIGIN => ^https?://(localhost|127\.0\.0\.1)(:[0-9]+)?$

PHP Variables

Variable => Value
$_ENV['PATH'] => /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/.composer/vendor/bin
$_ENV['HOSTNAME'] => fb6d372627df
$_ENV['JETBRAINS_REMOTE_RUN'] => 1
$_ENV['IDE_PHPUNIT_PHPUNIT_PHAR'] => /var/www/privateapi/vendor/bin/.phpunit/phpunit-7.5-0/phpunit
$_ENV['IDE_PHPUNIT_VERSION'] => 7.5.20
$_ENV['PHPIZE_DEPS'] => autoconf        dpkg-dev dpkg       file        g++         gcc         libc-dev        make        pkgconf         re2c
$_ENV['PHP_INI_DIR'] => /usr/local/etc/php
$_ENV['PHP_EXTRA_CONFIGURE_ARGS'] => --enable-fpm --with-fpm-user=www-data --with-fpm-group=www-data --disable-cgi
$_ENV['PHP_CFLAGS'] => -fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64
$_ENV['PHP_CPPFLAGS'] => -fstack-protector-strong -fpic -fpie -O2 -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64
$_ENV['PHP_LDFLAGS'] => -Wl,-O1 -pie
$_ENV['GPG_KEYS'] => 42670A7FE4D0441C8E4632349E4FDC074A4EF02D 5A52880781F755608BF815FC910DEB46F53EA312
$_ENV['PHP_VERSION'] => 7.4.7
$_ENV['PHP_URL'] => https://www.php.net/distributions/php-7.4.7.tar.xz
$_ENV['PHP_ASC_URL'] => https://www.php.net/distributions/php-7.4.7.tar.xz.asc
$_ENV['PHP_SHA256'] => 53558f8f24cd8ab6fa0ea252ca8198e2650160649681ce5230c1df1dc2b52faf
$_ENV['PHP_MD5'] => 
$_ENV['HOME'] => /root
$_ENV['APP_ENV'] => test
$_ENV['KERNEL_CLASS'] => App\Kernel
$_ENV['APP_AUTH_API_HOST'] => http://authapi
$_ENV['DATABASE_URL_TEST'] => postgresql://postgres:postgres@172.17.0.1:5433/privateapi_test
$_ENV['CORS_ALLOW_ORIGIN'] => ^https?://(localhost|127\.0\.0\.1)(:[0-9]+)?$
$_ENV['APP_SECRET'] => $ecretf0rt3st
$_ENV['DATABASE_URL'] => 
$_ENV['SYMFONY_DOTENV_VARS'] => APP_SECRET,DATABASE_URL,SYMFONY_DEPRECATIONS_HELPER,PANTHER_APP_ENV
$_ENV['SYMFONY_DEPRECATIONS_HELPER'] => 999999
$_ENV['PANTHER_APP_ENV'] => panther
$_ENV['APP_DEBUG'] => 1
```

Set Up:
- - - - 


----------
        
## Answer \#0

**Accepted** Vote: 109

Created at 2020-07-27 14:17:52

------------

Your Xdebug settings look fine to me. It shows expected values for PHPUnit and it works for a web page debug.
This has to be the IDE settings / some IDE misconfiguration. In particular,  Even though you have specified one in your Run/Debug Configuration, PhpStorm still requires a project-default interpreter to be selected -- it's a know limitation: [WI-51570](https://youtrack.jetbrains.com/issue/WI-51570).
`Settings/Preferences | Languages & Frameworks | PHP | CLI Interpreter`
[](https://i.stack.imgur.com/zY6Wc.png)
 as of  version the PHP settings node has been moved to the top level and now it's `Settings/Preferences | PHP | CLI Interpreter`
[](https://i.stack.imgur.com/ZSwbi.png)
 You can also set it via `File | New Projects Settings | Settings for New Projects...`. This way it will be set for all  created on that computer (which you can then change as required on per project basis).


------------
    
    
## Answer \#1

 Vote: 8

Created at 2021-04-12 04:40:39

------------

I had this problem.
I fixed it by specifying the CLI Interpreter in Preferences > PHP
[](https://i.stack.imgur.com/LAHQn.png)


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-07-27 07:26:01

------------

This will not work in your docker since the steps will be removed when running the container:
```
RUN echo 'export PATH="$PATH:$HOME/.composer/vendor/bin"' >> ~/.bashrc
RUN source ~/.bashrc
```

you may use:
```
ENV PATH="$PATH:$HOME/.composer/vendor/bin"
```


You may also not using the `IP` in your config, instead you can map the port to your host when starting your container.
First change `XDEBUG_REMOTE_HOST=172.17.0.1` to `XDEBUG_REMOTE_HOST=0.0.0.0`


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-01-29 17:56:01

------------

I had this same issue. However it turned out in XDebug 3 the port changed to 9003.
Ctrl + Alt + S > Languages & Frameworks > PHP > Debug > Debug Port to 


------------
    
    