
# Post \#59236834 [Link](https://stackoverflow.com/questions/59236834/)

## How to disable "Attempting to reconnect to the server" message on ASP.NET Core producton server

**Vote**: 19 (269/702) **Views**: 17066 (333/702) 

**Internal ID** \#2-6-417

Created at 2019-12-08 15:20:00

Tags: `c#` `asp.net-core` `razor-pages` `blazor-server-side` `asp.net-core-3.1`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I have an ASP.NET Core 3.1 C# razor pages application that also uses some Blazor-serverside razor components. I have published it to IIS on Windows 2008 R2 Server.
But when browsing the site in Chrome on one andorid mobile phone a message appears periodically:

> Attemting to reconnect to the server

Also when user stays inactive for a while, e.g. turns off the  mobile phone display, a message appears

> Disconnected from server. Reload page ...

The site is not in english and those generic messages are not good for end user exprience. Is there any way how to disable  those messages, or at least translate them to another language?


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2019-12-13 14:57:42

------------

So far I have only found a way how to disable Blazor overlays on pages that do not contain any serverside Blazor components. It is quite simple, I have created an empty Interface `IPageWithBlazor` and made all models of razor pages that contain serverside Blazor implement this empty interface. Now I can use the following condition in `_Layout.cshtml`:

```
@if (this.Model is IPageWithBlazor)
{
    <script type="text/javascript" src="~/js/blazor.polyfill.min.js"></script>
    <script src="~/_framework/blazor.server.js"></script>
}
```


About translating messages there is [another question that covers the topic](https://stackoverflow.com/questions/58404533/how-can-i-change-the-could-not-reconnect-to-the-server-text-in-blazor).


------------
    
    
## Answer \#1

 Vote: 30

Created at 2019-12-18 15:35:57

------------

There is actually an answer for server-side Blazor as well. According to this: [ASP.NET Core Blazor hosting models](https://learn.microsoft.com/nl-nl/aspnet/core/blazor/hosting-models?view=aspnetcore-3.0), one can define a div-element with the id `components-reconnect-modal` in the body of `_Host.cshtml` to manipulate the overlay that shows up in case of a connection loss. 

That would look something like this:

```
<body>
...
<!-- Blazor overlay -->
<div id="components-reconnect-modal"></div>

<app>
    @(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))
</app>
...
</body>
```


Blazor applies these custom classes depending on the state of the app. According to the documentation these classes are in effect:

- `components-reconnect-show`- `components-reconnect-hide`- `components-reconnect-failed``window.Blazor.reconnect()`- `components-reconnect-rejected``location.reload()`

To hide the overlay completely, for instance you can add this CSS:

```
.components-reconnect-show, .components-reconnect-failed, .components-reconnect-rejected {
     display: none;
}
```


If you want a custom look for the overlay, you can just fill the div in `_Host.cshtml` with content to your liking:

```
<div id="components-reconnect-modal" class="my-reconnect-modal components-reconnect-hide">
<div class="show">
    <p>
        // Message when attempting to connect to server
    </p>
</div>
<div class="failed">
    <p>
        // Message when failing to connect
    </p>
</div>
<div class="rejected">
    <p>
        // Message when refused
    </p>
</div>
```




I have no idea if this works client-side  however, as I only work with server-side Blazor. 
I hope this works for you.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-06-23 16:58:59

------------

I had the same issue and I found this explanation ([https://github.com/dotnet/aspnetcore/issues/19050](https://github.com/dotnet/aspnetcore/issues/19050)) from the Blazor team.
Christiaan Bruin's answer solved my issue, but it changes the reconnect page. I just wanted it not to be fired as the user browses, especially in Firefox. So the way I did is the one suggested on the github issue, [https://github.com/dotnet/aspnetcore/issues/19050#issuecomment-653953887](https://github.com/dotnet/aspnetcore/issues/19050#issuecomment-653953887).
```
<script src="_framework\blazor.server.js"></script>
<script>
  window.addEventListener('beforeunload', function () {
    Blazor.defaultReconnectionHandler._reconnectionDisplay = { };
  });
</script>
```

It worked for the Firefox attempts to reconnect and it showed the message correctly when I actually killed the connection.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2023-02-03 11:54:47

------------

My solution was to completely delete from _Layout.cshtml
```
<script src="~/_framework/blazor.server.js"></script>
```

and only put it on pages that use Blazor, like this
```
@section Scripts {
    <script src="~/_framework/blazor.server.js"></script>
}
```



------------
    
    