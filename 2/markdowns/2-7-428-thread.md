
# Post \#62182092 [Link](https://stackoverflow.com/questions/62182092/)

## Laravel Sanctum Token API Authentication Not Working in Postman

**Vote**: 2 (631/702) **Views**: 8895 (433/702) 

**Internal ID** \#2-7-428

Created at 2020-06-03 20:22:47

Tags: `php` `laravel` `authentication` `laravel-sanctum`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I am trying to utilize Sanctum for an API only application. I am not using it for a SPA. I have a single end point set up and protected by the Sanctum middleware. I am creating a user and a token for said user through the tinker cli tool. I then paste the token into Postman under the bearer token selection within the authorization tab. However, when I submit the request I get an unauthenticated error. Not quite sure what I am doing incorrect here. Followed the documentation provided very closely as well as the sparse videos I could find. Here are some code snippets. I appreciate the insight here.

API.php

```
Route::middleware('auth:sanctum')->apiResource('/documents','DocumentHandlerController');
```


Middleware/Authenticate.php

```
class Authenticate extends Middleware
{
    /**
     * Return 401 when not authorized
     *
     * @param  \Illuminate\Http\Request  $request
     * @return string|null
     */
    public function handle($request)
    {
        return response()->json(
            ['message'=>'Unauthorized']
            ,Response::HTTP_UNAUTHORIZED
        );
    }
}
```


Function from my controller

```
public function index()
    {
        return response()->json(['Success'],Response::HTTP_OK);
    }
```



----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2021-12-15 21:30:36

------------

I had a similar issue where I got the response `{"message":"Unauthenticated."}`.
For me it was because in my `.env` file, my APP_URL was `https://www.example.com` and I was trying to access the API via the URL `https://example.com`.
When I changed the URL that made the API call to `https://www.example.com` it was working.


------------
    
    
## Answer \#1

 Vote: 6

Created at 2021-08-12 12:50:33

------------

I faced the same Issue but I solved it by add to headers: `Accept with value application/json`.
That's the best solution I found maybe others have another solution.
[](https://i.stack.imgur.com/J4DGw.png)


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-04-12 08:50:25

------------

Faced with the same problem. After some investigation it seems the problem happens there:
[https://github.com/laravel/sanctum/commit/f5695aecc547138c76bc66aaede73ba549dabdc5](https://github.com/laravel/sanctum/commit/f5695aecc547138c76bc66aaede73ba549dabdc5)
During the refactoring they forgot to include default guard definition.
Just add this at the end of config/sanctum.php for api guard:
```
'guard' =>  'api'
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-02-10 20:13:27

------------

I killed full day when trying to solve this problem. In my case, the problem was in the Apache config. I hope this helps you.
```
RewriteCond %{HTTP:Authorization} ^(.+)$
RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-04-09 07:33:39

------------

For some reason  `auth:sanctum` doesn't work for API authentication. Same problem happened with me then i saw this issue on [github](https://github.com/nWidart/laravel-modules/issues/1177) .  i see two solutions at this point

1. jwt-auth
2. API Authentication




------------
    
    