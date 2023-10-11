
# Post \#58014360 [Link](https://stackoverflow.com/questions/58014360/)

## How do you use Basic Authentication with System.Net.Http.HttpClient?

**Vote**: 58 (113/702) **Views**: 100875 (84/702) 

**Internal ID** \#2-6-412

Created at 2019-09-19 15:25:01

Tags: `c#` `basic-authentication` `dotnet-httpclient`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I'm trying to implement a rest client in c# .net core that needs to first do Basic Authentication, then leverage a Bearer token in subsequent requests. 

When I try to do Basic Authentication in combination with client.PostAsync with a FormUrlEncodedContent object, I'm getting an exception: 

```
System.InvalidOperationException occurred in System.Net.Http.dll: 'Misused header name. Make sure request headers are used with HttpRequestMessage, response headers with HttpResponseMessage, and content headers with HttpContent objects.'
```


```
//setup reusable http client
HttpClient client = new HttpClient();
Uri baseUri = new Uri(url);
client.BaseAddress = baseUri;
client.DefaultRequestHeaders.Clear();
client.DefaultRequestHeaders.ConnectionClose = true;

//Post body content
var values = new List<KeyValuePair<string,string>>();
values.Add(new KeyValuePair<string, string>("grant_type", "client_credentials"));

var content = new FormUrlEncodedContent(values);

//Basic Authentication
var authenticationString = $"{clientId}:{clientSecret}";
var base64EncodedAuthenticationString = Convert.ToBase64String(System.Text.ASCIIEncoding.ASCII.GetBytes(authenticationString));
content.Headers.Add("Authorization", $"Basic {base64EncodedAuthenticationString}");

//make the request
var task = client.PostAsync("/oauth2/token",content);
var response = task.Result;
response.EnsureSuccessStatusCode();
string responseBody = response.Content.ReadAsStringAsync().Result;
Console.WriteLine(responseBody);
```


```
Exception has occurred: CLR/System.InvalidOperationException
An unhandled exception of type 'System.InvalidOperationException' occurred in System.Net.Http.dll: 'Misused header name. Make sure request headers are used with HttpRequestMessage, response headers with HttpResponseMessage, and content headers with HttpContent objects.'
   at System.Net.Http.Headers.HttpHeaders.GetHeaderDescriptor(String name)
   at System.Net.Http.Headers.HttpHeaders.Add(String name, String value)
```



----------
        
## Answer \#0

**Accepted** Vote: 76

Created at 2019-09-19 16:07:53

------------

It looks like you can't use PostAsync and have access to mess with the Headers for authentication. I had to use an HttpRequestMessage and SendAsync.

```
//setup reusable http client
HttpClient client = new HttpClient();
Uri baseUri = new Uri(url);
client.BaseAddress = baseUri;
client.DefaultRequestHeaders.Clear();
client.DefaultRequestHeaders.ConnectionClose = true;

//Post body content
var values = new List<KeyValuePair<string, string>>();
values.Add(new KeyValuePair<string, string>("grant_type", "client_credentials"));
var content = new FormUrlEncodedContent(values);

var authenticationString = $"{clientId}:{clientSecret}";
var base64EncodedAuthenticationString = Convert.ToBase64String(System.Text.ASCIIEncoding.ASCII.GetBytes(authenticationString));

var requestMessage = new HttpRequestMessage(HttpMethod.Post, "/oauth2/token");
requestMessage.Headers.Authorization = new AuthenticationHeaderValue("Basic", base64EncodedAuthenticationString);
requestMessage.Content = content;

//make the request
var task = client.SendAsync(requestMessage);
var response = task.Result;
response.EnsureSuccessStatusCode();
string responseBody = response.Content.ReadAsStringAsync().Result;
Console.WriteLine(responseBody);
```



------------
    
    
## Answer \#1

 Vote: 27

Created at 2021-07-20 12:17:54

------------

It's not a good practice to create HttpClients explicitly from your calling code.
Please use [HttpClientFactory](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/http-requests?view=aspnetcore-5.0) that simplifies a lot of things.
However, if you want to use basic authentication, just create an [HttpRequestMessage](https://learn.microsoft.com/en-us/dotnet/api/system.net.http.httprequestmessage?view=net-5.0) and add the following header:
```
var request = new HttpRequestMessage(HttpMethod.Post, getPath)
{
    Content = new FormUrlEncodedContent(values)
};
request.Headers.Authorization = new BasicAuthenticationHeaderValue("username", "password");
// other settings
```

If you decide to use a recommended IHttpClientFactory it's even simpler:
```
serviceCollection.AddHttpClient(c =>
{
   c.BaseAddress = new Uri("your base url");
   c.SetBasicAuthentication("username", "password");
})
```



------------
    
    
## Answer \#2

 Vote: 24

Created at 2019-09-19 15:35:34

------------

Don't encode the whole authentication string - encode the "Username:Password" expression and append the result to the "Basic " prefix.

```
var authenticationString = $"{clientId}:{clientSecret}";
var base64EncodedAuthenticationString = Convert.ToBase64String(System.Text.ASCIIEncoding.UTF8.GetBytes(authenticationString));
content.Headers.Add("Authorization", "Basic " + base64EncodedAuthenticationString);
```


Also, consider using just ASCII encoding - the UTF8 may not be understood by the server unless you add a `charset` declaration to the header.

[Wikipedia](https://en.wikipedia.org/wiki/Basic_access_authentication) seems to cover this quite well.


------------
    
    
## Answer \#3

 Vote: 7

Created at 2021-05-28 14:17:31

------------

The specific problem is this line (below)
```
content.Headers.Add("Authorization", $"Basic {base64EncodedAuthenticationString}");
```

This fails because `HttpContent.Headers` (`System.Net.Http.Headers.HttpContentHeaders`) is only for headers that are content-specific, such as `Content-Type`, `Content-Length`, and so on.
You've stated that you can't use `DefaultRequestHeaders` because you only need it for a single request - but you also can't use it with `PostAsync` - only `SendAsync` provided you construct the `HttpRequestMessage` yourself, [as per your own answer](https://stackoverflow.com/a/58015049/159145) [and @NeilMoss' answer](https://stackoverflow.com/a/58014537/159145) - but you could use an extension-method in future.
But for the benefit of other readers, another alternative is to add a new extension method based on the existing `PostAsync`, which is actually  ([only 3 lines!](https://github.com/microsoft/referencesource/blob/5697c29004a34d80acdaf5742d7e699022c64ecd/System/net/System/Net/Http/HttpClient.cs#L293)):
```
public Task<HttpResponseMessage> PostAsync( this HttpClient httpClient, Uri requestUri, HttpContent content, String basicUserName, String basicPassword, String? challengeCharSet = null, CancellationToken cancellationToken = default )
{
    if( basicUserName.IndexOf(':') > -1 ) throw new ArgumentException( message: "RFC 7617 states that usernames cannot contain colons.", paramName: nameof(basicUserName) );

    HttpRequestMessage httpRequestMessage = new HttpRequestMessage( HttpMethod.Post, requestUri );
    httpRequestMessage.Content = content;

    //

    Encoding encoding = Encoding.ASCII;
    if( challengeCharSet != null )
    {
        try
        {
            encoding = Encoding.GetEncoding( challengeCharSet );
        }
        catch
        {
            encoding = Encoding.ASCII;
        }
    }

    httpRequestMessage.Headers.Authorization = new AuthenticationHeaderValue(
        scheme   : "Basic",
        parameter: Convert.ToBase64String( encoding.GetBytes( userName + ":" + password ) )
    );

    return SendAsync( httpRequestMessage, cancellationToken );
}
```

Usage:
```
HttpClient httpClient = ...

using( HttpResponseMessage response = await httpClient.PostAsync( uri, content, basicUserName: "AzureDiamond", basicPassword: "hunter2" ).ConfigureAwait(false) )
{
    // ...
}
```



------------
    
    
## Answer \#4

 Vote: 5

Created at 2021-07-29 14:39:17

------------

Just something to add that I struggled with, which I only experienced with Basic authentication endpoints. If you add Json as StringContent then it adds a charset=utf-8, this often return a BadRequest 400.
Here is the code I got to fix this: reference:
[https://dzone.com/articles/httpclient-how-to-remove-charset-from-content-type](https://dzone.com/articles/httpclient-how-to-remove-charset-from-content-type)
```
using (var client = new HttpClient())
using (var content = new StringContent(ParseJSON(data), Encoding.Default, "application/json"))
{  
   //Remove UTF-8 Charset causing BadRequest 400
   content.Headers.ContentType.CharSet = "";

   var clientId = "client";
   var clientSecret = "secret";
   var authenticationString = $"{clientId}:{clientSecret}";
   var base64EncodedAuthenticationString = Convert.ToBase64String(System.Text.ASCIIEncoding.UTF8.GetBytes(authenticationString));
   client.DefaultRequestHeaders.TryAddWithoutValidation(authHeader, authorization);

   var response = await client.PostAsync(url, content);
   return response;
}
```



------------
    
    
## Answer \#5

 Vote: 3

Created at 2022-10-26 12:40:34

------------

Using .NET 6, I use the `HttpClient.DefaultRequestHeaders.Authorization` property to set the Authorization header.
```
// This example will send a signing request to the RightSignature API
var api = "https://api.rightsignature.com/public/v2/sending_requests";

// requestJson is the serialized JSON request body
var contentData = new StringContent(requestJson, Encoding.UTF8, "application/json");

// Instantiate client (for testing), use Microsoft's guidelines in production
var client = new HttpClient();

// Use basic auth, the token has already been converted to base64
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", tokenB64);

try
{
    var response = await client.PostAsync(api, contentData);
}
...
```

Good luck!


------------
    
    
## Answer \#6

 Vote: 2

Created at 2022-03-25 10:45:11

------------

I have resolve this by using below code, that serve my purpose also. Added Code for both Get/Post, this will help you. Moreover I have added one more Header key. So to pass extra data to header. Hope that will resolve your issue.
```
class Program {
                  private static readonly string Username = "test";
                  private static readonly string Password = "test@123";
                  static void Main(string[] args) {
                     var response = Login();
                   }
            public static async Task Login() 
             {
                 var anotherKey ="test";
                  HttpClient httpClient = new HttpClient
                {
                    BaseAddress = new Uri("https://google.com/")
                };
    
                httpClient.DefaultRequestHeaders.Add($"Authorization", $"Basic {Base64Encode($"{Username}:{Password}")}");
                httpClient.DefaultRequestHeaders.Add($"anotherKey", $"{anotherKey}");
                HttpResponseMessage httpResponseMessage = await httpClient.GetAsync("user/123").ConfigureAwait(false);
// For Get Method
            var response= await httpResponseMessage.Content.ReadAsStringAsync().ConfigureAwait(false);
// For Post Method
 User user = new User (1,"ABC");
            HttpResponseMessage httpResponseMessage = await httpClient.PostAsJsonAsync("/post", user).ConfigureAwait(false);
            UserDetail userDetail  = await httpResponseMessage.Content.ReadAsAsync<UserDetail>().ConfigureAwait(false);
        }
            
  }
```



------------
    
    