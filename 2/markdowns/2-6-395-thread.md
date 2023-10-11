
# Post \#70800034 [Link](https://stackoverflow.com/questions/70800034/)

## Add Swagger description to minimal .NET6 APIs

**Vote**: 10 (387/702) **Views**: 8300 (453/702) 

**Internal ID** \#2-6-395

Created at 2022-01-21 10:44:16

Tags: `c#` `swagger` `swagger-ui` `.net-6.0` `minimal-apis`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I have a small project in .NET6 that contains minimal APIs like that one
```
app.MapGet("/clients",
     async (IClientRepository repo) =>
     {
          var results = await repo.GetClientsAsync();
          return mapper.Map<IEnumerable<ClientModel>>(results);
     });
```

In the `SwaggerUI` I can use this API but I can't find a way to add description to it (although in the project settings I check for creating an API XML documentation).
[](https://i.stack.imgur.com/DfCTU.png)
How can I add the XML comment?


----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2022-06-28 15:29:27

------------


```
dotnet add package Swashbuckle.AspNetCore.Annotations
```


```
// Program.cs

builder.Services.AddSwaggerGen(x =>
{
  x.EnableAnnotations();
});

// app.MapGet("/hi", [SwaggerOperation(Summary = "summary001", Description = "description001 `adads`")] () => "Hi");

app.MapGet("/hi", () => "Hi")
  .WithMetadata(new SwaggerOperationAttribute("summary001", "description001"));
```


# Result


[](https://i.stack.imgur.com/RFy0G.png)


------------
    
    
## Answer \#1

 Vote: 21

Created at 2022-01-21 13:01:13

------------

Currently support for Open API docs for minimal APIs is [quite minimal](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis?view=aspnetcore-6.0#openapi) and does not allow adding descriptions/summaries as far as I can see. There is a feature planned for [.NET 7](https://github.com/dotnet/aspnetcore/issues/37906) to add descriptions. Also soon `Swashbuckle` should [consider EndpointMetadata for annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/pull/2215).
Also related [issue](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/issues/2267).

With latest updates to `Swashbuckle` nuget packages and `Swashbuckle.AspNetCore.Annotations` you can add description to the endpoints:
```
builder.Services.AddSwaggerGen(opts => opts.EnableAnnotations());

app.MapGet("/weatherforecast", () =>
{
    // Implementation
})
.WithMetadata(new SwaggerOperationAttribute(summary: "Summary", description: "Descritption Test"));

// Or

app.MapGet("/weatherforecast1", [SwaggerOperation(summary: "Summary1", description: "Descritption Test1")] () =>
{
    // Implementation
});
```


For .NET 7 and latest `Swashbuckle.AspNetCore` package `WithDescription` method also can be used:
```
builder.Services.AddSwaggerGen();
...

app.MapGet("/weatherforecast", () =>
{
    // implementation
})
.WithDescription("Some Method Description")
.WithOpenApi();
```

Or with `EndpointDescriptionAttribute`:
```
app.MapGet("/weatherforecast1", [EndpointDescription("Some Attribute description")] () =>
{
    // implementation
})
.WithOpenApi();
```



------------
    
    
## Answer \#2

 Vote: 8

Created at 2022-05-03 17:43:53

------------

package `Swashbuckle.AspNetCore.Annotations` `6.3`
```
...
builder.Services.AddSwaggerGen(c => c.EnableAnnotations());

var app = builder.build();

app.MapGet("/clients",
    [SwaggerOperation(
        Summary = "returns clients",
        Description = "more description on get `clients`")]
    [SwaggerResponse(200, "success")]
    [SwaggerResponse(500, "some failure")]
    async (IClientRepository repo) =>
    {
        var results = await repo.GetClientsAsync();
        return mapper.Map<IEnumerable<ClientModel>>(results);
    }).WithTags("Clients");
```

more examples here
[https://github.com/domaindrivendev/Swashbuckle.AspNetCore#enrich-operation-metadata](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#enrich-operation-metadata)


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-04-18 06:18:48

------------

You may use [this guide](https://medium.com/geekculture/minimal-apis-in-net-6-a-complete-guide-beginners-advanced-fd64f4da07f5). It worked for me using Swashbuckle. There are extension methods that come with minimal APIs. Here is how it looks:
```
app.MapGet(“/books”, async (BooksDB db) =>
await db.Books.ToListAsync()
)
.Produces<List<Book>>(StatusCodes.Status200OK)
.WithName(“GetAllBooks”).WithTags(“Getters”);
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2023-02-08 10:50:37

------------

you may use Extension Methods Which I think are more cleaner way to document API endpoints. This way you can define generic response methods for each endpoint.
For this you need to install the following packages :
[Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/) and [Swashbuckle.AspNetCore.Annotations](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Annotations)
```
using Microsoft.AspNetCore.Authorization;
using Swashbuckle.AspNetCore.Annotations;


public static class MinimalAttributeExtensions
{
    public static RouteHandlerBuilder AllowAnonymous(this RouteHandlerBuilder endpoint)
    {
        endpoint.WithMetadata(new AllowAnonymousAttribute());
        return endpoint;
    }
    public static RouteHandlerBuilder Authorize(this RouteHandlerBuilder endpoint, string policy = null, string[] roles = null, params string[] schemes)
    {
        var authorizeAttribute = new AuthorizeAttribute();

        if (!string.IsNullOrEmpty(policy))
        {
            authorizeAttribute.Policy = policy;
        }

        if (roles != null && roles.Any())
        {
            authorizeAttribute.Roles = string.Join(',', roles);
        }

        if (schemes != null && schemes.Any())
        {
            authorizeAttribute.AuthenticationSchemes = string.Join(',', schemes);
        }

        endpoint.WithMetadata(authorizeAttribute);

        return endpoint;
    }
    public static RouteHandlerBuilder AddMetaData<T>(this RouteHandlerBuilder endpoint, string tag, string summary = null, string description = null)
    {
        endpoint.WithTags(tag);

        endpoint.WithMetadata(new SwaggerOperationAttribute(summary, description));

        endpoint.WithMetadata(new SwaggerResponseAttribute(200, type: typeof(T)))
                .WithMetadata(new SwaggerResponseAttribute(500, type: typeof(ErrorResponseModel)))
                .WithMetadata(new SwaggerResponseAttribute(400, type: typeof(ErrorResponseModel)))
                .WithMetadata(new SwaggerResponseAttribute(404, type: typeof(ErrorResponseModel)))
                .WithMetadata(new SwaggerResponseAttribute(422, type: typeof(ErrorResponseModel)))
                .WithMetadata(new SwaggerResponseAttribute(304, type: typeof(ErrorResponseModel)));

        return endpoint;
    }
    public static RouteHandlerBuilder AddMetaData(this RouteHandlerBuilder endpoint, string tag, string summary = null, string description = null)
    {
        endpoint.WithTags(tag);

        endpoint.WithMetadata(new SwaggerOperationAttribute(summary, description));

        endpoint.WithMetadata(new SwaggerResponseAttribute(500, type: typeof(ErrorResponseModel)))
                .WithMetadata(new SwaggerResponseAttribute(400, type: typeof(ErrorResponseModel)))
                .WithMetadata(new SwaggerResponseAttribute(404, type: typeof(ErrorResponseModel)))
                .WithMetadata(new SwaggerResponseAttribute(422, type: typeof(ErrorResponseModel)))
                .WithMetadata(new SwaggerResponseAttribute(304, type: typeof(ErrorResponseModel)));

        return endpoint;
    }
}
```

following is an example of how to use these extension Methods:
```
app.MapGet(“/books”, async (BooksDB db) =>
{
    return await db.Books.ToListAsync()
})
.Authorize(policy : "AdminOnly", roles : new string[] {1,2}, schemes: "Bearer")
.AddMetaData<List<Book>>
(
    tag : "Books",
    summary : "Get All Books",
    description : "Get All Books in List, Roles Allowed => Admin Only"
);
```

The `.AddMetaData<T>` here represents the response model for success, this will also add to the response documentation of this endpoint. In this case it is `List<Book>`.


------------
    
    