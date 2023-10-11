
# Post \#42360139 [Link](https://stackoverflow.com/questions/42360139/)

## ASP.NET Core return JSON with status code

**Vote**: 225 (16/702) **Views**: 502783 (6/702) 

**Internal ID** \#2-6-403

Created at 2017-02-21 06:26:47

Tags: `c#` `json` `asp.net-core` `asp.net-core-webapi`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#` `json`)

----------

**Notepad**


----------

I'm looking for the correct way to return JSON with a HTTP status code in my .NET Core Web API controller. I use to use it like this:

```
public IHttpActionResult GetResourceData()
{
    return this.Content(HttpStatusCode.OK, new { response = "Hello"});
}
```


This was in a 4.6 MVC application but now with .NET Core I don't seem to have this `IHttpActionResult` I have `ActionResult` and using like this:

```
public ActionResult IsAuthenticated()
{
    return Ok(Json("123"));
}
```


But the response from the server is weird, as in the image below:

[](https://i.stack.imgur.com/DpSlo.png)

I just want the Web API controller to return JSON with a HTTP status code like I did in Web API 2.


----------
        
## Answer \#0

**Accepted** Vote: 258

Created at 2017-02-21 06:43:58

------------

The most basic version responding with a `JsonResult` is:

```
// GET: api/authors
[HttpGet]
public JsonResult Get()
{
    return Json(_authorRepository.List());
}
```


However, this isn't going to help with your issue because you can't explicitly deal with your own response code.

> The way to get control over the status results, is you need to return a `ActionResult` which is where you can then take advantage of the `StatusCodeResult` type.

for example:

```
// GET: api/authors/search?namelike=foo
[HttpGet("Search")]
public IActionResult Search(string namelike)
{
    var result = _authorRepository.GetByNameSubstring(namelike);
    if (!result.Any())
    {
        return NotFound(namelike);
    }
    return Ok(result);
}
```


Note both of these above examples came from a great guide available from Microsoft Documentation: [Formatting Response Data](https://learn.microsoft.com/en-us/aspnet/core/mvc/models/formatting)


---




## Extra Stuff



The issue I come across quite often is that I wanted more granular control over my WebAPI rather than just go with the defaults configuration from the "New Project" template in VS.

Let's make sure you have some of the basics down...


## Step 1: Configure your Service



In order to get your ASP.NET Core WebAPI to respond with a JSON Serialized Object along full control of the status code, you should start off by making sure that you have included the `AddMvc()` service in your `ConfigureServices` method usually found in `Startup.cs`.

> It's important to note that`AddMvc()` will automatically include the Input/Output Formatter for JSON along with responding to other request types.

If your project requires  and you want to strictly define your services, such as how your WebAPI will behave to various request types including `application/json` and not respond to other request types (such as a standard browser request), you can define it manually with the following code:

```
public void ConfigureServices(IServiceCollection services)
{
    // Build a customized MVC implementation, without using the default AddMvc(), instead use AddMvcCore().
    // https://github.com/aspnet/Mvc/blob/dev/src/Microsoft.AspNetCore.Mvc/MvcServiceCollectionExtensions.cs

    services
        .AddMvcCore(options =>
        {
            options.RequireHttpsPermanent = true; // does not affect api requests
            options.RespectBrowserAcceptHeader = true; // false by default
            //options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();

            //remove these two below, but added so you know where to place them...
            options.OutputFormatters.Add(new YourCustomOutputFormatter()); 
            options.InputFormatters.Add(new YourCustomInputFormatter());
        })
        //.AddApiExplorer()
        //.AddAuthorization()
        .AddFormatterMappings()
        //.AddCacheTagHelper()
        //.AddDataAnnotations()
        //.AddCors()
        .AddJsonFormatters(); // JSON, or you can build your own custom one (above)
}
```


You will notice that I have also included a way for you to add your own custom Input/Output formatters, in the event you may want to respond to another serialization format (protobuf, thrift, etc).

The chunk of code above is mostly a duplicate of the `AddMvc()` method. However, we are implementing each "default" service on our own by defining each and every service instead of going with the pre-shipped one with the template. I have added the repository link in the code block, or you can check out `AddMvc()` [from the GitHub repository.](https://github.com/aspnet/Mvc/blob/dev/src/Microsoft.AspNetCore.Mvc/MvcServiceCollectionExtensions.cs).




---




## Step 2: Create a Controller



I'm going to show you a really straight-forward one just to get your question sorted.

```
public class FooController
{
    [HttpPost]
    public async Task<IActionResult> Create([FromBody] Object item)
    {
        if (item == null) return BadRequest();

        var newItem = new Object(); // create the object to return
        if (newItem != null) return Ok(newItem);

        else return NotFound();
    }
}
```



---




## Step 3: Check your Content-Type and Accept



You need to make sure that your `Content-Type` and `Accept` headers in your  are set properly. In your case (JSON), you will want to set it up to be `application/json`.

If you want your WebAPI to respond as JSON as default, regardless of what the request header is specifying you can do that in a .


As shown in the article I recommended earlier ([Formatting Response Data](https://learn.microsoft.com/en-us/aspnet/core/mvc/models/formatting)) you could force a particular format at the Controller/Action level. I personally don't like this approach... but here it is for completeness:

>  If you would like to restrict the response formats for a specific action you can, you can apply the
  [Produces] filter. The [Produces] filter specifies the response
  formats for a specific action (or controller). Like most Filters, this
  can be applied at the action, controller, or global scope.```
[Produces("application/json")]
public class AuthorsController
```
The `[Produces]` filter will force all actions within the
  `AuthorsController` to return JSON-formatted responses, even if other
  formatters were configured for the application and the client provided
  an `Accept` header requesting a different, available format.


My preferred method is for the WebAPI to respond to all requests with the format requested. However, in the event that it doesn't accept the requested format, then  to a default (ie. JSON)

First, you'll need to register that in your options (we need to rework the default behavior, as noted earlier)

```
options.RespectBrowserAcceptHeader = true; // false by default
```


Finally, by simply re-ordering the list of the formatters that were defined in the services builder, the web host will default to the formatter you position at the top of the list (ie position 0).

More information can be found in this [.NET Web Development and Tools Blog entry](https://blogs.msdn.microsoft.com/webdev/2014/11/24/content-negotiation-in-mvc-6-or-how-can-i-just-write-json/)


------------
    
    
## Answer \#1

 Vote: 76

Created at 2017-02-21 07:04:59

------------

You have predefined methods for most common status codes. 

- `Ok(result)``200`- `CreatedAtRoute``201`- `NotFound``404`- `BadRequest``400`

See [BaseController.cs](http://github.com/aspnet/Mvc/blob/rel/1.1.1/src/Microsoft.AspNetCore.Mvc.Core/ControllerBase.cs) and [Controller.cs](https://github.com/aspnet/Mvc/blob/rel/1.1.1/src/Microsoft.AspNetCore.Mvc.ViewFeatures/Controller.cs) for a list of all methods. 

But if you really insist you can use `StatusCode` to set a custom code, but you really shouldn't as it makes code less readable and you'll have to repeat code to set headers (like for `CreatedAtRoute`). 

```
public ActionResult IsAuthenticated()
{
    return StatusCode(200, "123");
}
```



------------
    
    
## Answer \#2

 Vote: 68

Created at 2018-02-02 07:59:07

------------

With , the ideal way to return object from `Web API` (which is unified with MVC and uses same base class `Controller`) is

```
public IActionResult Get()
{
    return new OkObjectResult(new Item { Id = 123, Name = "Hero" });
}
```


Notice that


1. It returns with 200 OK status code (it's an Ok type of ObjectResult)
2. It does content negotiation, i.e. it'll return based on Accept header in request. If Accept: application/xml is sent in request, it'll return as XML. If nothing is sent, JSON is default.



If it needs to send , use `ObjectResult` or `StatusCode` instead. Both does the same thing, and supports content negotiation. 

```
return new ObjectResult(new Item { Id = 123, Name = "Hero" }) { StatusCode = 200 };
return StatusCode( 200, new Item { Id = 123, Name = "Hero" });
```


or even more fine grained with ObjectResult:

```
Microsoft.AspNetCore.Mvc.Formatters.MediaTypeCollection myContentTypes = new Microsoft.AspNetCore.Mvc.Formatters.MediaTypeCollection { System.Net.Mime.MediaTypeNames.Application.Json };
 String hardCodedJson = "{\"Id\":\"123\",\"DateOfRegistration\":\"2012-10-21T00:00:00+05:30\",\"Status\":0}";
 return new ObjectResult(hardCodedJson) { StatusCode = 200, ContentTypes = myContentTypes };
```


If you specifically want to , there are couple of ways

```
//GET http://example.com/api/test/asjson
[HttpGet("AsJson")]
public JsonResult GetAsJson()
{
    return Json(new Item { Id = 123, Name = "Hero" });
}

//GET http://example.com/api/test/withproduces
[HttpGet("WithProduces")]
[Produces("application/json")]
public Item GetWithProduces()
{
    return new Item { Id = 123, Name = "Hero" };
}
```


Notice that


1. Both enforces JSON in two different ways.
2. Both ignores content negotiation.
3. First method enforces JSON with specific serializer Json(object).
4. Second method does the same by using Produces() attribute (which is a ResultFilter) with contentType = application/json



Read more about them in [the official docs](https://learn.microsoft.com/en-us/aspnet/core/mvc/models/formatting). Learn about [filters here](https://learn.microsoft.com/en-us/aspnet/core/mvc/controllers/filters).

The simple model class that is used in the samples

```
public class Item
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```



------------
    
    
## Answer \#3

 Vote: 43

Created at 2017-12-08 14:58:52

------------

The easiest way I came up with is :

```
var result = new Item { Id = 123, Name = "Hero" };

return new JsonResult(result)
{
    StatusCode = StatusCodes.Status201Created // Status code here 
};
```



------------
    
    
## Answer \#4

 Vote: 20

Created at 2018-06-15 10:15:15

------------

This is my easiest solution:

```
public IActionResult InfoTag()
{
    return Ok(new {name = "Fabio", age = 42, gender = "M"});
}
```


or

```
public IActionResult InfoTag()
{
    return Json(new {name = "Fabio", age = 42, gender = "M"});
}
```



------------
    
    
## Answer \#5

 Vote: 5

Created at 2019-05-21 15:53:37

------------

Awesome answers I found here and I also tried this return statement see `StatusCode(whatever code you wish)` and it worked!!!

```
return Ok(new {
                    Token = new JwtSecurityTokenHandler().WriteToken(token),
                    Expiration = token.ValidTo,
                    username = user.FullName,
                    StatusCode = StatusCode(200)
                });
```



------------
    
    
## Answer \#6

 Vote: 4

Created at 2019-02-03 03:22:55

------------

Instead of using 404/201 status codes using enum 

```
public async Task<IActionResult> Login(string email, string password)
    {
        if (string.IsNullOrWhiteSpace(email) || string.IsNullOrWhiteSpace(password))
        { 
            return StatusCode((int)HttpStatusCode.BadRequest, Json("email or password is null")); 
        }

        var user = await _userManager.FindByEmailAsync(email);
        if (user == null)
        {
            return StatusCode((int)HttpStatusCode.BadRequest, Json("Invalid Login and/or password"));

        }
        var passwordSignInResult = await _signInManager.PasswordSignInAsync(user, password, isPersistent: true, lockoutOnFailure: false);
        if (!passwordSignInResult.Succeeded)
        {
            return StatusCode((int)HttpStatusCode.BadRequest, Json("Invalid Login and/or password"));
        }
        return StatusCode((int)HttpStatusCode.OK, Json("Sucess !!!"));
    }
```



------------
    
    
## Answer \#7

 Vote: 1

Created at 2020-09-05 05:01:06

------------

Controller action return types in ASP.NET Core web API
02/03/2020
6 minutes to read
+2
By Scott Addie [Link](https://learn.microsoft.com/en-us/aspnet/core/web-api/action-return-types?view=aspnetcore-3.1#synchronous-action-1)
[Synchronous action](https://learn.microsoft.com/en-us/aspnet/core/web-api/action-return-types?view=aspnetcore-3.1#synchronous-action-1)
```
[HttpGet("{id}")]
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
public ActionResult<Product> GetById(int id)
{
    if (!_repository.TryGetProduct(id, out var product))
    {
        return NotFound();
    }

    return product;
}
```

[Asynchronous action](https://learn.microsoft.com/en-us/aspnet/core/web-api/action-return-types?view=aspnetcore-3.1#asynchronous-action-1)
```
[HttpPost]
[Consumes(MediaTypeNames.Application.Json)]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public async Task<ActionResult<Product>> CreateAsync(Product product)
{
    if (product.Description.Contains("XYZ Widget"))
    {
        return BadRequest();
    }

    await _repository.AddProductAsync(product);

    return CreatedAtAction(nameof(GetById), new { id = product.Id }, product);
}
```



------------
    
    
## Answer \#8

 Vote: 0

Created at 2017-02-21 07:18:12

------------

Please refer below code, You can manage multiple status code with different type JSON 

```
public async Task<HttpResponseMessage> GetAsync()
{
    try
    {
        using (var entities = new DbEntities())
        {
            var resourceModelList = entities.Resources.Select(r=> new ResourceModel{Build Your Resource Model}).ToList();

            if (resourceModelList.Count == 0)
            {
                return this.Request.CreateResponse<string>(HttpStatusCode.NotFound, "No resources found.");
            }

            return this.Request.CreateResponse<List<ResourceModel>>(HttpStatusCode.OK, resourceModelList, "application/json");
        }
    }
    catch (Exception ex)
    {
        return this.Request.CreateResponse<string>(HttpStatusCode.InternalServerError, "Something went wrong.");
    }
}
```



------------
    
    
## Answer \#9

 Vote: 0

Created at 2019-03-09 19:41:42

------------

What I do in my Asp Net Core Api applications it is to create a class that extends from ObjectResult and provide many constructors to customize the content and the status code.
Then all my Controller actions use one of the costructors as appropiate.
You can take a look at my implementation at:
[https://github.com/melardev/AspNetCoreApiPaginatedCrud](https://github.com/melardev/AspNetCoreApiPaginatedCrud)

and

[https://github.com/melardev/ApiAspCoreEcommerce](https://github.com/melardev/ApiAspCoreEcommerce)

here is how the class looks like(go to my repo for full code):

```
public class StatusCodeAndDtoWrapper : ObjectResult
{



    public StatusCodeAndDtoWrapper(AppResponse dto, int statusCode = 200) : base(dto)
    {
        StatusCode = statusCode;
    }

    private StatusCodeAndDtoWrapper(AppResponse dto, int statusCode, string message) : base(dto)
    {
        StatusCode = statusCode;
        if (dto.FullMessages == null)
            dto.FullMessages = new List<string>(1);
        dto.FullMessages.Add(message);
    }

    private StatusCodeAndDtoWrapper(AppResponse dto, int statusCode, ICollection<string> messages) : base(dto)
    {
        StatusCode = statusCode;
        dto.FullMessages = messages;
    }
}
```


Notice the base(dto) you replace dto by your object and you should be good to go.


------------
    
    
## Answer \#10

 Vote: 0

Created at 2020-03-09 18:59:29

------------

I got this to work.  My big issue was my json was a string (in my database...and not a specific/known Type).

Ok, I finally got this to work.

```
////[Route("api/[controller]")]
////[ApiController]
////public class MyController: Microsoft.AspNetCore.Mvc.ControllerBase
////{
                    //// public IActionResult MyMethod(string myParam) {

                    string hardCodedJson = "{}";
                    int hardCodedStatusCode = 200;

                    Newtonsoft.Json.Linq.JObject job = Newtonsoft.Json.Linq.JObject.Parse(hardCodedJson);
                    /* "this" comes from your class being a subclass of Microsoft.AspNetCore.Mvc.ControllerBase */
                    Microsoft.AspNetCore.Mvc.ContentResult contRes = this.Content(job.ToString());
                    contRes.StatusCode = hardCodedStatusCode;

                    return contRes;

                    //// } ////end MyMethod
              //// } ////end class
```


I happen to be on asp.net core 3.1

```
#region Assembly Microsoft.AspNetCore.Mvc.Core, Version=3.1.0.0, Culture=neutral, PublicKeyToken=adb9793829ddae60
//C:\Program Files\dotnet\packs\Microsoft.AspNetCore.App.Ref\3.1.0\ref\netcoreapp3.1\Microsoft.AspNetCore.Mvc.Core.dll
```


I got the hint from here  ::   [https://www.jianshu.com/p/7b3e92c42b61](https://www.jianshu.com/p/7b3e92c42b61)


------------
    
    
## Answer \#11

 Vote: 0

Created at 2021-02-22 18:46:46

------------

The cleanest solution I have found is to set the following in my ConfigureServices method in Startup.cs (In my case I want the TZ info stripped. I always want to see the date time as the user saw it).
```
services.AddControllers()
                .AddNewtonsoftJson(o =>
                {
                    o.SerializerSettings.DateTimeZoneHandling = DateTimeZoneHandling.Unspecified;
                });
```

The DateTimeZoneHandling options are Utc, Unspecified, Local or RoundtripKind
I would still like to find a way to be able to request this on a per-call bases.
something like
```
static readonly JsonMediaTypeFormatter _jsonFormatter = new JsonMediaTypeFormatter();
 _jsonFormatter.SerializerSettings = new JsonSerializerSettings()
                {DateTimeZoneHandling = DateTimeZoneHandling.Unspecified};

return Ok("Hello World", _jsonFormatter );
```

I am converting from ASP.NET and there I used the following helper method
```
public static ActionResult<T> Ok<T>(T result, HttpContext context)
    {
        var responseMessage = context.GetHttpRequestMessage().CreateResponse(HttpStatusCode.OK, result, _jsonFormatter);
        return new ResponseMessageResult(responseMessage);
    }
```



------------
    
    