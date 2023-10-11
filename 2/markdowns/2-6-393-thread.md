
# Post \#69056652 [Link](https://stackoverflow.com/questions/69056652/)

## Why am I getting Synchronous reads are not supported in ImageSharp?

**Vote**: 2 (631/702) **Views**: 1940 (609/702) 

**Internal ID** \#2-6-393

Created at 2021-09-04 15:24:38

Tags: `c#` `asp.net` `blazor` `blazor-webassembly` `imagesharp`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I'm trying to use a Blazor input file, and also the Imagesharp library, to turn an IBrowserFile into an image.
My method looks like this
```
public async Task<byte[]> ConvertFileToByteArrayAsync(IBrowserFile file)
        {

            using var image = Image.Load(file.OpenReadStream());
            image.Mutate(x => x.Resize(new ResizeOptions
            {
                Mode = ResizeMode.Min,
                Size = new Size(128)
            }));

            MemoryStream memoryStream = new MemoryStream();
            if (file.ContentType == "image/png")
            {

                await image.SaveAsPngAsync(memoryStream);
            }
            else
            {
                await image.SaveAsJpegAsync(memoryStream);
            }
            var byteFile = memoryStream.ToArray();
            memoryStream.Close();
            memoryStream.Dispose();


            return byteFile;
            
        }
```

But I'm getting the following error  :
```
crit: Microsoft.AspNetCore.Components.WebAssembly.Rendering.WebAssemblyRenderer[100]
      Unhandled exception rendering component: Synchronous reads are not supported.
System.NotSupportedException: Synchronous reads are not supported.
   at Microsoft.AspNetCore.Components.Forms.BrowserFileStream.Read(Byte[] buffer, Int32 offset, Int32 count)
   at System.IO.Stream.CopyTo(Stream destination, Int32 bufferSize)
   at SixLabors.ImageSharp.Image.WithSeekableStream[ValueTuple`2](Configuration configuration, Stream stream, Func`2 action)
   at SixLabors.ImageSharp.Image.Load(Configuration configuration, Stream stream, IImageFormat& format)
   at SixLabors.ImageSharp.Image.Load(Configuration configuration, Stream stream)
   at SixLabors.ImageSharp.Image.Load(Stream stream)
   at MasterMealWA.Client.Services.FileService.ConvertFileToByteArrayAsync(IBrowserFile file) in F:\CoderFoundry\Code\MasterMealWA\MasterMealWA\Client\Services\FileService.cs:line 37
   at MasterMealWA.Client.Pages.RecipePages.RecipeCreate.CreateRecipeAsync() in F:\CoderFoundry\Code\MasterMealWA\MasterMealWA\Client\Pages\RecipePages\RecipeCreate.razor:line 128
   at Microsoft.AspNetCore.Components.ComponentBase.CallStateHasChangedOnAsyncCompletion(Task task)
   at Microsoft.AspNetCore.Components.Forms.EditForm.HandleSubmitAsync()
   at Microsoft.AspNetCore.Components.ComponentBase.CallStateHasChangedOnAsyncCompletion(Task task)
   at Microsoft.AspNetCore.Components.RenderTree.Renderer.GetErrorHandledTask(Task taskToHandle)
```

for the record, line 37 is "using var image......",  I don't quite see where I'm using multiple streams, unless its the read stream and memory stream.  however, I also don't see how to close the stream I open with file.OpenReadStream.


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2021-09-04 17:07:28

------------


## Background:


- ASP.NET Core 3+ discourages devs from using non-async IO by having all `Stream` objects handled by ASP.NET Core (such as `HttpRequest.Body` and `IBrowserFile.OpenReadStream()`) throw an exception whenever the non-async `Stream.Read` and `Stream.Write` methods are called.- [https://github.com/dotnet/aspnetcore/issues/7644](https://github.com/dotnet/aspnetcore/issues/7644)- [ASP.NET Core : Synchronous operations are disallowed. Call WriteAsync or set AllowSynchronousIO to true instead](https://stackoverflow.com/questions/47735133/asp-net-core-synchronous-operations-are-disallowed-call-writeasync-or-set-all)- [This behaviour is controlled by IHttpBodyControlFeature - so you can re-enable synchronous stream IO if you absolutely have to](https://stackoverflow.com/a/60985016/159145)- Also, you don't need the intermediate `MemoryStream`: you can write the ImageSharp output directly to the response.

## Proper solution:


You're calling ImageSharp's `Image.Load` method, which uses non-async `Stream` methods. The fix is to simply use `await Image.LoadAsync` instead:
So change your code to this:
```
// I assume this is a Controller Action method
// This method does not return an IActionResult because it writes directly to the response in the action method. See examples here: https://stackoverflow.com/questions/42771409/how-to-stream-with-asp-net-core

public async Task ResizeImageAsync( IBrowserFile file )
{
    await using( Stream stream = file.OpenReadStream() )
    using( Image image = await Image.LoadAsync( stream ) )
    {
        ResizeOptions ro = new ResizeOptions
        {
            Mode = ResizeMode.Min,
            Size = new Size(128)
        };

        image.Mutate( img => img.Resize( ro ) );

        if( file.ContentType == "image/png" ) // <-- You should not do this: *never trust* the client to be correct and truthful about uploaded files' types and contents. In this case it's just images so it's not that big a deal, but always verify independently server-side.
        {
            this.Response.ContentType = "image/png";
            await image.SaveAsPngAsync( this.Response.Body );
        }
        else
        {
            this.Response.ContentType = "image/jpeg";
            await image.SaveAsJpegAsync( this.Response.Body );
        }
}
```


## Alternative (non-)solution: Procrastinate:


Just disable ASP.NET Core's prohibition on non-async IO:
```
public void ConfigureServices(IServiceCollection services)
{
    // If using Kestrel:
    services.Configure<KestrelServerOptions>(options =>
    {
        options.AllowSynchronousIO = true;
    });

    // If using IIS:
    services.Configure<IISServerOptions>(options =>
    {
        options.AllowSynchronousIO = true;
    });
}
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-09-04 17:03:56

------------

`Image.Load` is a synchronous operation. Try with the async version instead:
```
using var image = await Image.LoadAsync(file.OpenReadStream());
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-09-04 16:35:36

------------

You have to call async methods, like `LoadAsync`, `DisposeAsync()` instead of the synchronous `Dispose()`. Use `await using xxx` to await the call to `DisposeAsync()`.
```
public async Task<byte[]> ConvertFileToByteArrayAsync(IBrowserFile file)
{
    await using var image = await image.LoadAsync(file.OpenReadStream());
    image.Mutate(x => x.Resize(new ResizeOptions
    {
        Mode = ResizeMode.Min,
        Size = new Size(128)
    }));

    MemoryStream memoryStream = new MemoryStream();
    if (file.ContentType == "image/png")
    {

        await image.SaveAsPngAsync(memoryStream);
    }
    else
    {
        await image.SaveAsJpegAsync(memoryStream);
    }
    var byteFile = memoryStream.ToArray();
    memoryStream.Close();
    await memoryStream.DisposeAsync();

    return byteFile;
}
```



------------
    
    