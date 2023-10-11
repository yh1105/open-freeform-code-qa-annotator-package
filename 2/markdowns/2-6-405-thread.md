
# Post \#59714992 [Link](https://stackoverflow.com/questions/59714992/)

## Why asp.net core sending empty object as response?

**Vote**: 17 (290/702) **Views**: 12128 (392/702) 

**Internal ID** \#2-6-405

Created at 2020-01-13 10:41:07

Tags: `c#` `angular` `asp.net-core` `asp.net-core-webapi`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#` `javascript`)

----------

**Notepad**


----------

When I debug the code in VS, the cities list, which I am returning have 3 objects in it along with the properties. When I call this endpoint I am receiving a response of 3 list items of empty objects. 

How to resolve this issue?


# Model Class:



```
public class City
{
    public string CityName;
    public string AssociatedCities; 
    public string Province;
    public int Status;

    public City(string cityName, string associatedCities, string province, int status)
    {
        this.CityName = cityName;
        this.AssociatedCities = associatedCities;
        this.Province = province;
        this.Status = status;
    }
}
```



# Endpoint:



```
[HttpGet]
[Route("cities")]
public ActionResult<IEnumerable<City>> GetCities()
{
    return Ok(Cities);
}
```


This is how I am calling the endpoint

```
getCities() {
  this.http.get<City[]>('/api/wizard/cities')
  .subscribe(result => {
    console.log(result);
    this.cities = result;
  }, error => console.error('Something went wrong : ' + error));
}
```


The response I get:
[](https://i.stack.imgur.com/zlyrW.png)

The response that is needed:

```
[
  {
    "SearchCity": "Toronto",
    "AssociatedCities": "Ajax, Whitby, Toronto, Mississauga, Brampton",
    "Province": "ON",
    "Status": 1
  },
  {
    "SearchCity": "Vancouver",
    "AssociatedCities": "Vancouver, Vancouver City",
    "Province": "BC",
    "Status": 1
  }
]
```


I have tried this already: [Fresh ASP.NET Core API returns empty JSON objects](https://stackoverflow.com/questions/44318390/fresh-asp-net-core-api-returns-empty-json-objects)


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2020-01-13 14:00:58

------------

Based on the fact that all your action does is return `Cities`, which presumably is a property or field defined on your controller, I'm going to take a shot in the dark and assume that you're setting that in another request and expecting it to still be there in this request. That's not how it works. The controller is instantiated and disposed with each request, so anything set to it during the lifetime of a request will not survive. As a result, `Cities` has nothing in this request, so you get an empty response.

If you need a list of cities in the action, then you should query those . Also, for what it's worth, System.Text.Json does not currently support serializing fields, as others have mentioned in the comments, but you may still use JSON.NET instead, which does. See: [https://learn.microsoft.com/en-us/aspnet/core/migration/22-to-30?view=aspnetcore-3.1&tabs=visual-studio#jsonnet-support](https://learn.microsoft.com/en-us/aspnet/core/migration/22-to-30?view=aspnetcore-3.1&tabs=visual-studio#jsonnet-support)


------------
    
    
## Answer \#1

 Vote: 17

Created at 2020-01-15 05:31:06

------------

`System.Text.Json` currently does not support serialization/deserialization of fields and non-parameter-less, non-default constructors.

Your example model uses both  and a . If you need to use a custom constructor for some reason, you would need to implement your own `JsonConverter<T>` to support that. This doc might be helpful for that:
[https://learn.microsoft.com/en-us/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to#deserialize-to-immutable-classes-and-structs](https://learn.microsoft.com/en-us/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to#deserialize-to-immutable-classes-and-structs)

Only  with public getters/setters are supported along with the  (what is referred to as [Plain_old_CLR_object (POCO)](https://en.wikipedia.org/wiki/Plain_old_CLR_object)). Note: If you are only serializing (i.e. writing), the setters generally don't have to be public.

[Properties](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/properties) are different from fields (and contain getters/setters).

Here is the fix:

```
public class City
{
    public string CityName { get; set; }
    public string AssociatedCities { get; set; }
    public string Province { get; set; }
    public int Status { get; set; }
}
```



------------
    
    
## Answer \#2

 Vote: 9

Created at 2020-12-16 13:48:39

------------

In my case, I just added this in my ConfigureServices method in Startup.cs (I am using Dot Net 5.0)
```
services.AddControllers().AddNewtonsoftJson();
```



------------
    
    