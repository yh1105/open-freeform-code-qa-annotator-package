
# Post \#71201393 [Link](https://stackoverflow.com/questions/71201393/)

## How to create a global variable in ASP.NET Core Web API application?

**Vote**: 3 (575/702) **Views**: 9771 (418/702) 

**Internal ID** \#2-6-389

Created at 2022-02-21 04:58:48

Tags: `c#` `asp.net-core`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

How can I create a global variable in an ASP.NET Core Web API application? In ASP.NET MVC, I could do it like:
`Application["<variableName>"] = <value>`
I tried the same in my web API application, but was unable to find an equivalent for this. I saw some solutions which suggested me to store the data `appsettings.json`, but since the data I want to store in the global variable is not static, I cannot use that setup. I need to set different data during runtime. How can I do that? Please help me.


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-02-21 05:19:58

------------

somewhere in project
```
public static class Config{
       public static Dictionary<string,string> Application = new Dictionary<string,string>();
}
```

elsewhere
```
Config.Application["froop"] = "noodle";
```

of course you will have to deal with race conditions, etc


------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-02-21 05:37:05

------------

We could use [Singleton_pattern](https://en.wikipedia.org/wiki/Singleton_pattern) , creating an `Application` static object in .net core.
we can also try to use Depend Injection, register a singleton object as below in your code.
Writing a property `ConcurrentDictionary` in `ApplicationInstance` class.
```
public class ApplicationInstance {

    public ConcurrentDictionary<string,object> Application { get; } = new ConcurrentDictionary<string, object>();
}

public void ConfigureServices(IServiceCollection services){
    services.AddSingleton<ApplicationInstance>();
}
```

Then we might use this object
```
public class HomeController : ControllerBase
{
    private readonly ApplicationInstance _application;

    public HomeController(ApplicationInstance application)
    {
        this._application = application;
    }
    
    //use _application["test"] instance in your code
}
```

I would use `ConcurrentDictionary` to help us avoid racing-condition which will be happened on multiple-thread accessing the same object concurrently.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-02-22 04:19:32

------------

First of all thank you all for finding some time to help me. I took @pm100's solution and made a slight modification to suit my purpose. I could not use it directly as I am using SonarLint, and it had some issues, so I used a `KeyValuePair` instead of `Dictionary` like this:
```
public static class GlobalData
{
  public static KeyValuePair<string,object> Application { get; set; }
}
```

and I used it in my code like:
`string value = GlobalData.Application.Value`
and luckily it works without any issue.


------------
    
    