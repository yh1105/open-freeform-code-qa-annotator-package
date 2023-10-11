
# Post \#62964949 [Link](https://stackoverflow.com/questions/62964949/)

## The MetadataAddress or Authority must use HTTPS unless disabled for development by setting RequireHttpsMetadata=false

**Vote**: 8 (428/702) **Views**: 22802 (275/702) 

**Internal ID** \#2-6-420

Created at 2020-07-18 05:19:09

Tags: `c#` `azure` `.net-core` `azure-active-directory` `azure-appservice`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

net core application. I have added azure ad authentication. Below is my startup.cs file
```
services.AddAuthentication(x =>
            {
                x.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                x.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
            })
            .AddJwtBearer(options =>
            {
                options.SaveToken = true;
                options.RequireHttpsMetadata = true;
                options.Authority = $"{authSettings.Authority}/{authSettings.TenantId}";
                options.Audience = authSettings.ClientId;
                options.TokenValidationParameters = new TokenValidationParameters
                {
                    ValidateAudience = true,
                    ValidateLifetime = true,
                    ValidateIssuer = true,
                    ValidateIssuerSigningKey = false,
                    ValidateActor = false,
                };
            });
```

I have deployed application to azure app service. It deployed successfully. I opened swagger and authenticated and tried to hit api. It throws me 500 error. I went to azure app service log stream. It shows me below error message.
```
/appsvctmp/volatile/logs/runtime/0ad1b1161bafbb0b8662db769e40eca0226d3d1d7d4737bdbe13f88e8a4f089b.log
2020-07-18T03:48:32.615339369Z: [INFO]  Hosting environment: Production
2020-07-18T03:48:32.615384568Z: [INFO]  Content root path: /app
2020-07-18T03:48:32.616251458Z: [INFO]  Now listening on: http://[::]:8081
2020-07-18T03:48:32.616952250Z: [INFO]  Application started. Press Ctrl+C to shut down.
/appsvctmp/volatile/logs/runtime/f342b472edabea44dc320f2bfa84254b92f9d5ddb433c5d248c4cae636eecd28.log
2020-07-18T05:04:03.313150755Z: [INFO]  Hosting environment: Production
2020-07-18T05:04:03.313765848Z: [INFO]  Content root path: /app
2020-07-18T05:04:03.314598838Z: [INFO]  Now listening on: http://[::]:8081
2020-07-18T05:04:03.315059032Z: [INFO]  Application started. Press Ctrl+C to shut down.
Ending Log Tail of existing logs ---
Starting Live Log Stream ---
2020-07-18T05:12:23  No new trace in the past 1 min(s).
2020-07-18T05:13:23  No new trace in the past 2 min(s).
2020-07-18T05:13:39.265412299Z: [INFO]  [41m[30mfail[39m[22m[49m: Microsoft.AspNetCore.Diagnostics.ExceptionHandlerMiddleware[1]
2020-07-18T05:13:39.265449199Z: [INFO]        An unhandled exception has occurred while executing the request.
2020-07-18T05:13:39.265456199Z: [INFO]  System.InvalidOperationException: The MetadataAddress or Authority must use HTTPS unless disabled for development by setting RequireHttpsMetadata=false.
2020-07-18T05:13:39.265461099Z: [INFO]     at Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerPostConfigureOptions.PostConfigure(String name, JwtBearerOptions options)
2020-07-18T05:13:39.265465899Z: [INFO]     at Microsoft.Extensions.Options.OptionsFactory`1.Create(String name)
2020-07-18T05:13:39.265470499Z: [INFO]     at Microsoft.Extensions.Options.OptionsMonitor`1.<>c__DisplayClass11_0.<Get>b__0()
2020-07-18T05:13:39.265475899Z: [INFO]     at System.Lazy`1.ViaFactory(LazyThreadSafetyMode mode)
2020-07-18T05:13:39.265480298Z: [INFO]  --- End of stack trace from previous location where exception was thrown ---
2020-07-18T05:13:39.265484698Z: [INFO]     at System.Lazy`1.CreateValue()
2020-07-18T05:13:39.265489098Z: [INFO]     at System.Lazy`1.get_Value()
2020-07-18T05:13:39.265493798Z: [INFO]     at Microsoft.Extensions.Options.OptionsCache`1.GetOrAdd(String name, Func`1 createOptions)
2020-07-18T05:13:39.265498198Z: [INFO]     at Microsoft.Extensions.Options.OptionsMonitor`1.Get(String name)
2020-07-18T05:13:39.265502598Z: [INFO]     at Microsoft.AspNetCore.Authentication.AuthenticationHandler`1.InitializeAsync(AuthenticationScheme scheme, HttpContext context)
2020-07-18T05:13:39.265507098Z: [INFO]     at Microsoft.AspNetCore.Authentication.AuthenticationHandlerProvider.GetHandlerAsync(HttpContext context, String authenticationScheme)
2020-07-18T05:13:39.265511698Z: [INFO]     at Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync(HttpContext context, String scheme)
2020-07-18T05:13:39.265516198Z: [INFO]     at Microsoft.AspNetCore.Authentication.AuthenticationMiddleware.Invoke(HttpContext context)
2020-07-18T05:13:39.265520598Z: [INFO]     at SGRE.SiteEnrichment.WebApi.Startup.<>c.<<Configure>b__5_2>d.MoveNext() in d:\a\1\s\src\app\SGRE.SiteEnrichment.WebApi\Startup.cs:line 211
2020-07-18T05:13:39.265526198Z: [INFO]  --- End of stack trace from previous location where exception was thrown ---
2020-07-18T05:13:39.265530698Z: [INFO]     at Swashbuckle.AspNetCore.SwaggerUI.SwaggerUIMiddleware.Invoke(HttpContext httpContext)
2020-07-18T05:13:39.265535098Z: [INFO]     at Swashbuckle.AspNetCore.Swagger.SwaggerMiddleware.Invoke(HttpContext httpContext, ISwaggerProvider swaggerProvider)
2020-07-18T05:13:39.265539498Z: [INFO]     at Swashbuckle.AspNetCore.SwaggerUI.SwaggerUIMiddleware.Invoke(HttpContext httpContext)
2020-07-18T05:13:39.265544798Z: [INFO]     at Swashbuckle.AspNetCore.Swagger.SwaggerMiddleware.Invoke(HttpContext httpContext, ISwaggerProvider swaggerProvider)
```

I have set AzureAd:Authority to [https://login.microsoftonline.com/](https://login.microsoftonline.com/) in app service app settings configurations. Can someone help me to understand this issue? Any help would be greatly appreciated. Thank you


----------
        
## Answer \#0

**Accepted** Vote: 26

Created at 2020-07-18 12:42:19

------------

You can get rid of this error by adding `JwtBearerOptions.RequireHttpsMetadata` to false as `ConfigureServices`
```
public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;

        }).AddJwtBearer(options =>
        {
            options.Authority = Configuration["Auth0:Authority"];
            options.Audience = Configuration["Auth0:Audience"];
            options.RequireHttpsMetadata = false;
        });          
    }
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2022-02-23 07:16:50

------------

FWIW: I spent an hour not finding the leading space in the url.
`options.Authority = " https://localhost:5005";`
`^`


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-05-09 07:01:22

------------

You can simply choose https endpoints for Audience and Authority.
An example with local ones.
```
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme).AddJwtBearer(options => {
        options.Audience = "https://localhost:5001/";
        options.Authority = "https://localhost:5000/";
    });
...
}
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-08-04 15:57:46

------------

In my case, it was just simply http instead of https in “Authority” attribute of JwtBearer options,
```
options.Authority = "https://localhost:5005";
```



------------
    
    