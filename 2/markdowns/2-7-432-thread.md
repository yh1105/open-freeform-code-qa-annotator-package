
# Post \#57094725 [Link](https://stackoverflow.com/questions/57094725/)

## Laravel 5.8 showing "419 Page Expired" after clicking logout from an already cleared session

**Vote**: 18 (275/702) **Views**: 28091 (241/702) 

**Internal ID** \#2-7-432

Created at 2019-07-18 12:41:58

Tags: `php` `laravel` `laravel-5` `laravel-5.8`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I run the  command and I will explain step by step what I do after that to understand the scenario,
- - - - - 
The thing is, these kinds of scenarios may arise, and I don't want to see this error message, just logout after clicking logout, even if the session is expired.



----------
        
## Answer \#0

**Accepted** Vote: 47

Created at 2019-07-18 12:57:45

------------

Well that's an obvious message you can maybe try to make a better layout for that page, but still it is good to show it so the user knows what happened. If you want to handle it differently you can try to redirect to the login page. 

So in your `app\Exceptions\Handler.php` file within the render method add this:

```
if ($exception instanceof \Illuminate\Session\TokenMismatchException) {
    return redirect()->route('login');
}
```



------------
    
    
## Answer \#1

 Vote: 9

Created at 2020-09-15 23:01:57

------------

A solution to the problem is relatively simple and requires a small addition to the
VerifyCsrfToken middleware;
```
use Closure;


    public function handle($request, Closure $next)
    {
        if(!Auth::check() && $request->route()->named('logout')) {
        
            $this->except[] = route('logout');
            
        }
        
        return parent::handle($request, $next);
    }
```

Usually, this file contains just an $except array of routes that should be ignored from csrf.
In this code, we override the handle method and perform two checks.
- - 
If both are true then we add 'logout' to the except array.  We then pass control to the core VerifyCsrfMiddleware which recognises the presence of the logout route in the array, and bypasses the check. The form data is correctly posted and we are redirected using the LogoutResponse.
The user sees no error page.
By checking in this way, we ensure that CSRF Token still protects genuine logout requests.


------------
    
    
## Answer \#2

 Vote: 4

Created at 2021-09-08 00:45:36

------------

It's never too late)
Add app/Http/Middleware/VerifyCsrfToken.php
```
use Closure;

    public function handle($request, Closure $next)
    {
        if($request->route()->named('logout')) {
            if (auth()->check()) {
                auth()->logout();
            }

            return redirect('/');
        }
    
        return parent::handle($request, $next);
    }
```

I think these are the best solutions.


------------
    
    
## Answer \#3

 Vote: 3

Created at 2019-07-18 13:08:01

------------

IMHO you can try to modify your `app/Http/Middleware/VerifyCsrfToken.php` file.
Edit the the  property with something like  this:

```
class VerifyCsrfToken extends Middleware
{       
    protected $except = [
        'http://example.com/logout',
    ];
```



------------
    
    
## Answer \#4

 Vote: 3

Created at 2020-03-27 11:49:22

------------

In a Laravel 6 project, I ended up modifying the `VerifyCsrfTokenMiddleware` as follows

As you will see, I simply added the `logout` named route to list of exclusion. 

I overridden the `__construct` function because we cannot use `route()` function when initializing a new variable

```
<?php

namespace App\Http\Middleware;

use Illuminate\Contracts\Encryption\Encrypter;
use Illuminate\Contracts\Foundation\Application;
use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as Middleware;

class VerifyCsrfToken extends Middleware
{
    /**
     * Indicates whether the XSRF-TOKEN cookie should be set on the response.
     *
     * @var bool
     */
    protected $addHttpCookie = true;

    /**
     * The URIs that should be excluded from CSRF verification.
     *
     * @var array
     */
    protected $except = [

    ];

    /**
     * Create a new middleware instance.
     *
     * @param  \Illuminate\Contracts\Foundation\Application  $app
     * @param  \Illuminate\Contracts\Encryption\Encrypter  $encrypter
     * @return void
     */
    public function __construct(Application $app, Encrypter $encrypter)
    {
        parent::__construct($app, $encrypter);
        $this->except = [
            route('logout')
        ];
    }
}
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2019-07-18 13:17:25

------------

```
<a href="{{ route('logout') }}" class="dropdown-item notify-item"="event.preventDefault(); document.getElementById('logout-form').submit();">
    <i class="fa fa-power-off"></i>  <span>{{ __('Logout') }} </span>
    </a>
<form id="logout-form" action="{{ route('logout') }}" method="POST" style="display: none;">
  @csrf
</form>
```





------------
    
    
## Answer \#6

 Vote: -1

Created at 2021-03-12 15:12:04

------------

You need to add the CSRF Token to your form:
```
<form action="{{ route('logout') }}" method="POST">
    @csrf
    <button type="submit" class="btn nav-link">Logout</button>
</form>
```

[Laravel 8.x Docs](https://laravel.com/docs/8.x/csrf)


------------
    
    