
# Post \#60306175 [Link](https://stackoverflow.com/questions/60306175/)

## Bearer error="invalid_token", error_description="The issuer is invalid"

**Vote**: 12 (356/702) **Views**: 25915 (256/702) 

**Internal ID** \#2-6-390

Created at 2020-02-19 17:35:25

Tags: `c#` `asp.net-core` `.net-core` `postman` `identityserver4`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I have a simple web api project, which looks like this:

```
[Authorize]
        [Route("Get")]
        public ActionResult<string> SayHello()
        {
            return "Hello World";
        }
```


I am trying to test it with Postman.  By following the steps here: [https://kevinchalet.com/2016/07/13/creating-your-own-openid-connect-server-with-asos-testing-your-authorization-server-with-postman/](https://kevinchalet.com/2016/07/13/creating-your-own-openid-connect-server-with-asos-testing-your-authorization-server-with-postman/)

1) Send the request below and receive a token as expected:

[](https://i.stack.imgur.com/iqduu.png)

2) Attempt to send another request with the authorization token as shown below:

[](https://i.stack.imgur.com/StJYj.png)

Why do I get a 401 (unauthorized) error? The WWW-Authenticate response header says: Bearer error="invalid_token", error_description="The issuer is invalid".  I am using .Net Core 3.1.  I have commented out the sensitive information in the screenshots.

The web api works as expected when accessed from an MVC application.

Here is the startup code:

```
services.AddAuthentication("Bearer")
                .AddIdentityServerAuthentication(options =>
                {
                    options.Authority = identityUrl; //identityurl is a config item
                    options.RequireHttpsMetadata = false;
                    options.ApiName = apiName;

                });
```



----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2020-02-20 08:13:36

------------

The `Authority` of `AddIdentityServerAuthentication` middleware should be the base-address of your identityserver , middleware will contact the identity server's OIDC metadata endpoint to get the public keys to validate the JWT token .

Please confirm that the Authority is the url of identity server where you issued the jwt token .


------------
    
    
## Answer \#1

 Vote: 6

Created at 2020-09-04 11:55:34

------------

I ran into a similar issue. I was generating my token via Postman when sending in my request and using an external IP to access my Keycloak instance running inside of my kubernetes cluster. When my service inside the cluster tried to verify the token against the authority, it failed because the internal service name (http://keycloak) it used to validated the token was different than what Postman had used to generate the token (<external-keycloak-ip).
Since this was just for testing, I set the `ValidateIssuer` to false.
```
options.TokenValidationParameters = new TokenValidationParameters
{
    ValidateIssuer = false 
};
```



------------
    
    
## Answer \#2

 Vote: 5

Created at 2021-03-16 23:00:42

------------

I'm on dotnet 5.0, adding swagger (NSwag.AspNetCore) to my AzureAD "protected" web api and got a similar error about invalid issuer:
```
date: Tue,16 Mar 2021 22:50:58 GMT 
 server: Microsoft-IIS/10.0 
 www-authenticate: Bearer error="invalid_token",error_description="The issuer 'https://sts.windows.net/<your-tenant-id>/' is invalid" 
 x-powered-by: ASP.NET
```

So, instead of not validating the issuer, I just added sts.windows.net to the list (important parts in the end):
```
// Enable JWT Bearer Authentication
services.AddAuthentication(sharedOptions =>
{
    sharedOptions.DefaultScheme = JwtBearerDefaults.AuthenticationScheme;
}).AddJwtBearer(options =>
{
    Configuration.Bind("AzureAd", options);
    // Authority will be Your AzureAd Instance and Tenant Id
    options.Authority = $"{Configuration["AzureAd:Instance"]}{Configuration["AzureAd:TenantId"]}/v2.0";

    // The valid audiences are both the Client ID(options.Audience) and api://{ClientID}
    options.TokenValidationParameters.ValidAudiences = new[]
    {
        Configuration["AzureAd:ClientId"], $"api://{Configuration["AzureAd:ClientId"]}",

    };
    // Valid issuers here:
    options.TokenValidationParameters.ValidIssuers = new[]
    {
        $"https://sts.windows.net/{Configuration["AzureAd:TenantId"]}/",
        $"{Configuration["AzureAd:Instance"]}{Configuration["AzureAd:TenantId"]}/"
    };
});
```

This solved my problems. Now, why NSwag uses sts.windows.net as token issuer, I don't know. Seems wrong. I'm using these package versions:
```
<PackageReference Include="Microsoft.AspNetCore.Authentication" Version="2.2.0" />
    <PackageReference Include="Microsoft.AspNetCore.Authentication.JwtBearer" Version="5.0.3" />
    <PackageReference Include="NSwag.AspNetCore" Version="13.10.8" />
```



------------
    
    
## Answer \#3

 Vote: 3

Created at 2022-06-10 14:03:38

------------

Setting `ValidateIssuer = false` like @nedstark179 proposes will work but it will also remove a security validation.
If you use a ASP.NET Core template with Individual Accounts (IdentityServer) and receive this error:
`WWW-Authenticate: Bearer error="invalid_token", error_description="The issuer 'https://example.com' is invalid"`
It is probably related to this issue:
[https://github.com/dotnet/aspnetcore/issues/28880](https://github.com/dotnet/aspnetcore/issues/28880)
For example a new Blazor Webassembly App with Individual Accounts and ASP.NET Core hosted from Visual Studio.
![image](https://user-images.githubusercontent.com/4015237/172410140-3153cad7-9889-434d-b650-16cbbee70aeb.png)
.NET 6.0 Known Issues only mentions it could happen in development but it can happen in production hosted as an Azure App Service as well.
[https://github.com/dotnet/core/blob/main/release-notes/6.0/known-issues.md#spa-template-issues-with-individual-authentication-when-running-in-development](https://github.com/dotnet/core/blob/main/release-notes/6.0/known-issues.md#spa-template-issues-with-individual-authentication-when-running-in-development)
I created an issue about that here:
[https://github.com/dotnet/aspnetcore/issues/42072](https://github.com/dotnet/aspnetcore/issues/42072)
The example fix for development was not enough.
Started of by adding a new Application settings for the Azure App Service called `IdentityServer:IssuerUri` with value `https://example.com/`. This can of course be placed in `appsettings.json` as well.  I then added the code below:
```
//Used until https://github.com/dotnet/aspnetcore/issues/42072 is fixed
if (!string.IsNullOrEmpty(settings.IdentityServer.IssuerUri))
{
    builder.Services.Configure<JwtBearerOptions>(IdentityServerJwtConstants.IdentityServerJwtBearerScheme, o => o.Authority = settings.IdentityServer.IssuerUri);
}
```

below this code:
```
builder.Services.AddAuthentication()
    .AddIdentityServerJwt();
```

I have not verified if it matters where the code is placed but `AddIdentityServerJwt()` calls `AddPolicyScheme` and `.AddJwtBearer(IdentityServerJwtConstants.IdentityServerJwtBearerScheme, null, o => { });`. Therefore I deemed it appropriate to set it after this code has been called.
After doing this the app still failed with the same error. I then modified `AddIdentityServer` like this:
```
builder.Services.AddIdentityServer(options =>
                {
                    //Used until https://github.com/dotnet/aspnetcore/issues/42072 is fixed
                    if (!string.IsNullOrEmpty(settings.IdentityServer.IssuerUri))
                    {
                        options.IssuerUri = settings.IdentityServer.IssuerUri;
                    }
                })
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
```

and then it started working for me. If you still experience a problem you could also try to set `AuthenticatorIssuer` like this:
```
builder.Services.AddDefaultIdentity<ApplicationUser>(options =>
{
    options.SignIn.RequireConfirmedAccount = true;
    //Used until https://github.com/dotnet/aspnetcore/issues/42072 is fixed
    if (!string.IsNullOrEmpty(settings.IdentityServer.IssuerUri))
    {
        options.Tokens.AuthenticatorIssuer = settings.IdentityServer.IssuerUri;
    }
})
    .AddEntityFrameworkStores<ApplicationDbContext>();
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-05-05 08:33:56

------------

In my case, simply adding /v2.0 to the Authority was sufficient.
Case:
```
services.AddAuthentication(x => {
            x.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            x.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
        }).AddJwtBearer(options => {
            options.Audience = "client id here";
            options.Authority = "https://login.microsoftonline.com/tenant id here/v2.0";
        });
```



------------
    
    