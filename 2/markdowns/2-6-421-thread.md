
# Post \#58371328 [Link](https://stackoverflow.com/questions/58371328/)

## OnInitializedAsync() in blazor

**Vote**: 13 (343/702) **Views**: 31634 (225/702) 

**Internal ID** \#2-6-421

Created at 2019-10-14 06:41:43

Tags: `c#` `blazor`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I have used `OnInitializedAsync()` in my code. In that hook, I am fetching data.
In markup, I have checked whether data is null or not. But I found that data checked is executed before the `onInitalizedAsync()` triggered. Meanwhile, after getting data too, data checked is executed.
I have checked the blazor documents but struggling to find why it triggered at first.
```
<Component1>
  @if (Data != null)
      {
        @foreach (var item in Data) {
             <Component2>
        }
       }
</Componet1>

@code{
  protected override async Task OnInitializedAsync() {
       Data = //data from dataBase
  }
}
```

I need to execute data checked only after data fetch. Can anyone guide me to fix this issue?


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2019-10-14 07:03:42

------------

The data check has to happen first because something has to be rendered before the `OnInitializedAsync` method. So in case the `OnInitializedAsync` takes a long time to load the data, the user already sees something and not just a blank page.

Why do you want the data check only after the data fetch?

As a workaround you can create a local variable `bool dataIsLoaded = false;` and only after loading data in `OnInitializedAsync` you can set it to true. Then in the data check do: `@if (dataIsLoaded && Data != null)`


------------
    
    
## Answer \#1

 Vote: 7

Created at 2020-07-04 02:45:34

------------

I would suggest  to check for null Data. Instead just render the Data variable and call StateHasChanged() after Data is loaded inside the OnInitializedAsync(). The key is to let the framework know that variable Data is being used, so when StateHasChanged() is called it will render Data appropriately.
```
<h3> @Data </h3>

@code {
    private string Data = "";

    protected override async Task OnInitializedAsync()
    {
        Data = await Service.GetDataAsync();
        StateHasChanged();
    }
}
```



------------
    
    
## Answer \#2

 Vote: 5

Created at 2019-10-14 12:40:19

------------

I assume that Data is of the list or array type. What I usually do is initialize the list or array in OnInitialized and make the call in OnInitializedAsync. So no checking is necessary.

```
protected override void OnInitialized()
{
   Data = new List<Type>()
}
```



------------
    
    