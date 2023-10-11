
# Post \#60841930 [Link](https://stackoverflow.com/questions/60841930/)

## Laravel 6-7 How Can I Override/Change a Vendor Class?

**Vote**: 6 (473/702) **Views**: 12997 (382/702) 

**Internal ID** \#2-7-449

Created at 2020-03-25 01:47:38

Tags: `php` `laravel` `composer-php` `laravel-6` `laravel-7`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I recently ran into a problem where I need to change one of Laravel's vendor files to get a desired result.
That file is `vendor/laravel/framework/Illuminate/Routing/CompileRouteCollection.php`.

Inside that file, a function needs to be changed.

```
protected function requestWithoutTrailingSlash(Request $request)
{
    $trimmedRequest = Request::createFromBase($request);

    $parts = explode('?', $request->server->get('REQUEST_URI'), 2);

    $trimmedRequest->server->set(
        'REQUEST_URI', rtrim($parts[0], '/').(isset($parts[1]) ? '?'.$parts[1] : '')
    );

    return $trimmedRequest;
}
```


More specifically, this part: `rtrim($parts[0], '/')`. That / at the end needs to be removed in order for parts of my routes to work.

I have tried creating my own custom class inside: `App\Helpers\CompiledRouteCollection.php`. In which I copy the function listed above and make my own changes.

App\Helpers\CompiledRouteCollection.php

```
namespace App\Helpers;

use Illuminate\Http\Request;

class CompiledRouteCollection extends \Illuminate\Routing\CompiledRouteCollection
{
    protected function requestWithoutTrailingSlash(Request $request)
    {
        $trimmedRequest = Request::createFromBase($request);

        $parts = explode('?', $request->server->get('REQUEST_URI'), 2);

        $trimmedRequest->server->set(
            'REQUEST_URI', rtrim($parts[0], '').(isset($parts[1]) ? '?'.$parts[1] : '')
        );

        return $trimmedRequest;
    }
}
```


Then I go into App\Providers\AppServiceProvider and I run this inside of the register function.

```
public function register()
{
    $loader = AliasLoader::getInstance();
    $loader->alias('App\Helpers\CompiledRouteCollection', 'Illuminate\Routing\CompiledRouteCollection');
}
```


But nothing happens.



I changed my alias to this:

```
$loader = AliasLoader::getInstance();
$loader->alias('Illuminate\Routing\CompiledRouteCollection', 'App\Helpers\CompiledRouteCollection');
```


But now in my Helper class it returns this error when trying to access any page:
`Class 'Illuminate\Routing\CompiledRouteCollection' not found`

To anyone who is interested in helping me out thank you so much. If you need more information please let me know!

If you need more context I opened up an issue on laravel/framework that has the full background explanation [here](https://github.com/laravel/framework/issues/32082)


----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2020-03-28 19:23:56

------------

I resulted to using composer to ovveride the file instead, as my previous method was not working whatsoever. Here is what I did.

Firstly, in the autoload section of the `composer.json` file I added this:

```
"exclude-from-classmap": [
    "vendor\\laravel\\framework\\src\\Illuminate\\Routing\\CompiledRouteCollection.php"
],
"psr-4": {
    "App\\": "app/",
    "Illuminate\\": "app/Overrides/"
},
```


Next I created the Ovverides folder in the app folder and then I copied the `CompiledRouteCollection.php` file and pasted it inside of the Ovverides folder.


------------
    
    
## Answer \#1

 Vote: 8

Created at 2020-03-25 07:02:33

------------

Edit `app\Providers\AppServiceProvider.php`.
The first argument of the `alias()` method must be a vendor class, and after is the overriding class:
```
public function register()
{
    $loader = AliasLoader::getInstance();
    $loader->alias(CompileRouteCollection::class, CompiledRouteCollection::class);
}
```

or
```
public function register()
{
    $loader = AliasLoader::getInstance();
    $loader->alias('Illuminate/Routing/CompileRouteCollection', 'App\Helpers\CompiledRouteCollection');
}
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-01-31 10:51:05

------------

You can override vendor classes in composer
you’ll need to edit your `composer.json` file and add two extra lines inside the autoload section to swap out the implementation.
```
"exclude-from-classmap": ["vendor/packagename/diraction/to/File.php"],
"files": ["app/Overrides/File.php"]
```

The first line will exclude the original class from being loaded, and the second class will point to the original class with the proper namespace for your implementation.
then you need to create `app/Overrides` folder and copy the File.php (file that you want to override) inside it.
and the last step is to run:
```
composer dump-autoload
```

And you’re good to go!


------------
    
    
## Answer \#3

 Vote: 0

Created at 2020-04-02 20:26:35

------------

Try swapping it out in your `RouteServiceProvider` rather than `AppServiceProvider` 

Just a guess, being as it is part of the routing system. It may have already been invoked as the original class before it ever hits `AppServiceProvider`. 

I looked at your issue at [https://github.com/laravel/framework/issues/32082](https://github.com/laravel/framework/issues/32082) and I'm surprised they didn't suggest trying it. If I remember the request lifetime of Laravel correctly, `RouteServiceProvider`  be your ticket.

I admire your resourcefulness in finding a workaround.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2020-10-30 06:55:57

------------

By my experience, you can do something like this:

1. add the following into register function in App\Providers\AppServiceProvider .


```
public function register()
{
    $loader = AliasLoader::getInstance();
    $loader->alias('Illuminate\Routing\CompileRouteCollection', 'App\Helpers\CompiledRouteCollection');
}
```


1. edit composer file like this.


```
"psr-4": {
    "App\\": "app/",
    "Illuminate\\Routing\\": "app\\Helpers\\"
},
```

And then please `compose install` and run the project.


------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-11-23 16:10:03

------------

Probably what you want to use is `$bindings` on `AppServiceProvider`.
[https://laravel.com/docs/9.x/providers#the-bindings-and-singletons-properties](https://laravel.com/docs/9.x/providers#the-bindings-and-singletons-properties)
In this case I wanted to override `$redirectTo` variable in the Login Controller of a package:
```
class AppServiceProvider extends ServiceProvider
{
    public $bindings = [
        \Vendor\Http\Controllers\Auth\LoginController::class => \App\Http\Controllers\Auth\LoginController::class,
        ...
```

```
use Vendor\Http\Controllers\Auth\LoginController as OriginalLoginController;

class LoginController extends OriginalLoginController
{
    protected $redirectTo = '/dashboard';
}
```



------------
    
    