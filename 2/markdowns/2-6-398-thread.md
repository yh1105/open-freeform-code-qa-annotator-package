
# Post \#52921966 [Link](https://stackoverflow.com/questions/52921966/)

## Unable to resolve ILogger from Microsoft.Extensions.Logging

**Vote**: 163 (30/702) **Views**: 139233 (57/702) 

**Internal ID** \#2-6-398

Created at 2018-10-22 03:16:38

Tags: `c#` `.net` `dependency-injection` `.net-core` `ilogger`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I've configured my console application's `Main` like so
```
var services = new ServiceCollection()
    .AddLogging(logging => logging.AddConsole())
    .BuildServiceProvider();
```

And then I try to use it in another class like so
```
private readonly ILogger _logger;
    
public MyClass(ILogger logger)
{
    _logger = logger;
}

public void MyFunc()
{
    _logger.Log(LogLevel.Error, "My Message");
}
```

> System.InvalidOperationException: 'Unable to resolve service for type
'Microsoft.Extensions.Logging.ILogger'
I've tried the solutions [here](https://stackoverflow.com/a/51329388/2025666) but it didn't work for me.

Based on Yaakov's comment below and [this Github comment](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/issues/92#issuecomment-223991293) I'm able to resolve it correctly by doing this
```
public MyClass(ILogger<MyClass> logger)
{
    _logger = logger;
}
```

I would have preferred to have this in the initial `BuildServiceProvider` but looks like I'm gonna have to repeat this every time I want to use the logger (or create my own ILogger).


----------
        
## Answer \#0

**Accepted** Vote: 173

Created at 2019-08-21 10:56:27

------------

`ILogger` is no longer registered by default but `ILogger<T>` is. If you still want to use ILogger you can register it manually with the following (in `Startup.cs`):
```
public void ConfigureServices(IServiceCollection services)
{
    var serviceProvider = services.BuildServiceProvider();
    var logger = serviceProvider.GetService<ILogger<AnyClass>>();
    services.AddSingleton(typeof(ILogger), logger);
    ...
}
```

Where `AnyClass` can be something generic, such as:
```
public class ApplicationLogs
{
}
```

So:
```
public void ConfigureServices(IServiceCollection services)
{
    var serviceProvider = services.BuildServiceProvider();
    var logger = serviceProvider.GetService<ILogger<ApplicationLog>>();
    services.AddSingleton(typeof(ILogger), logger);
    ...
}
```

`ILogger` will now resolve via constructor injection.


------------
    
    
## Answer \#1

 Vote: 32

Created at 2018-10-22 03:25:54

------------

I am assuming you are using the default template for .net core web application.
In your your Startup.cs you should have a method like this↓↓↓↓↓↓↓

```
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
        {
            // This lambda determines whether user consent for non-essential cookies is needed for a given request.
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });


        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
        //Do the service register here and extra stuff you want
         services.AddLogging(config =>
        {
            config.AddDebug();
            config.AddConsole();
            //etc
        });

    }
```


Edit: I have written a simple program for you to show how it works

```
public class MyClass
{

    private readonly ILogger<MyClass> _logger;


    public MyClass(ILogger<MyClass> logger)
    {
        _logger = logger;
    }
    public void MyFunc()
    {
        _logger.Log(LogLevel.Error, "My Message");
    }
}



public class Program
{
    public static void Main(string[] args)
    {
        var services = new ServiceCollection().AddLogging(logging => logging.AddConsole());
        services.AddSingleton<MyClass>();//Singleton or transient?!
        var s = services.BuildServiceProvider();
        var myclass = s.GetService<MyClass>();
     }
}
```


[](https://i.stack.imgur.com/mDlW5.png)
Edit: Output of the program:
[](https://i.stack.imgur.com/m53bc.png)


------------
    
    
## Answer \#2

 Vote: 17

Created at 2021-09-29 21:39:41

------------

For .NET Core 3.1, be sure to include the
`Microsoft.Extensions.Logging`
package and configure logging by calling
```
IServiceCollection services = new ServiceCollection();
services.AddLogging();
```



------------
    
    
## Answer \#3

 Vote: 15

Created at 2022-03-03 19:31:46

------------

For anyone that gets this error using NLog, I found I was making the following error in my dependency injection:
```
private readonly ILogger _logger;

        public ErrorController(ILogger ilogger)
        {
            _logger = ilogger;
        }
```

[According to the documentation](https://github.com/NLog/NLog/wiki/Getting-started-with-ASP.NET-Core-5), in .Net 5, you need to add a type of the Controller itself to the logger in order to avoid this issue. Dependency injecting as such fixed the error:
```
private readonly ILogger<ErrorController> _logger;

        public ErrorController(ILogger<ErrorController> ilogger)
        {
            _logger = ilogger;
        }
```

After making the above change, this fixed my issue. DO NOT ATTEMPT ANY OF THE SOLUTIONS HERE! This will only send you down a rabbit hole.
Hope this helps anyone in the future!


------------
    
    
## Answer \#4

 Vote: 12

Created at 2020-07-16 08:20:27

------------

In .NET Core, `ILogger<T>` is automatically registered for you.  As `ILogger<T>` inherits from `ILogger`, you can request an instance of `ILogger<T>` from the `IServiceProvider`.
For example:
`services.AddSingleton<ILogger>(svc => svc.GetRequiredService<ILogger<MyClassType>>());`
Note that this will return an `ILogger<MyClassType>` wherever you've got a non-generic `ILogger` constructor parameter so if you need more than one, look into creating it specifically for that instance by using the `AddSingleton` (or transient/or scoped) implementationFactory override.


------------
    
    
## Answer \#5

 Vote: 12

Created at 2021-01-12 14:33:43

------------

As noted in a comment, the accepted answer has the problem that `BuildServiceProvider` should not be used in `ConfigureServices`, since it creates another copy of each singleton (in fact, of the whole DI container). Here is an alternative one-liner that avoids that problem. In `ConfigureServices`, add the line
```
services.AddSingleton<Microsoft.Extensions.Logging.ILogger>(provider => 
   provider.GetRequiredService<Microsoft.Extensions.Logging.ILogger<AnyClass>>());
```

where `AnyClass` can be any class, as explained in the accepted answer. This redirects the resolution of `ILogger` to that of `ILogger<AnyClass>`.
Like the accepted answer, this one has the disadvantage that Serilog's `SourceContext` will be set to `AnyClass`. So, if your Serilog configuration contains an `outputTemplate` that contains `{SourceContext}`, your log will show `AnyClass` instead of the class that contains the logging statement.


------------
    
    
## Answer \#6

 Vote: 5

Created at 2020-11-08 23:19:00

------------

This answer isn't necessarily for .net Core or whatever dependency injection is being used by the poster. I ran across this searching for a way to fix this error using Autofac in an asp.net forms web application. The answer of logging not being "registered by default" led me to add this to my registrations:
```
builder.RegisterType<LoggerFactory>().As<ILoggerFactory>().SingleInstance();
builder.RegisterGeneric(typeof(Logger<>)).As(typeof(ILogger<>)).SingleInstance();
```

And now it works for me. This is the second time I searched for this answer and ended up here, I forgot how I fixed it the first time. So I thought I'd post my answer.


------------
    
    
## Answer \#7

 Vote: 3

Created at 2020-07-23 14:43:49

------------

The approach I used when trying to use an `ILogger` (Because it's no longer injected), as to inject a `ILoggerFactory`, and then when your code wants a specific logger (say its some custom factory you're `new`ing up, `LoggerFactory.CreateLogger("Logger Name")` - Below
But for your case,it looks like you want a `ILogger<MyClass>`
```
public class MyClass
{

    private readonly ILoggerFactory _loggerFactory;


    public MyClass(ILoggerFactory loggerFactory)
    {
        _loggerFactory = _loggerFactory;
    }
    public void MyFunc()
    {
       new MyNewThatsASpecialCase(_loggerFactory.CreateLogger("MyNewThatsASpecialCase"));
    }
}
```



------------
    
    
## Answer \#8

 Vote: 3

Created at 2021-01-27 20:51:00

------------

Based on @[Barry](https://stackoverflow.com/users/680563/barry-franklin) answer [above](https://stackoverflow.com/a/64744036/959779) found out how to register generic log provider for `Microsoft.Extensions.DependencyInjection`:
```
var services = new ServiceCollection()
                            .AddLogging(logging => logging.AddConsole())
                            .BuildServiceProvider();

services.AddSingleton<ILoggerFactory, LoggerFactory>()
services.Add(ServiceDescriptor.Describe(typeof(ILogger<>),typeof(Logger<>),ServiceLifetime.Scoped))
```



------------
    
    
## Answer \#9

 Vote: 2

Created at 2021-07-23 20:04:32

------------

This is what worked for me to get DI working for ILogger.
For .NET Core see:
[https://learn.microsoft.com/en-us/aspnet/core/fundamentals/logging/?view=aspnetcore-5.0](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/logging/?view=aspnetcore-5.0)
Specifically:
```
.ConfigureLogging(logging =>
            {
                logging.ClearProviders();
                logging.AddConsole();
            })
```

Reference:
```
public static IHostBuilder CreateHostBuilder(string[] args) =>
Host.CreateDefaultBuilder(args)
    .ConfigureLogging(logging =>
    {
        logging.ClearProviders();
        logging.AddConsole();
    })
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
```



------------
    
    
## Answer \#10

 Vote: 1

Created at 2022-01-06 16:44:52

------------

The accepted answer (as provided by @sam-shiles) goes in the right direction. I don't like that it requires building an intermediate service provider, and the registration of a singleton instance is maybe not wanted as well.
So, I suggest the registration of a factory instead, that utilizes `ILoggerFactory` instead. The creation of an empty class (like `AnyClass` is also not needed).
```
...
services.AddTransient(provider =>
{
    var loggerFactory = provider.GetRequiredService<ILoggerFactory>();
    const string categoryName = "Any";
    return loggerFactory.CreateLogger(categoryName);
});
...
```



------------
    
    