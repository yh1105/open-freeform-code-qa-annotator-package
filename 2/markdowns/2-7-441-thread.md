
# Post \#60297371 [Link](https://stackoverflow.com/questions/60297371/)

## Trying to access array offset on value of type null when using RefreshDatabase in Laravel

**Vote**: 10 (387/702) **Views**: 27053 (247/702) 

**Internal ID** \#2-7-441

Created at 2020-02-19 09:50:03

Tags: `php` `laravel` `laravel-6`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

I am writing tests in a fresh installation of Laravel 6.15 (with Spark 9).  My PHP version is PHP 7.4.2.  My testing environment is Laravel Valet on MacOS. 

I am facing an issue where whenever I use the RefreshDatabase trait as part of a test, all tests fail with the trying to access array offset on value of type null error message.

From reading around the suggestion seems to be to downgrade to PHP 7.3 whenever this error is encountered, however there is no mention in the official documentation that PHP 7.4 isn't supported - is there any other way to resolve the error?

It triggers even if the trait is interested into the Laravel example test:

```
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class ExampleTest extends TestCase
{

    Use RefreshDatabase;

    /**
     * A basic test example.
     *
     * @return void
     */
    public function testBasicTest()
    {
        $response = $this->get('/');

        $response->assertStatus(200);
    }
}
```


Gives 

```
PHPUnit 8.5.2 by Sebastian Bergmann and contributors.

.EE                                                                 3 / 3 (100%)

Time: 212 ms, Memory: 22.00 MB

There were 2 errors:

1) Tests\Feature\ExampleTest::testBasicTest
ErrorException: Trying to access array offset on value of type null

/Users/rory/sites/landlord/vendor/laravel/telescope/src/Watchers/QueryWatcher.php:46
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Events/Dispatcher.php:369
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Events/Dispatcher.php:218
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Database/Connection.php:833
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Database/Connection.php:687
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Database/Connection.php:640
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Database/Connection.php:338
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Database/Connection.php:309
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Database/Schema/Builder.php:75
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Database/Migrations/DatabaseMigrationRepository.php:169
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Database/Migrations/Migrator.php:590
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Database/Console/Migrations/MigrateCommand.php:91
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Database/Console/Migrations/MigrateCommand.php:63
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Container/BoundMethod.php:32
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Container/Util.php:36
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Container/BoundMethod.php:90
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Container/BoundMethod.php:34
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Container/Container.php:590
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Console/Command.php:134
/Users/rory/sites/landlord/vendor/symfony/console/Command/Command.php:255
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Console/Command.php:121
/Users/rory/sites/landlord/vendor/symfony/console/Application.php:1012
/Users/rory/sites/landlord/vendor/symfony/console/Application.php:272
/Users/rory/sites/landlord/vendor/symfony/console/Application.php:148
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Console/Application.php:93
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Console/Application.php:185
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Foundation/Console/Kernel.php:273
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Foundation/Testing/PendingCommand.php:139
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Foundation/Testing/PendingCommand.php:238
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Foundation/Testing/Concerns/InteractsWithConsole.php:56
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Foundation/Testing/RefreshDatabase.php:40
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Foundation/Testing/RefreshDatabase.php:17
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Foundation/Testing/TestCase.php:115
/Users/rory/sites/landlord/vendor/laravel/framework/src/Illuminate/Foundation/Testing/TestCase.php:82
```




```
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="./vendor/phpunit/phpunit/phpunit.xsd"
         backupGlobals="false"
         backupStaticAttributes="false"
         bootstrap="vendor/autoload.php"
         colors="true"
         convertErrorsToExceptions="true"
         convertNoticesToExceptions="true"
         convertWarningsToExceptions="true"
         processIsolation="false"
         stopOnFailure="false">
    <testsuites>
        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>

        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>
    </testsuites>
    <filter>
        <whitelist processUncoveredFilesFromWhitelist="true">
            <directory suffix=".php">./app</directory>
        </whitelist>
    </filter>
    <php>
        <server name="APP_ENV" value="testing"/>
        <server name="BCRYPT_ROUNDS" value="4"/>
        <server name="CACHE_DRIVER" value="array"/>
        <server name="DB_CONNECTION" value="sqlite"/>
        <server name="DB_DATABASE" value=":memory:"/>
        <server name="MAIL_DRIVER" value="array"/>
        <server name="QUEUE_CONNECTION" value="sync"/>
        <server name="SESSION_DRIVER" value="array"/>
    </php>
</phpunit>
```



----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2020-02-24 16:04:38

------------

I have  resolved this issue - which it turns out is related to Telescope being installed and running in tests.  

To resolve this issue (and `ReflectionException: Class env does not exist` [related issue](https://github.com/laravel/telescope/issues/347)) add the following line to the `phpunit.xml` file between the `<php>` nodes:

```
<env name="TELESCOPE_ENABLED" value="false"/>
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2020-02-21 14:46:13

------------

Adding this to my TestCase class in the Laravel tests root folder solves the issue:

```
protected function setUp(): void
{
    parent::setUp();
}
```


seems to be an issue with laravel telescope:
[https://github.com/laravel/telescope/issues/804](https://github.com/laravel/telescope/issues/804)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-04-07 10:58:12

------------

I faced quite a similar issue when i upgraded my php version to 7.4.3. I typed this command

```
composer update
```


in my console and the error disappeared.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2020-11-30 13:17:56

------------

I had also faced the similar issue.
I upgraded my PHP version to 7.4 and run `composer update` and the issue was fixed.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-02-10 15:10:50

------------

This fixed the issue for me
```
composer selfupdate
```

I'm using an old Symfony 4 but the behavior should be close for some.
[Source](https://github.com/symfony/symfony/issues/32750#issuecomment-585580178)


------------
    
    