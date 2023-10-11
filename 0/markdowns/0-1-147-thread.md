
# Post \#68393652 [Link](https://stackoverflow.com/questions/68393652/)

## How to install bootstrap in laravel 8

**Vote**: 16 (306/702) **Views**: 42832 (185/702) 

**Internal ID** \#0-1-147

Created at 2021-07-15 12:15:51

Tags: `css` `laravel` `twitter-bootstrap` `laravel-8`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css` `php`)

----------

**Notepad**


----------

All of the info I can find online says that you should install bootstrap by running `composer require laravel/ui` and then `php artisan ui boostrap`. However, in Laravel 8, `laravel/ui` is deprecated. Is there a new/better way of installing bootstrap?


----------
        
## Answer \#0

**Accepted** Vote: 40

Created at 2021-07-15 12:38:35

------------

You can install bootstrap manually and compile sass.
First, add it to npm with compilers:
```
npm install bootstrap
npm install sass
npm install sass-loader
```

Second, create (if not created) resources/sass/app.scss and add this:
```
@import '~bootstrap';
```

Third, add compilation in webpack.mix.js:
```
mix.sass('resources/sass/app.scss', 'public/css')
```

Than, compile it with  - you see compiled file public/css/app.css
Now you can use it in templates with asset:
```
<link href="{{ asset('css/app.css') }}" rel="stylesheet">
```



------------
    
    
## Answer \#1

 Vote: 14

Created at 2021-07-15 12:30:47

------------

The way I do it is similar to what you mention, even in Laravel 8.
So I believe you are on the right path.
You can create a new Laravel project using this comand
```
composer create-project laravel/laravel --prefer-dist laravel-bootstrap
```

after, in laravel folder run
```
composer require laravel/ui
```

If you just want to add bootstrap to an existing project, just run:
```
php artisan ui bootstrap
npm install
npm run dev
```

And if you need Auth
```
php artisan ui bootstrap --auth
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2022-04-26 01:51:08

------------

 Install bootstrap manually

---


1). Download bootstrap's source files from bootstrap website: [https://getbootstrap.com/docs/5.1/getting-started/download/](https://getbootstrap.com/docs/5.1/getting-started/download/)
See this screenshot to download
[](https://i.stack.imgur.com/UwgbE.png)
2). Rename downloaded bootstrap folder and add it into the Public folder
[](https://i.stack.imgur.com/MfENu.png)
3). Load Bootstrap files in your file/view
[](https://i.stack.imgur.com/kdAFH.png)
```
<!doctype html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <!-- Bootstrap CSS -->
    <link href="{{asset('bootstrap/css/bootstrap.min.css')}}" rel="stylesheet">
       
    <!-- Bootstrap Bundle with Popper -->
    <script src="{{asset('bootstrap/js/bootstrap.bundle.min.js')}}"></script>

    <title>Hello, world!</title>
    <button class="btn btn-primary">Bootstrap btn</button>
  </head>
  <body>
    <h1>Hello, world!</h1>
  </body>
</html>
```


---


 Install bootstrap with package manager

---



```
composer create-project laravel/laravel --prefer-dist laravel-bootstrap
```


```
cd laravel-bootstrap
```


```
php artisan ui bootstrap
```


```
npm -v
```


```
npm install
```


For SCSS: Open resources/sass folder app.scss file and add the following lines
```
@import 'variables';
@import '~bootstrap/scss/bootstrap';
```

For CSS: Open resources/css folder app.css file and add the following lines
```
@import 'variables';
@import '~bootstrap/css/bootstrap';
```


```
for development: npm run dev
for production: npm run production
```


If you are lazy and don’t want to run the npm run dev command every time you make changes in SASS/CSS and JS file, you should use the following command.
```
npm run watch
```

After compile see this status in terminal
[](https://i.stack.imgur.com/AT3Mr.png)

```
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <title>{{ config('app.name', 'Laravel') }}</title>
    <!-- Scripts -->
    <script src="{{ asset('js/app.js') }}" defer></script>
    <!-- Styles -->
    <link href="{{ asset('css/app.css') }}" rel="stylesheet">
</head>
<body>
    <h1>Tutorial made by Positronx.io</h1>
</body>
</html>
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-07-10 13:40:38

------------


## composer require laravel/ui php artisan ui bootstrap npm install npm run dev


If you didn't see Laravel Mix compiled successfully, follow this Mix documentation page "https://laravel-mix.com/docs/6.0/installation"


------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-06-18 08:15:13

------------

Windows 10
Laravel v9.17.0
PHP v8.1.6
```
composer create-project laravel/laravel <project>
cd <project>
composer req laravel/ui
php artisan ui bootstrap --auth
npm install bootstrap
npm install sass
npm install sass-loader
npm install && npm run dev
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-09-26 18:01:44

------------

`composer req laravel/ui:*` will resolve the following problem
```
Problem 1
    - Root composer.json requires laravel/ui ^4.0 -> satisfiable by laravel/ui[v4.0.0, v4.0.1, v4.0.2, 4.x-dev].
    - laravel/ui[v4.0.0, ..., 4.x-dev] require illuminate/console ^9.21 -> found illuminate/console[v9.21.0, ..., 9.x-dev] but these were not loaded, likely because it conflicts with another require.
```

mentioned  problem will be occured when you have update the laravel and composer into latest version and you are tring to install a previous version there for you have to put following command 
```
composer req laravel/ui:*
php artisan ui bootstrap
npm install
npm install resolve-url-loader@^5.0.0 --save-dev --legacy-peer-deps
npm install sass-loader@^12.1.0 sass resolve-url-loader@^5.0.0 --save-dev --legacy-peer-deps
npm run dev
```



------------
    
    