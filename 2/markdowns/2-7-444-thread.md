
# Post \#57564792 [Link](https://stackoverflow.com/questions/57564792/)

## Laravel Spatie Roles get current users role

**Vote**: 10 (387/702) **Views**: 46585 (174/702) 

**Internal ID** \#2-7-444

Created at 2019-08-19 22:50:54

Tags: `laravel` `laravel-5`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

Using laravels Spatie/Roles is there a way to get the current users role who is logged in?

I've tried

```
if($this->hasRole('admin')) { //do something }
```


but it wont work


----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2019-08-19 22:59:07

------------

Figured it out, for those of you out there this is how you do it.

```
if(Auth::user()->hasRole('my_role_name'))
{
    // do something
}
  //otherwise do something else
```



------------
    
    
## Answer \#1

 Vote: 21

Created at 2019-08-20 01:15:47

------------

In your Model that uses the [Spatie\Permission\Traits\HasRoles](https://github.com/spatie/laravel-permission/blob/master/src/Traits/HasRoles.php) trait, which is in most cases `User`, you can call various methods for verifying or retrieving roles.
They are:
- `roles`- `hasRole`- `hasAnyRole`- `hasAllRoles`- `getRoleNames`

## Usage example


For a given user,
```
$roles = $user->getRoleNames();// Returns a collection
```

[Reference here](https://spatie.be/docs/laravel-permission/v5/basic-usage/basic-usage).


------------
    
    
## Answer \#2

 Vote: 13

Created at 2020-06-15 08:01:07

------------

```
{{ Auth::user()->roles->pluck('name') }}
```



------------
    
    
## Answer \#3

 Vote: 6

Created at 2021-12-03 09:00:10

------------

If you know that the user has only one role (for example: "customer") then you can have this displayed as follows:
```
{{ Auth::user()->roles->pluck('name')[0] ?? '' }}
// or with the auth() helper:
{{ auth()->user()->roles->pluck('name')[0] ?? '' }}

// output: "customer"
```

Then you can check in your blade File:
```
@if ( auth()->user()->roles->pluck('name')[0] ?? '' === 'customer' )
   <h1>Hello Customer {{auth()->user()->name }}</h1>
    ...
@endif
```

Or even much simpler:
```
@role('customer')
    I am a Customer!
@else
    I am not a Customer ...
@endrole
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2022-10-04 03:47:13

------------

Using Laravel 9 and Spatie v5 you can use
Controller
```
if(auth()->user()->hasRole('name')){}
```

Blade
```
@role('name')
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-11-17 20:05:51

------------

Using laravel8 and spattie  5.5
If users ; you can use:
> auth()->user()->roles->pluck("id")->first()
to get the role id for the current logged user,
optionally can use:
> auth()->user()->roles->pluck("name")->first();
to get the role's name


------------
    
    
## Answer \#6

 Vote: -1

Created at 2022-01-21 19:14:01

------------

Auth::user()->getRoleNames();
This will return a collection with all the roles the user has attached to.


------------
    
    