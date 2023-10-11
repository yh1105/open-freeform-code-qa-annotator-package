
# Post \#58363023 [Link](https://stackoverflow.com/questions/58363023/)

## how to make Blazor HTTP Get JSON ASYNC request?

**Vote**: 6 (473/702) **Views**: 16725 (337/702) 

**Internal ID** \#2-6-407

Created at 2019-10-13 11:16:48

Tags: `c#` `asp.net-core` `asp.net-web-api` `blazor` `blazor-webassembly`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I am trying to implement a small crud in sample application.

I'm using blazor in an application, but found some problem with server call.

I created a simple API controller with the name of Employee and method with HTTP verbs.

```
[ApiController]
    [Route("[controller]")]
    public class EmployeeController : ControllerBase
    {
        EmployeeRepository objemployee = new EmployeeRepository();   
        [HttpGet]
        [Route("api/Employee/Index")]
        public IEnumerable<Employee> Index()
        {
            return objemployee.GetAllEmployees();
        }
   }
```


The

```
empList = await Http.GetJsonAsync<Employee[]>("/api/Employee/Index");
```


line reports a problem and I have no idea with it since I'm a newbie with blazor. What should I do in my code?

Application built with asp. Net core 3.0", "blazor preview 9".

Output: 
I tried to follow guides and looked up example implementations but could not solve the issue.

I am getting the following exception:

```
WASM: Unhandled exception rendering component:
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM: 
    System.Text.Json.JsonException: 
    '<' is an invalid start of a value. Path: $ | LineNumber: 0 | BytePositionInLine: 0. ---> 
    System.Text.Json.JsonReaderException: '<' is an invalid start of a value. LineNumber: 0 | BytePositionInLine: 0.
d.printErr @ blazor.webassembly.js:1
    blazor.webassembly.js:1 WASM:   at.    System.Text.Json.ThrowHelper.ThrowJsonReaderException (System.Text.Json.Utf8JsonReader& json, System.Text.Json.ExceptionResource resource, System.Byte nextByte, System.ReadOnlySpan`1[T] bytes) <0x2398fc8 + 0x00020> in <81e9245ca982431695a55cc67ffb3b86>:0 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at System.Text.Json.Utf8JsonReader.ConsumeValue (System.Byte marker) <0x1fa7718 + 0x0028e> in <81e9245ca982431695a55cc67ffb3b86>:0 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at System.Text.Json.Utf8JsonReader.ReadFirstToken (System.Byte first) <0x1fa6d60 + 0x001ec> in <81e9245ca982431695a55cc67ffb3b86>:0 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at System.Text.Json.Utf8JsonReader.ReadSingleSegment () <0x1fa6618 + 0x00234> in <81e9245ca982431695a55cc67ffb3b86>:0 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at System.Text.Json.Utf8JsonReader.Read () <0x1fa61d0 + 0x00012> in <81e9245ca982431695a55cc67ffb3b86>:0 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at System.Text.Json.JsonSerializer.ReadCore (System.Text.Json.JsonSerializerOptions options, System.Text.Json.Utf8JsonReader& reader, System.Text.Json.ReadStack& readStack) <0x1fa5b40 + 0x00062> in <81e9245ca982431695a55cc67ffb3b86>:0 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:    --- End of inner exception stack trace ---
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at System.Text.Json.ThrowHelper.ReThrowWithPath (System.Text.Json.ReadStack& readStack, System.Text.Json.JsonReaderException ex) <0x23e6bc8 + 0x00116> in <81e9245ca982431695a55cc67ffb3b86>:0 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at System.Text.Json.JsonSerializer.ReadCore (System.Text.Json.JsonSerializerOptions options, System.Text.Json.Utf8JsonReader& reader, System.Text.Json.ReadStack& readStack) <0x1fa5b40 + 0x002a8> in <81e9245ca982431695a55cc67ffb3b86>:0 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at System.Text.Json.JsonSerializer.ReadCore (System.Type returnType, System.Text.Json.JsonSerializerOptions options, System.Text.Json.Utf8JsonReader& reader) <0x1fa4e70 + 0x0003e> in <81e9245ca982431695a55cc67ffb3b86>:0 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at System.Text.Json.JsonSerializer.ParseCore (System.String json, System.Type returnType, System.Text.Json.JsonSerializerOptions options) <0x1fa1698 + 0x00086> in <81e9245ca982431695a55cc67ffb3b86>:0 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at System.Text.Json.JsonSerializer.Deserialize[TValue] (System.String json, System.Text.Json.JsonSerializerOptions options) <0x2398808 + 0x00022> in <81e9245ca982431695a55cc67ffb3b86>:0 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at Microsoft.AspNetCore.Components.HttpClientJsonExtensions.GetJsonAsync[T] (System.Net.Http.HttpClient httpClient, System.String requestUri) <0x2270e18 + 0x000fa> in <900d091618e14952821fd2fc9b26598c>:0 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at EMS.Client.Pages.FetchEmployee.OnInitializedAsync () [0x0002a] in C:\ES\Client\Pages\FetchEmployee.razor:51 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at Microsoft.AspNetCore.Components.ComponentBase.RunInitAndSetParametersAsync () <0x1f4ed10 + 0x00176> in <cc81133ac6304aada69282c517e2b811>:0 
d.printErr @ blazor.webassembly.js:1
blazor.webassembly.js:1 WASM:   at Microsoft.AspNetCore.Components.RenderTree.Renderer.GetErrorHandledTask (System.Threading.Tasks.Task taskToHandle) <0x224b4e8 + 0x000f4> in <cc81133ac6304aada69282c517e2b811>:0 
 `
```



----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2019-10-13 16:18:53

------------

I don't think this is a Blazor issue, I assume you're using the Blazor WebAssembly Hosted template? 

If you try and load your API endpoint by navigating to it in a browser what do you see? I'm going to guess it's a 404 not found or a 500 error?

I think the issue is how you've defined the route on your API controller. Try the following setup.

```
[ApiController]
[Route("[controller]")]
public class EmployeeController : ControllerBase
{
    EmployeeRepository objemployee = new EmployeeRepository();  

    [HttpGet]
    public IEnumerable<Employee> Index()
    {
        return objemployee.GetAllEmployees().ToArray();
    }
```


}  

Then make a call `employees = await Http.GetJsonAsync<Employee[]>("Employee");`, if all goes well you should get your list of employees back.


------------
    
    
## Answer \#1

 Vote: 4

Created at 2019-10-13 12:39:34

------------

I guess the problem is related to your Web Api which returns Html instead of JSON. The GetJsonAsync method functions well, and the JSON parser too. Perhaps the employee model is faulty... 

Try to use this:

```
[ApiController]
public class EmployeeController: ControllerBase
{
   EmployeeRepository objemployee = new EmployeeRepository();   
        [HttpGet("/")]
        public IEnumerable<Employee> Index()
        {
            return objemployee.GetAllEmployees();
        }

}
```


And call the Web Api like that:

```
empList = await Http.GetJsonAsync<Employee[]>("api/Employee");
```


Note the removal of the leading "/" in the url. This may be the root of the problem.

- - 


------------
    
    
## Answer \#2

 Vote: 2

Created at 2019-10-13 12:40:13

------------

It looks you are requesting bad API point. Just check your end point is returning json and not html: open navigator and go `http://localhost:5000/api/Employee/Index`.

Looking your code, I suggest to you to change:

```
Route("[controller]"...
```


By:

```
Route("/api/[controller]"...
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-02-04 11:04:40

------------

I've had the error
> System.Text.Json.JsonReaderException: '<' is an invalid start of a value
occur several times after I've made valid code changes to the return types of controller methods, only for the error to stop occurring for seemingly no reason, even though no further code changes have been made.
I'm guessing some sort of caching occurs and for a while the Blazor WASM client is looking for a controller endpoint that now expects different parameters or returns a different object type.
I've tried restarting VS, deleting the obj, bin and .vs files etc. which haven't helped, so I'm guessing this may be a Chrome caching issue, or some sort of caching on the debug circuit Blazor WebAssembly creates.
So, if you make these checks:
- `requestUri``[Route("api/[controller]")]``api\`- `requestUri`- `Http.GetFromJsonAsync<YourType>`
and all is as expected, then it may be worth killing Chrome's process or even restarting your PC to see if that clears the exception, before spending too much time chasing what may be a phantom error.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-03-25 12:23:32

------------

The solution is to update all the Nuget Packages to 5.0.4. Also, make sure you update Visual Studio 2019 to the latest version.  Also, you must ensure your certificate works, the self-signed PFX. You do not have to use `dot-net dev-certs`, but you will need one certificate duly registered for HTTPS to function. It is a well-documented procedure on the Web and YouTube. Also, you may want to add logging code into the relevant server-side locations so you can see what's up.
Shown below is the Kestrel HTTPS startup from Console as evidence of my output functionality. It's good and fun to learn - praise to Jesus!
The server side code is now visible and breakpoints are working. This all started working just today, Tuesday, March 25th, 2021, after I updated all the attached Nuget Packages to 5.0.4. See the very bottom for the creation script.
You will see output from various `Console.Writeline` instructions too, annotated as 'First Hit', 'Second Hit', etc. These are ways to view what's up. Logging techniques kick in too, but you'll need to learn how to attach it and output that yourself.
```
..\dotnet watch --verbose run -c Debug
watch : Evaluating dotnet-watch file set.
watch : Running MSBuild target 'GenerateWatchList' on 'C:\Beachgoo.Authority\Server\Authority.Server.csproj'
watch : Started 'C:\Program Files\dotnet\dotnet.exe' 'msbuild /nologo C:\Beachgoo.Authority\Server\Authority.Server.csproj /p:_DotNetWatchListFile=D:\Global_TMP\h4f00m3z.2oq /nologo /v:n /t:GenerateWatchList /p:DotNetWatchBuild=true /p:DesignTimeBuild=true "/p:CustomAfterMicrosoftCommonTargets=C:\Program Files\dotnet\sdk\5.0.201\DotnetTools\dotnet-watch\5.0.201-servicing.21123.24\tools\net5.0\any\DotNetWatch.targets" "/p:CustomAfterMicrosoftCommonCrossTargetingTargets=C:\Program Files\dotnet\sdk\5.0.201\DotnetTools\dotnet-watch\5.0.201-servicing.21123.24\tools\net5.0\any\DotNetWatch.targets"' with process id 10188
watch : Process id 10188 ran for 3629ms
watch : Watching 39 file(s) for changes
watch : Watch command can be configured to use --no-restore.
watch : No restore arguments: run --no-restore -c Debug
watch : dotnet-watch is configured to launch a browser on ASP.NET Core application startup.
watch : Refresh server running at ws://127.0.0.1:23993.
watch : Started 'C:\Program Files\dotnet\dotnet.exe' 'run -c Debug' with process id 9768
watch : Running dotnet with the following arguments: run -c Debug
watch : Started
Building...
First hit: now in Server Program.Main() method of Program.cs
Second hit: IPAddress.Parse
Third hit: now in ConfigureKestrel() => listopt lambda
Certificate Path is: /wwwroot/<yourssc>.pfx, <yourpassword>
Fourth Hit: Added WebAssemblyDebugging in development should be: True
Fifth Hit: Inside the UseEndoints Lambda in Startup.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://192.168.2.3:5000
watch : Launching browser.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://192.168.2.3:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\Beachgoo.Authority\Server
trce: Authority.Server.Controllers.ResortQualitiesController[0]
      Inside constructor for ApiController ResortQualitiesController. This type is: Authority.Server.Controllers.ResortQualitiesController
trce: Authority.Server.Controllers.ResortQualitiesController[0]
      Inside HTTP Get(), on server side Kestrel.
trce: Authority.Server.Controllers.ResortQualitiesController[0]
      Inside constructor for ApiController ResortQualitiesController. This type is: Authority.Server.Controllers.ResortQualitiesController
trce: Authority.Server.Controllers.ResortQualitiesController[0]
      Inside HTTP Get(), on server side Kestrel.
trce: Authority.Server.Controllers.ResortQualitiesController[0]
      Inside constructor for ApiController ResortQualitiesController. This type is: Authority.Server.Controllers.ResortQualitiesController
trce: Authority.Server.Controllers.ResortQualitiesController[0]
      Inside HTTP Get(), on server side Kestrel.
```

The creation Script for me is here. I was playing with persistence too, but you can scratch that.
```
@ECHO OFF
@CLS
@REM Rename / create the word <Trial> as you need.
@REM C:\Trial.bat
@ECHO -------------------------
@ECHO  Trial System Build
@ECHO -------------------------
@ECHO.
C:
CD C:\
@ECHO ------Build WASM---------
dotnet new blazorwasm -lang "C#" -o <your.folder.<Trial> -n <Trial> -f net5.0 -ho -p
@ECHO ----Create Persistence----
cd C:\<yourfolder.<Trial>
dotnet new classlib -lang "C#" -n Persistent -f net5.0
dotnet add .\Persistent\Persistent.csproj package Microsoft.EntityFrameworkCore --version 5.0.4
dotnet add .\Persistent\Persistent.csproj package Microsoft.AspNetCore.Identity.UI --version 5.0.4
dotnet add .\Persistent\Persistent.csproj package Microsoft.Extensions.Configuration --version 5.0.0
dotnet add .\Persistent\Persistent.csproj package Microsoft.EntityFrameworkCore.Tools --version 5.0.4
dotnet add .\Persistent\Persistent.csproj package Microsoft.EntityFrameworkCore.SqlServer --version 5.0.4
dotnet add .\Persistent\Persistent.csproj package Microsoft.AspNetCore.Identity.EntityFrameworkCore --version 5.0.4
dotnet add .\Persistent\Persistent.csproj package Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore --version 5.0.4
@ECHO.
@ECHO -----Combine Projects-----
dotnet sln <Trial>.sln add .\Persistent\Persistent.csproj
@ECHO.
CD \
@ECHO.
@ECHO Done! Completed <Trial> Solution Build.
```



------------
    
    