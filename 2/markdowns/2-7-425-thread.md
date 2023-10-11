
# Post \#53755125 [Link](https://stackoverflow.com/questions/53755125/)

## Insert the current date time using Laravel

**Vote**: 14 (336/702) **Views**: 58033 (145/702) 

**Internal ID** \#2-7-425

Created at 2018-12-13 04:43:02

Tags: `php` `mysql` `laravel` `php-carbon`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I want to store the current date time in MySQL using the following Laravel function. Actually, I stored a static date. Instead of this, how can I store the current date time in the `created_at` and `updated_at` fields in the database?
```
function insert(Request $req)
{
    $name = $req->input('name');
    $address = $req->input('address');
    $data = array("name" => $name, "address" => $address, "created_at" => '2017-04-27 10:29:59', "updated_at" => '2017-04-27 10:29:59');
    DB::table('student')->insert($data);
    echo "Record inserted successfully.<br/>";
    return redirect('/');
}
```



----------
        
## Answer \#0

**Accepted** Vote: 25

Created at 2018-12-13 04:45:17

------------

Use the Laravel helper function
```
now()
```

Otherwise, use the `carbon` class:
```
Carbon\Carbon::now()
```

It is used like this:
```
$data = array("name" => $name,"address" => $address,"created_at"=> Carbon::now(),"updated_at"=> now());
DB::table('student')->insert($data);
```

For more information, see [now()](https://laravel.com/docs/5.7/helpers#method-now)


------------
    
    
## Answer \#1

 Vote: 3

Created at 2018-12-13 05:05:07

------------

You can also use this to get the current date time. It's working.
```
$current_date = date('Y-m-d H:i:s');
```

And also I have updated and set things in your function. You have to add this:
```
function insert(Request $req)
{
    $name = $req->input('name');
    $address = $req->input('address');

    $current_date_time = date('Y-m-d H:i:s');

    $data = array("name" => $name,
                  "address" => $address,
                  "created_at" => $current_date_time,
                  "updated_at" => $current_date_time);
    DB::table('student')->insert($data);
    echo "Record inserted successfully.<br/>";
    return redirect('/');
 }
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2018-12-13 06:07:13

------------

Use this in your database query:
```
'created_at' => Carbon::now(),
'updated_at' => Carbon::now()
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2018-12-13 04:48:50

------------

You can use the `DateTime` object.
Look at the below code:
```
$curTime = new \DateTime();
$created_at = $curTime->format("Y-m-d H:i:s");

$updateTime = new \DateTime();
$updated_at = $updateTime->format("Y-m-d H:i:s");
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-06-24 02:17:35

------------

Use
```
use Carbon\Carbon;
```

at the header of the controller. This code work for Laravel 9:
```
$updated = Carbon::now();
```

You may save it inside a variable and use the variable for inserting and updating statements or for any other purpose.
During , I defined the column  as datetime. And I used the following command for table creation.
```
php artisan migrate
```

It generates a timestamp-type column in MySQL. I am passing the variable $updated for update purposes. While using [phpMyAdmin](https://en.wikipedia.org/wiki/PhpMyAdmin), I can see the column  has data in date-time format.


------------
    
    