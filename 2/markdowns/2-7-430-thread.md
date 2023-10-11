
# Post \#62485384 [Link](https://stackoverflow.com/questions/62485384/)

## Symfony 5 & EasyAdmin 3.0 - /admin route not found

**Vote**: 12 (356/702) **Views**: 13860 (373/702) 

**Internal ID** \#2-7-430

Created at 2020-06-20 11:46:29

Tags: `php` `symfony` `symfony5` `easyadmin`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I have fresh Symfony 5 project instlled locally, and added Easy Admin trought Symfony CLI:
`symfony composer req admin`
I should have `/admin` route but it's missing
I run:
`symfony console cache:clear`
`symfony composer dump-autoload`
`rm -rf var/cache/*`
```
symfony console debug:router
 -------------------------- -------- -------- ------ ----------------------------------- 
  Name                       Method   Scheme   Host   Path                               
 -------------------------- -------- -------- ------ ----------------------------------- 
  _preview_error             ANY      ANY      ANY    /_error/{code}.{_format}           
  _wdt                       ANY      ANY      ANY    /_wdt/{token}                      
  _profiler_home             ANY      ANY      ANY    /_profiler/                        
  _profiler_search           ANY      ANY      ANY    /_profiler/search                  
  _profiler_search_bar       ANY      ANY      ANY    /_profiler/search_bar              
  _profiler_phpinfo          ANY      ANY      ANY    /_profiler/phpinfo                 
  _profiler_search_results   ANY      ANY      ANY    /_profiler/{token}/search/results  
  _profiler_open_file        ANY      ANY      ANY    /_profiler/open                    
  _profiler                  ANY      ANY      ANY    /_profiler/{token}                 
  _profiler_router           ANY      ANY      ANY    /_profiler/{token}/router          
  _profiler_exception        ANY      ANY      ANY    /_profiler/{token}/exception       
  _profiler_exception_css    ANY      ANY      ANY    /_profiler/{token}/exception.css   
  homepage                   ANY      ANY      ANY    /                                  
 -------------------------- -------- -------- ------ -----------------------------------
```

```
// config/routes/easy_admin.yaml

easy_admin_bundle:
    resource: '@EasyAdminBundle/Controller/EasyAdminController.php'
    prefix: /admin
    type: annotation
```

```
symfony console router:match /admin

                                                                                                                       
 [ERROR] None of the routes match the path "/admin"
```

What am I missing?


----------
        
## Answer \#0

**Accepted** Vote: 14

Created at 2020-06-21 17:08:33

------------

You need to create at least one dashboard. Try:
```
php bin/console make:admin:dashboard
```

Next, you can create a CrudController with:
```
php bin/console make:admin:crud
```

[https://symfony.com/doc/master/bundles/EasyAdminBundle/dashboards.html](https://symfony.com/doc/master/bundles/EasyAdminBundle/dashboards.html)


------------
    
    
## Answer \#1

 Vote: 5

Created at 2020-06-27 11:31:53

------------

Yeah, I am reading this book right now and ran into the same issue.
First, make sure that your working directory is clean (run "" and remove all changes made by EasyAdminBundle setup).
Then run:
```
composer require easycorp/easyadmin-bundle:2.*
```

to install EasyAdminBundle version 2; with this version, you can proceed as described in the book.


------------
    
    
## Answer \#2

 Vote: 5

Created at 2020-10-01 11:26:26

------------

As others have already said you need to create a dashboard with:
`php bin/console make:admin:dashboard`
And then create at least one crud controller. Since it seems you're using the Fast Track book, you need to type the following command twice and make one for both Comment and Conference:
`php bin/console make:admin:crud`
I am using the Fast Track book too and this is how my files ended up. I am still learning easy admin3 and thus make no claims as to best practice, but this should make the menus look as they do in the Fast Track screenshots:
src/Controller/Admin/DashboardController.php:
```
/**
 * @Route("/admin", name="admin")
 */
public function index(): Response
{
    $routeBuilder = $this->get(CrudUrlGenerator::class)->build();

    return $this->redirect($routeBuilder->setController(ConferenceCrudController::class)->generateUrl());
}

public function configureDashboard(): Dashboard
{
    return Dashboard::new()
        ->setTitle('Guestbook');
}

public function configureMenuItems(): iterable
{
    yield MenuItem::linktoRoute('Back to website', 'fa fa-home', 'homepage');
    yield MenuItem::linkToCrud('Conference', 'fa fa-map-marker', Conference::class);
    yield MenuItem::linkToCrud('Comment', 'fa fa-comment', Comment::class);
}
```

src/Controller/Admin/CommentCrudController.php:
```
public static function getEntityFqcn(): string
{
    return Comment::class;
}

public function configureFields(string $pageName): iterable
{
    return [
        IdField::new('id')->hideOnForm(),
        TextField::new('author'),
        TextareaField::new('text')->hideOnIndex(), // Removing ->hideOnIndex() will display a link to a text modal
        EmailField::new('email'),
        DateTimeField::new('createdAt')->hideOnForm(),
        ImageField::new('photoFilename', 'Photo')->setBasePath('/uploads/photos')->hideOnForm(),

        AssociationField::new('conference')
    ];
}
```

src/Controller/Admin/ConferenceCrudController.php
```
public static function getEntityFqcn(): string
{
    return Conference::class;
}

public function configureFields(string $pageName): iterable
{
    return [
        IdField::new('id')->hideOnForm(),
        TextField::new('city'),
        TextField::new('year'),
        BooleanField::new('isInternational'),
        IntegerField::new('commentCount', 'Comments')->hideOnForm()
    ];
}
```

And in src/Entity/Conference.php I added the following to make `commentCount` available:
```
public function getCommentCount(): int
{
    return $this->comments->count();
}
```

To generate the createdAt datetime automatically when the comment is submitted, I first installed the following bundle:
```
$ composer require stof/doctrine-extensions-bundle
```

And then modified config/packages/stof_doctrine_extensions.yaml:
```
stof_doctrine_extensions:
    default_locale: en_US
    orm:
        default:
            tree: true
            timestampable: true
```

And finally decorated `private $createdAt` in src/Entity/Comment.php with the following:
```
/**
 * @var \DateTime
 * @ORM\Column(type="datetime")
 * @Gedmo\Mapping\Annotation\Timestampable(on="create")
 * @Doctrine\ORM\Mapping\Column(type="datetime")
 */
private $createdAt;
```



------------
    
    
## Answer \#3

 Vote: 3

Created at 2020-06-20 13:41:14

------------

EasyAdminBundle v3 have another configuration and you no longer need to use `EasyAdminController` resource.
You can find more information about it here
[https://github.com/EasyCorp/EasyAdminBundle/blob/master/src/Controller/EasyAdminController.php](https://github.com/EasyCorp/EasyAdminBundle/blob/master/src/Controller/EasyAdminController.php)
and here
[https://symfony.com/doc/master/bundles/EasyAdminBundle/dashboards.html](https://symfony.com/doc/master/bundles/EasyAdminBundle/dashboards.html)


------------
    
    
## Answer \#4

 Vote: 3

Created at 2020-08-19 14:28:33

------------

When migrating from easyadmin 2 to 3, it appears that the route name is not preserved.  One way to do this is in DashboardController, add
```
/**
 * @Route("/admin", name="easyadmin")
 */
public function index(): Response
{
    return parent::index();
}
```



------------
    
    
## Answer \#5

 Vote: 3

Created at 2020-09-16 12:20:34

------------

I solved this problem as follows:

1. Remove EasyAdmin 3


```
composer remove admin
```


1. Install additional package.


```
composer require "easycorp/easyadmin-bundle":"^2.3"
```


1. Update packages.


```
composer update
```



------------
    
    
## Answer \#6

 Vote: 1

Created at 2020-09-23 15:45:28

------------

In my case
first
```
composer remove doctrine/common
```

and then
```
composer require easycorp/easyadmin-bundle v2.3.9 doctrine/common v2.13.3 doctrine/persistence v1.3.8
```

With that it worked for the book


------------
    
    
## Answer \#7

 Vote: 0

Created at 2020-10-25 23:48:10

------------

For me, the clearest and most complete explanation is the answer to @AnnaHowell
I would only change a part of your code.
In p:
```
public function configureFields(string $pageName): iterable
{
    $avatar = ImageField::new('photoFilename')->setBasePath('uploads/photos/')->setLabel('Photo');
    $avatarTextFile = TextField::new('photoFilename');
   
     {
        yield     TextField::new('author');
        yield     TextEditorField::new('text');
        yield     TextField::new('state');
        yield     EmailField::new('email');
        yield     DateTimeField::new('createdAt', 'Created')->setFormat('dd-MM-y HH:mm:ss')
                ->setSortable(true)->setFormTypeOption('disabled','disabled');
        if (Crud::PAGE_INDEX === $pageName) {
        yield ImageField::new('photoFilename')->setBasePath('uploads/photos/')->setLabel('Photo');
    } elseif (Crud::PAGE_EDIT === $pageName) {
       yield TextField::new('photoFilename')->setLabel('Photo');
    }      
       
};
```

Thus, we allow the Administrator to not only evaluate the text, but also the relevance of the photo.
What if a comment has great text, but an inconvenient or trivial photo?
The Administrator could delete the name of the photo (and that way it would not be visible), leave the text comment and publish it.


------------
    
    
## Answer \#8

 Vote: 0

Created at 2020-10-31 01:28:27

------------

Simply copy all of `/vendor/easycorp/easyadmin-bundle/src/Resources/public` to `public/bundles/easyadmin`


------------
    
    
## Answer \#9

 Vote: 0

Created at 2021-02-26 07:56:02

------------


## Short answer:


Make sure you are using HTTPS to access the admin route. Even if the admin route is supposed to use any scheme in the debug:router command.

## Detailed answer


Same situation, reproducible with Symfony 4.4 and Symfony 5.2 and Easyadmin 3.2
I found out that I was accessing my server with http (WAMP):

| URL | Result | 
| --- | ------ | 
| http://localhost | 200 | 
| http://localhost/admin | 404 | 


Then I tried with
```
symfony console server:start
```

Noticed the warning about installing the certificate, installed it and ran again the symfony server.
After than I was able to use HTTPS, and the admin was accessible on https://localhost/admin


------------
    
    
## Answer \#10

 Vote: 0

Created at 2022-11-30 21:07:49

------------

I was testing - easyAdmin4, with Symfony 6, with Lando backend, in Ubuntu.
No matter what I try, not only `/admin` routing, any of the controller was not working.
Make sure, if the issue is with  or with controllers.
The controller issue is resolved after
```
composer require symfony/apache-pack
```

From this [post](https://stackoverflow.com/a/60539467/15342125)
Also, you can test `/admin` controller with
returning

1. Twig Template (By Uncommenting) return $this->render('template1.html.twig'); and the template file will be present inside, templates/template1.html.twig
2. Simple Response useing class Symfony\Component\HttpFoundation\Response;
3. Or JSON data useing Symfony\Component\HttpFoundation\JsonResponse;


-- By default the `/admin` route will open default template `return parent::index();`
The Only solution seems to copy - `.htaccess` file inside `public` directory
```
# Use the front controller as index file. It serves as a fallback solution when
# every other rewrite/redirect fails (e.g. in an aliased environment without
# mod_rewrite). Additionally, this reduces the matching process for the
# start page (path "/") because otherwise Apache will apply the rewriting rules
# to each configured DirectoryIndex file (e.g. index.php, index.html, index.pl).
DirectoryIndex index.php

# By default, Apache does not evaluate symbolic links if you did not enable this
# feature in your server configuration. Uncomment the following line if you
# install assets as symlinks or if you experience problems related to symlinks
# when compiling LESS/Sass/CoffeScript assets.
# Options +FollowSymlinks

# Disabling MultiViews prevents unwanted negotiation, e.g. "/index" should not resolve
# to the front controller "/index.php" but be rewritten to "/index.php/index".
<IfModule mod_negotiation.c>
    Options -MultiViews
</IfModule>

<IfModule mod_rewrite.c>
    RewriteEngine On

    # Determine the RewriteBase automatically and set it as environment variable.
    # If you are using Apache aliases to do mass virtual hosting or installed the
    # project in a subdirectory, the base path will be prepended to allow proper
    # resolution of the index.php file and to redirect to the correct URI. It will
    # work in environments without path prefix as well, providing a safe, one-size
    # fits all solution. But as you do not need it in this case, you can comment
    # the following 2 lines to eliminate the overhead.
    RewriteCond %{REQUEST_URI}::$0 ^(/.+)/(.*)::\2$
    RewriteRule .* - [E=BASE:%1]

    # Sets the HTTP_AUTHORIZATION header removed by Apache
    RewriteCond %{HTTP:Authorization} .+
    RewriteRule ^ - [E=HTTP_AUTHORIZATION:%0]

    # Redirect to URI without front controller to prevent duplicate content
    # (with and without `/index.php`). Only do this redirect on the initial
    # rewrite by Apache and not on subsequent cycles. Otherwise we would get an
    # endless redirect loop (request -> rewrite to front controller ->
    # redirect -> request -> ...).
    # So in case you get a "too many redirects" error or you always get redirected
    # to the start page because your Apache does not expose the REDIRECT_STATUS
    # environment variable, you have 2 choices:
    # - disable this feature by commenting the following 2 lines or
    # - use Apache >= 2.3.9 and replace all L flags by END flags and remove the
    #   following RewriteCond (best solution)
    RewriteCond %{ENV:REDIRECT_STATUS} =""
    RewriteRule ^index\.php(?:/(.*)|$) %{ENV:BASE}/$1 [R=301,L]

    # If the requested filename exists, simply serve it.
    # We only want to let Apache serve files and not directories.
    # Rewrite all other queries to the front controller.
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ %{ENV:BASE}/index.php [L]
</IfModule>

<IfModule !mod_rewrite.c>
    <IfModule mod_alias.c>
        # When mod_rewrite is not available, we instruct a temporary redirect of
        # the start page to the front controller explicitly so that the website
        # and the generated links can still be used.
        RedirectMatch 307 ^/$ /index.php/
        # RedirectTemp cannot be used instead
    </IfModule>
</IfModule>
```



------------
    
    