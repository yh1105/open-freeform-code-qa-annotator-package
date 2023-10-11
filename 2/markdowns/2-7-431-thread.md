
# Post \#74371406 [Link](https://stackoverflow.com/questions/74371406/)

## Laravel 8 dd() limitations

**Vote**: 3 (575/702) **Views**: 529 (696/702) 

**Internal ID** \#2-7-431

Created at 2022-11-09 07:44:40

Tags: `php` `laravel` `debugging`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

how can I increase the laravel 8  `dd()` limitations? I have lots of nested relationship and I cant view most of them due to many data. For example this
```
#relations: array:4 [▼
    "activecourses" => Illuminate\Database\Eloquent\Collection {#1346 ▼
      #items: array:3 [▼
        0 => App\Models\Studentcourse {#1353 ▶}
        1 => App\Models\Studentcourse {#1354 ▼
          #guarded: []
          #connection: "mysql"
          #table: "studentcourses"
          #primaryKey: "id"
          #keyType: "int"
          +incrementing: true
          #with: []
          #withCount: []
          +preventsLazyLoading: false
          #perPage: 15
          +exists: true
          +wasRecentlyCreated: false
          #escapeWhenCastingToString: false
          #attributes: array:22 [ …22]
          #original: array:22 [ …22]
          #changes: []
          #casts: []
          #classCastCache: []
          #attributeCastCache: []
          #dates: []
          #dateFormat: null
          #appends: []
          #dispatchesEvents: []
          #observables: []
          #relations: array:1 [ …1]
          #touches: []
          +timestamps: true
          #hidden: []
          #visible: []
          #fillable: []
        }
        2 => App\Models\Studentcourse {#1355 ▶}
      ]
      #escapeWhenCastingToString: false
    }
```

I found this link [Laravel dd function limitations](https://stackoverflow.com/questions/39011241/laravel-dd-function-limitations) but I think its outdated since the new dd function for laravel 8 is
this is from vendor folder.
```
use Symfony\Component\VarDumper\VarDumper;

if (!function_exists('dump')) {
    /**
     * @author Nicolas Grekas <p@tchwork.com>
     */
    function dump($var, ...$moreVars)
    {
        VarDumper::dump($var);

        foreach ($moreVars as $v) {
            VarDumper::dump($v);
        }

        if (1 < func_num_args()) {
            return func_get_args();
        }

        return $var;
    }
}

if (!function_exists('dd')) {
    /**
     * @return never
     */
    function dd(...$vars)
    {
        if (!in_array(\PHP_SAPI, ['cli', 'phpdbg'], true) && !headers_sent()) {
            header('HTTP/1.1 500 Internal Server Error');
        }

        foreach ($vars as $v) {
            VarDumper::dump($v);
        }

        exit(1);
    }
}
```

Tried adding this lines before `dd()` but doesnt work
```
ini_set('xdebug.var_display_max_depth', 100);
   ini_set('xdebug.var_display_max_children', 2000);
   ini_set('xdebug.var_display_max_data', 2000);
```

Is there any way or alternative in viewing nested data? I dont want to use `var_dump` or `print_r` since the browser becomes slow when there are lots of data. Thanks in advance.


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-11-13 03:21:07

------------

This question has already been answered here: [Laravel dd function limitations](https://stackoverflow.com/questions/39011241/laravel-dd-function-limitations) - EDIT: those still work but are not complete anymore (`VarDumper` overrides), and should not be used in production.

> The Dumper is using Symfony's VarCloner which is extending the AbstractCloner. This class has a $maxItems attribute set to 2500.
Basically, you need to override Laravel `Dumper` as explained in the post's answers. You can also create a new method like `ddd()` allowing larger outputs with a custom `Dumper`.
Note that a very large dataset can crash your browser tab/window as the DOM size will grow with it.

Alternatively, if you really need to view this data "live" (ex: an API route), you could return JSON and check them with Postman or Chrome debugger (network tab, parsed payload). They can parse and provide a "dynamic" data-view like Laravel `dump` does with HTML/CSS/JS.

For very large JSON, I would recommend:
- - [UltraEdit](https://ultraedit.com)`dd/dump`
:
I just tested ["Lucas Martin" solution](https://stackoverflow.com/a/55692349/5608694) on `Laravel 8.6.12` and it works as expected:
EDIT 2: works but is not complete, the dumper is more complex in 8.X than 5.x (`server` format). Logic is the same but provided the override is not complete anymore. I would only use it for this particular debug case. I would use this simple/old logic with a [custom method](https://stackoverflow.com/a/53512317/5608694).
Default (truncated):
[](https://i.stack.imgur.com/wTYnW.png)
`VarDumper` updated handler (full):
[](https://i.stack.imgur.com/0TTz0.png)
The limitation of this solution will be the browser. It'll crash with a huge dataset as DOM size becomes too much to store in memory. Also, I would only use it in development environments.
[Laravel debugbar](https://github.com/barryvdh/laravel-debugbar#usage) debug helper may be able to handle larger objects and dynamic view, but still limited to browser memory and DOM size.
credit: [random JSON generator](https://mockturtle.net/)


------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-11-12 20:42:50

------------

There are three ways I use the dd() method.

1. Printing to command line (either via written test or artisan tinker)
2. Printing to browser. You can do this if you're making use of Blade.
3. Printing to a Log file. Use the Log facade, for example Log::debug($activeForces)


Due to the large amount of data, it won't even be advisable to print out the content of your variable to screen. Try using the log file.
Alternatively, why don't you narrow down the content being die-dumped? For example: `dd($activeForces[0])`


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-11-13 15:21:49

------------

So based on what you want, as well as what I've used. Tracy might be a better tool than dd() [Visit their GitHub Here](https://github.com/recca0120/laravel-tracy)
This replaces the default debug system in Laravel with the Tracy PHP debugger created by the team at Nette.
This means instead of using `dd($myArray)` you would use `bdump($myArray,"Active Courses")` (The second Parameter is optional but useful when using multiple `bdumps()` on a single page so you know which is which)
This will also allow you to see the rendered page as well as the dump (And other useful features, such as the Route, Session and even the Laravel Terminal)


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-11-16 12:22:43

------------

In this case the many data is not due to what is under the model. Just dd() each of the models you want to see.
```
dd($variable['activecourses'][0]);
```

Either way your reason for not using dump (Browser getting slow) makes no sense. cause DD or anything else with interactive javascript and styling of the same data is always gonna be way slower. Increasing your execution time and memory limit for php is not gonna change that.
Another option is to dump the data to a file and open it with something efficient like Sublime.


------------
    
    