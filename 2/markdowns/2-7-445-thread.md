
# Post \#64828762 [Link](https://stackoverflow.com/questions/64828762/)

## Getting current page url on wp-admin/admin dashboard?

**Vote**: 4 (532/702) **Views**: 7836 (467/702) 

**Internal ID** \#2-7-445

Created at 2020-11-13 22:20:10

Tags: `php` `wordpress` `query-variables`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

Im trying to get the current page url while on the wp-admin/admin dashboard, is it possible?
Im trying to use these codes but i can't seem to get it to work.
```
global $wp;
$current_page = add_query_arg( $wp->query_vars, home_url( $wp->request ) );
```

The output i wanted is like this: `https://example.com/1/wp-admin/admin.php?page=test`
But instead, the output turns like this: `/1/wp-admin/admin.php?page=test&=https://example.com/1`
Any help is greatly appreciated


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2020-11-14 00:49:36

------------

To retrieve the URL of the admin area of your wordpress site, you can use `admin_url()`: [https://developer.wordpress.org/reference/functions/admin_url/](https://developer.wordpress.org/reference/functions/admin_url/)
It accepts a path. So you could do `admin_url('admin.php?page=test');` if you like to hard code. But you want to have it dynamically I guess.
I assume, your page is part of the admin menu. Every menu page automatically has a page parameter in the URL like `page=test`.
You want to get the URL of the current page you are looking at, so you can get the page parameter from the URL using `$_GET`.
Putting all this information together, your code can look like:
`$current_page = admin_url( "admin.php?page=".$_GET["page"] );`


------------
    
    
## Answer \#1

 Vote: 4

Created at 2021-09-23 06:25:38

------------

I've found a very handfull solution using [http_build_query()](https://www.php.net/manual/en/function.http-build-query.php) function :
```
$current_page = admin_url(sprintf('admin.php?%s', http_build_query($_GET)));
```

`http_build_query()` will automatically transforms all `$_GET` params into a query string which can be append to the base admin URL using `sprintf()`.
However this does not support pages with a different name (eg: `edit.php` instead of `admin.php`). You may use something like this instead, as a helper function, to support every admin pages :
```
/**
 * Get the base URL of the current admin page, with query params.
 *
 * @return string
 */
function get_current_admin_url(): string
{
    return admin_url(sprintf(basename($_SERVER['REQUEST_URI'])));
}

# on your admin page..
$current_page = get_current_admin_url();
```

Beware of the use of this function, URLs can be manipulated by the end user /!\


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-12-15 13:36:14

------------

You can use the global `$pagenow` or `$typenow` to get the current admin page.
For example, if you want to target only `edit.php?post_type=page`, you can use the following :
```
global $pagenow, $typenow;
  if( ! is_admin() || 'edit.php' !== $pagenow || 'page' !== $typenow ) return;
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-07-27 16:25:48

------------

Just use `add_query_arg()` function that automatically detects current page URL (relative) - [https://developer.wordpress.org/reference/functions/add_query_arg/](https://developer.wordpress.org/reference/functions/add_query_arg/)


------------
    
    