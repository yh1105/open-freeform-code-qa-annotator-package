
# Post \#56066832 [Link](https://stackoverflow.com/questions/56066832/)

## How to set SameSite cookie attribute to explicit None ASP NET Core

**Vote**: 16 (306/702) **Views**: 28106 (240/702) 

**Internal ID** \#2-6-402

Created at 2019-05-09 20:08:11

Tags: `c#` `asp.net` `asp.net-core`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

Chrome 76 will begin to support an explicit `SameSite: None` attribute 

[https://web.dev/samesite-cookies-explained/](https://web.dev/samesite-cookies-explained/)

I found that the current implementation of ASP.NET Core treats `SameSiteMode.None` as a no-op and does not send any attribute. How can I add a custom attribute to a cookie and thereby add an explicit `SameSite: None` to the cookie text?

Appending the attribute to the cookie value does not work as HttpResponse.Cookies.Append url-encodes the cookie value.


----------
        
## Answer \#0

**Accepted** Vote: 15

Created at 2019-11-22 16:29:10

------------

It's now fixed in latest release of all versions of .NET Framework and .NET Core ([https://github.com/aspnet/AspNetCore/issues/12125](https://github.com/aspnet/AspNetCore/issues/12125))
I have multiple projects running on .NET Core 2.2 and after upgrading to 2.2.207, I don't have the problem anymore.
Here a sample code present in `ConfigureServices` method of `Startup.cs` file
```
services.ConfigureApplicationCookie(options => {
     options.Cookie.SameSite = SameSiteMode.None;
});
```



------------
    
    
## Answer \#1

 Vote: 23

Created at 2019-11-12 11:33:07

------------

Same issue occurs in ASP.NET as in ASP.NET Core.

Until Microsoft produce a fix, a hack that's working for me is to replace

```
myCookie.Path = "/";
myCookie.SameSite = SameSiteMode.None;     // has no effect
```


with

```
myCookie.Path = "/; SameSite=None";
```


This adds `SameSite=None` to the `set-cookie` header in the HTTP response.


------------
    
    
## Answer \#2

 Vote: 8

Created at 2019-12-31 13:34:15

------------

[Edit] 
If you are using all dlls and packages from nuget,
you have to ensure  is in version 2.2.8 of above.

After last KB from microsoft in 10 december 2019,
It should be fixed in .net framework and dotnetcore.

see:


1. https://learn.microsoft.com/en-us/aspnet/samesite/system-web-samesite
2. https://learn.microsoft.com/en-us/aspnet/samesite/kbs-samesite




------------
    
    
## Answer \#3

 Vote: 3

Created at 2019-05-09 20:41:25

------------

`response.Headers.Append("set-Cookie", $"{cookieName}={cookieValue}; path=/; SameSite=None; Secure");` seems to work as expected.

I tested this by enabling `same-site-by-default-cookies` and `cookies-without-same-site-must-be-secure` in Chrome Dev 76


------------
    
    
## Answer \#4

 Vote: 0

Created at 2020-02-06 08:20:14

------------

Other answers have mentioned .Net Core fix, so I skip that part.

The .Net Framework fix is provided via a "Quality Rollup".

[Here](https://support.microsoft.com/en-us/help/4531182/kb4531182)'s the KB for .Net 4.8.

[Here](https://support.microsoft.com/en-us/help/4524421/kb4524421)'s the KB for .Net 4.7.2.

[Here](https://learn.microsoft.com/en-us/dotnet/api/system.web.httpcookie.samesite?view=netframework-4.8#remarks)'s the relevant MSDN source.


------------
    
    