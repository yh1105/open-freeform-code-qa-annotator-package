
# Post \#69952420 [Link](https://stackoverflow.com/questions/69952420/)

## How to disable Browser Link in ASP.NET Core (.NET 6, VS 2022)

**Vote**: 33 (185/702) **Views**: 6913 (484/702) 

**Internal ID** \#2-6-397

Created at 2021-11-13 07:44:26

Tags: `c#` `visual-studio` `asp.net-core` `.net-core` `browser-link`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I have disabled Browser Link inside Visual Studio 2022, and I have also disabled all the Hot Reload functionality.
[](https://i.stack.imgur.com/0KgN3.png)
[](https://i.stack.imgur.com/SmZuJ.png)
Even the Browser Link Dashboard indicates it should be disabled:
[](https://i.stack.imgur.com/8qxYg.png)
I also  any of the `app.UseBrowserLink();` code anywhere in my code. (as documented in [Browser Link in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/client-side/using-browserlink?view=aspnetcore-6.0).
However, these middlewares still somehow appear in the request pipeline:
- `Microsoft.AspNetCore.Watch.BrowserRefresh.BrowserRefreshMiddleware`- `Microsoft.WebTools.BrowserLink.Net.BrowserLinkMiddleware`
These middlewares add this to my HTML:
```
<!-- Visual Studio Browser Link -->
<script type="text/javascript" src="https://localhost:44399/0928d478a97d441187231f2a1a7cd5e5/browserLink" async="async" id="__browserLink_initializationData" data-requestId="c94cf93088fb44e98f4e499b20ad7bfe" data-requestMappingFromServer="False"></script>
<!-- End Browser Link -->

<script src="/_framework/aspnetcore-browser-refresh.js"></script></body>
```

And every time I save a file, it reloads the browser. I want to stop this behaviour, and I ran out of options how.
Is this something that the IIS Express or Visual Studio add automatically?
I also checked all the packages and DLLs, and I don't see it included anywhere in my code. It must be included automatically via some "magic" that Visual Studio 2022 does.



----------
        
## Answer \#0

**Accepted** Vote: 32

Created at 2021-11-14 03:44:20

------------

You are almost there. You just need to also config the following (Don't forget to restart your VS):
[](https://i.stack.imgur.com/JOO2Q.png)
[](https://i.stack.imgur.com/ilo30.png)


------------
    
    
## Answer \#1

 Vote: 4

Created at 2022-04-11 15:18:31

------------

Visual Studio uses .NET Core's `Startup Hook` feature to inject those middlewares, if you set a breakpoint on the first line of your Program.cs and evaluate `System.Environment.GetEnvironmentVariable("DOTNET_STARTUP_HOOKS")` in the debugger, you can see which hooks are getting loaded from where. If you now go and rename the files it's trying to load, it won't be able anymore to do that and you're free from browserLink!.
For example, in my case, it was loading
```
"C:\Program Files\Microsoft Visual Studio\2022\Enterprise\Common7\IDE\Extensions\Microsoft\Web Tools\Browser Link\Microsoft.WebTools.BrowserLink.Net.dll"
"C:\Program Files\dotnet\sdk\6.0.201\DotnetTools\dotnet-watch\6.0.201-servicing.22124.17\tools\net6.0\any\middleware\Microsoft.AspNetCore.Watch.BrowserRefresh.dll"
```

so i renamed them, adding a `_` in front of them which made the script injection disappear.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-06-14 09:01:17

------------

More needed to uncheck!
[](https://i.stack.imgur.com/eh6kn.png)
This is really hard to unroot feature.


------------
    
    