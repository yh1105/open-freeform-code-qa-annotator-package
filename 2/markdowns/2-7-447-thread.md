
# Post \#71573457 [Link](https://stackoverflow.com/questions/71573457/)

## submit multiple arrays from html and combine their attribute into seperate collections and store in Laravel App

**Vote**: 1 (672/702) **Views**: 758 (681/702) 

**Internal ID** \#2-7-447

Created at 2022-03-22 14:08:00

Tags: `arrays` `laravel` `forms` `eloquent`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I submitting the arrays value from the html and recieving them into the Laravel controller, the problem is I have multiple arrays with seprate attributes arrays. I want to store the info into the database as a collection. Let me explain with example
HTML CODE
```
<input type="text" name="tw_name[]" placeholder="name" value="{{ old('tw_name') }}">
<input type="text" name="tw_price[]"  placeholder="price" value="{{ old('tw_price') }}">
```

Now I am receving the tw_name and tw_prices arrays in the controller, but I want to store them into the database using model like below
```
Bet::create([
  'name' => 'first_name',
  'price' => '10',
]);

Bet::create([
  'name' => 'second_name',
  'price' => '20',
]);
```

User can add 12 forms, so we can't say that user can submit 3 form or 5 form or 10. So we need to handle the same form and save them into database.
if the user will submit 3 form it we will recive 3 arrays in controller
```
Bet::create([
  'name' => 'first_name',
  'price' => '10',
]);

Bet::create([
  'name' => 'second_name',
  'price' => '20',
]);
Bet::create([
  'name' => 'thir_name',
  'price' => '30',
]);
```

How can I save them into database, Thank you


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2022-03-22 14:24:37

------------

A single `for()` or `foreach()` loop can be used to save these to the database:
`for` method:
```
for($i = 0; $i < count($request->input('tw_name')); $i++) {
  Bet::create([
    'name' => $request->input('tw_name')[$i],
    'price' => $request->input('tw_price')[$i]
  ]);
}
```

`foreach` method:
```
foreach($request->input('tw_name') as $index => $twName) {
  Bet::create([
    'name' => $twName,
    'price' => $request->input('tw_price')[$index]
  ]);
}
```

As long as both `tw_name[]` and `tw_price[]` arrays are the same length, this code will create a `Bet` instance for each pair of uploaded values.


------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-03-22 14:27:23

------------

I would suggest you use a form input like below
```
<input type="text" name="request[0][name]" placeholder="name" value="">
<input type="text" name="request[0][price]"  placeholder="price" value="">

<input type="text" name="request[1][name]" placeholder="name" value="">
<input type="text" name="request[1][price]"  placeholder="price" value="">


<input type="text" name="request[2][name]" placeholder="name" value="">
<input type="text" name="request[2][price]"  placeholder="price" value="">
```

so your request data like below
```
array:2 [▼
  "_token" => "AXp1HzsVQzGJGCcnemS0PEdpi1aAMU3g6NK5zViB"
  "request" => array:3 [▼
    0 => array:2 [▼
      "name" => "s1"
      "price" => "10"
    ]
    1 => array:2 [▼
      "name" => "s2"
      "price" => "20"
    ]
    2 => array:2 [▼
      "name" => "s3"
      "price" => "30"
    ]
  ]
]
```

so you can save data like below
```
Bet::insert($request->request);
```

or
```
foreach ($request->request as $value){
            Bet::create([
                'name' =>$value['name'],
                'price' =>$value['price']
            ]);
        }
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-03-22 14:37:15

------------

Try this:
```
<input type="text" name="tw_form[name][]" placeholder="name" value="{{ old('tw_name') }}">
<input type="text" name="tw_form[price][]"  placeholder="price" value="{{ old('tw_price') }}">
```

see the structure of array: `tw_form[name][]`
And in the controller:
Raw data:
```
array:1 [
  "tw_form" => array:2 [
    "name" => array:2 [
      0 => "a"
      1 => "b"
    ]
    "price" => array:2 [
      0 => "1"
      1 => "2"
    ]
  ]
]
```

```
// for save formated data for save
        $create = [];

        // format raw data
        foreach ($request->input('tw_form') as $key => $item) {
            foreach ($item as $value_key => $value) {
                $create[$value_key][$key] =  $value;
            }
        }
```

The result is this:
```
array:2 [
  0 => array:2 [
    "name" => "a"
    "price" => "1"
  ]
  1 => array:2 [
    "name" => "b"
    "price" => "2"
  ]
]
```

now only insert:
```
foreach ($create as $item){
            Bet::create($item);
        }
```

If you build the html with javascript dynamically, it can be simplified more because simply for each array in the html you already assign the key `form[i][name]` and `form[i][price]`, that way you simply iterate form in the controller and you already have the variables grouped


------------
    
    