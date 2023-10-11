
# Post \#58078757 [Link](https://stackoverflow.com/questions/58078757/)

## Class 'Illuminate\Support\Facades\Input' not found

**Vote**: 14 (336/702) **Views**: 53012 (157/702) 

**Internal ID** \#2-7-440

Created at 2019-09-24 10:56:08

Tags: `laravel` `class`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I have an error when upgrading laravel 6 

> Symfony \ Component \ Debug \ Exception \ FatalThrowableError
  (E_ERROR) Class 'Illuminate\Support\Facades\Input' not found



[](https://i.stack.imgur.com/HlQX4.jpg)



[](https://i.stack.imgur.com/MfrOw.jpg)

 


----------
        
## Answer \#0

**Accepted** Vote: 20

Created at 2019-09-24 11:31:18

------------

if you're using less version of 
In `config/app.php`, replace:
```
'Input' => Illuminate\Support\Facades\Input::class,
```

Or You can import `Input` facade directly as required,
```
use Illuminate\Support\Facades\Input;
```

In  Input:: is replaced with Request::
use
```
Request::
```

Add to the top of Controller or any other Class
```
use Illuminate\Http\Request;
```


```
$image_tmp = $request->image;
$fileName = time() . '.'.$image_tmp->clientExtension();
```


The `Input` facade, which was primarily a duplicate of the `Request` facade, has been removed. If you are using the `Input::get` method, you should now call the `Request::input` method. All other calls to the Input facade may simply be updated to use the `Request` facade.
You can directly use $request as well
```
$request->all();
```



------------
    
    
## Answer \#1

 Vote: 22

Created at 2019-12-31 09:35:14

------------

In `config/app.php`, replace:

```
'Input' => Illuminate\Support\Facades\Input::class
```


with

```
'Input' => Illuminate\Support\Facades\Request::class,
```



------------
    
    
## Answer \#2

 Vote: 6

Created at 2020-10-29 01:01:08

------------

I have resolved below it worked for me
: Access the link: 
: Create a file with the file name: 
: Paste the code below into the file you just created and save
```
<?php

namespace Illuminate\Support\Facades;

/**
 * @method static \Illuminate\Http\Request instance()
 * @method static string method()
 * @method static string root()
 * @method static string url()
 * @method static string fullUrl()
 * @method static string fullUrlWithQuery(array $query)
 * @method static string path()
 * @method static string decodedPath()
 * @method static string|null segment(int $index, string|null $default = null)
 * @method static array segments()
 * @method static bool is(...$patterns)
 * @method static bool routeIs(...$patterns)
 * @method static bool fullUrlIs(...$patterns)
 * @method static bool ajax()
 * @method static bool pjax()
 * @method static bool secure()
 * @method static string ip()
 * @method static array ips()
 * @method static string userAgent()
 * @method static \Illuminate\Http\Request merge(array $input)
 * @method static \Illuminate\Http\Request replace(array $input)
 * @method static \Symfony\Component\HttpFoundation\ParameterBag|mixed json(string $key = null, $default = null)
 * @method static \Illuminate\Session\Store session()
 * @method static \Illuminate\Session\Store|null getSession()
 * @method static void setLaravelSession(\Illuminate\Contracts\Session\Session $session)
 * @method static mixed user(string|null $guard = null)
 * @method static \Illuminate\Routing\Route|object|string route(string|null $param = null)
 * @method static string fingerprint()
 * @method static \Illuminate\Http\Request setJson(\Symfony\Component\HttpFoundation\ParameterBag $json)
 * @method static \Closure getUserResolver()
 * @method static \Illuminate\Http\Request setUserResolver(\Closure $callback)
 * @method static \Closure getRouteResolver()
 * @method static \Illuminate\Http\Request setRouteResolver(\Closure $callback)
 * @method static array toArray()
 * @method static bool offsetExists(string $offset)
 * @method static mixed offsetGet(string $offset)
 * @method static void offsetSet(string $offset, $value)
 * @method static void offsetUnset(string $offset)
 *
 * @see \Illuminate\Http\Request
 */
class Input extends Facade
{
    /**
     * Get an item from the input data.
     *
     * This method is used for all request verbs (GET, POST, PUT, and DELETE)
     *
     * @param  string  $key
     * @param  mixed   $default
     * @return mixed
     */
    public static function get($key = null, $default = null)
    {
        return static::$app['request']->input($key, $default);
    }

    /**
     * Get the registered name of the component.
     *
     * @return string
     */
    protected static function getFacadeAccessor()
    {
        return 'request';
    }
}
```

: Rerun your project
Done.Good luck!


------------
    
    
## Answer \#3

 Vote: 4

Created at 2019-09-24 11:00:19

------------

In  `Input::` is replaced with `Request::`

use

`Request::`

Add to the top of Controller or any other Class

`use Illuminate\Http\Request;`

Source: [https://stackoverflow.com/a/37203477/12089073](https://stackoverflow.com/a/37203477/12089073)


------------
    
    
## Answer \#4

 Vote: 3

Created at 2019-10-15 03:47:19

------------

Input no longer exists. Either use the Request facade or alias that instead of Input.
Kindly read this upgrade guide in Laravel 6 for more details. 
[https://laravel.com/docs/6.x/upgrade#the-input-facade](https://laravel.com/docs/6.x/upgrade#the-input-facade)


------------
    
    
## Answer \#5

 Vote: 3

Created at 2021-09-05 13:09:17

------------

You probably update your laravel package. And `Input::` is replaced in laravel 5.2 and above. You can replace your input in `config/app.php` with
`'Input' => Illuminate\Support\Facades\Input::class,`
and in your controller, you can reference it like this
```
use Illuminate\Support\Facades\Request as Input;
```



------------
    
    
## Answer \#6

 Vote: 2

Created at 2021-07-31 09:38:01

------------

The use of Input whether as namespace or in config/app.php became obsolete in Laravel 8.
You have to use Illuminate\Http\Request; in your namespace instead. And you get the Input from request. Below is the API.
```
Illuminate\Http\Request;

public function index(Request $request){
   $id = $request->Input('id');
}
```



------------
    
    
## Answer \#7

 Vote: 1

Created at 2020-01-28 12:23:18

------------

You can use `$request->all()` in place of `Input::all()`. It worked in my case.


------------
    
    
## Answer \#8

 Vote: 0

Created at 2019-09-24 11:57:01

------------

$image_tmp = $request->image;
$fileName = time() . '.'.$image_tmp->clientExtension()


------------
    
    
## Answer \#9

 Vote: 0

Created at 2019-09-24 12:58:30

------------

`use Input;` add to the top of your class.


------------
    
    
## Answer \#10

 Vote: 0

Created at 2019-12-21 18:19:45

------------

The very best way to fix this is to copy the  file which laravel provided [here](https://github.com/laravel/framework/blob/5.4/src/Illuminate/Support/Facades/Input.php)
and paste the file in your project directory.

Don't forget to add this to your controller 

> laravelproject\vendor\laravel\framework\src\Illuminate\Support\Facades


------------
    
    
## Answer \#11

 Vote: 0

Created at 2020-03-28 11:28:56

------------

You can use the global `request()` function e.g `request('key')` for accessing individual keys or
`request()->all()` to access all request data.


------------
    
    
## Answer \#12

 Vote: -1

Created at 2020-08-17 06:41:47

------------

```
use Illuminate\Support\Facades\Request;

Request::input();
```



------------
    
    