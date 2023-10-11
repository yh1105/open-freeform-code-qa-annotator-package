
# Post \#69842810 [Link](https://stackoverflow.com/questions/69842810/)

## How to pass an array to Laravel Route parameter

**Vote**: 1 (672/702) **Views**: 1542 (624/702) 

**Internal ID** \#2-7-443

Created at 2021-11-04 16:40:51

Tags: `laravel`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I wanted to pass a value of typed `array` to my route parameter, the array could be in any size and a different key-value pairs each time.
```
Route::get('/example/{array}', ...
```

So if I have an array like this:
```
$array = [
  'a' => 'one',
  'b' => 1,
  ...
]
```

I did this but knew already it ain't gonna work because it looks like I'm passing values to my route parameters named `a`, `b`, etc.
```
route('route.name', $array)
```

As expected the error says:
> ... [Missing parameter: array]
So I used the `serialize()`.
```
route('route.name', serialize($array))
```

I'm still getting an error, something like:
> [Missing parameter: s:1:"a";i:1;s:1:"b";i:2;]
What am I missing ? I also don't understand what the last error says.


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2021-11-04 16:46:54

------------

PHP have for this the http_build_query function.
```
$array = [
  'a' => 'one',
  'b' => 1,
];

$query = http_build_query(array('myArray' => $array));

// output: myArray%5Ba%5D=one&myArray%5Bb%5D=1
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2021-11-04 16:55:21

------------

When passing data to a route in Laravel you should make a practice of passing that data in an array like so:
Route:
```
Route::get('/example/{array}', ...
```

Calling the named route:
```
route('route.name', ['array' => serialize($array)])
```

I don't know if this formatting is required, but it 1. helps you to better format your routes when passing multiple values, and 2, makes your code more readable.
[Laravel Routing Documentation](https://laravel.com/docs/8.x/routing#generating-urls-to-named-routes)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-02-04 00:56:51

------------

I found the same problem, and from the tests I did, it seems to be an incompatibility between php and Laravel.
What happens is that php `serialize()` (and also php `json_encode()`) use the character «{». This character seems to confuse Laravel router, so the error message.
I have tried to use php `htmlspecialchars(serialize($array))` (and other combinations like `htmlentities(json_encode($array))`) but the problem is that «{» is a normal character so they do not transform it (so continues confusing the Laravel router).
I also tried the solution of [Maik Lowrey](https://stackoverflow.com/users/14807111/maik-lowrey), but then I do not see an out of the box method to recover the array from the serialized parameter on the other side of the route (`urldecode()` does nothing).
At last I have used the following ugly turnaround that only works for one-dimension arrays (but works):
In the blade route generation:
```
['arrayParameter' => trim(json_encode($array), '{}')]
```

In the Controller function:
```
$array = json_decode('{' . $arrayParameter . '}', true);
```

Best regards.


------------
    
    