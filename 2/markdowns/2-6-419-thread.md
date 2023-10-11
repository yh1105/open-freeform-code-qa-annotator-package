
# Post \#61989581 [Link](https://stackoverflow.com/questions/61989581/)

## Blazor - passing arguments to parent

**Vote**: 7 (449/702) **Views**: 11048 (405/702) 

**Internal ID** \#2-6-419

Created at 2020-05-24 17:16:19

Tags: `c#` `asp.net` `razor` `blazor`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

How to pass multiple arguments from child to parent upon onClick event in child component

Child Razor

```
@foreach( var item in items)
{
  @foreach( var score in item.scores)
  {
    <span @onclick="ItemClicked(item.name, score.id)"> 
      @item.name 
    </span>
  }
}
```


Parent Razor

```
<Child ItemClicked = "@ItemClicked"/>

@code{
 private void ItemClicked(string name, int id)
    {
        Console.WriteLine(name);
        Console.WriteLine(id);
    }
}
```



----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2020-05-24 18:42:59

------------

In Child

```
<span @onclick = "() => ItemClicked(item.name, score.id)"> @item.name</span>

@code 
{
   [Parameter] public Action<string,int> ItemClicked { get; set; }
}
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2020-05-26 17:55:59

------------

What you can do is create a new class for the arguments you're trying to pass back up to the parent. In this example, I'm using a generic checkbox component that I pass an Id and a Checked property because I need to know what item I'm selecting in the parent component as well as if it's checked or not. So the event needs to know the Id and whether it's checked. The child component contains it's own logic and then passes up a new object with the assigned values which the parent can now use.

Parent

```
<Checkbox Id="@Id" Checked="@Item.Checked" CheckChanged="OnCheck" />
```


```
public class OnCheckEventArgs
{
    public bool IsChecked {get; set;}
    public long ItemId {get; set;}
}

public class ItemsOverview : ComponentBase
{
    public void OnCheck(OnCheckEventArgs args)
    {
        // do stuff
    }
}
```


Child

```
<input type="checkbox" Id="@Id" checked="@Checked" @onchange="CheckboxChanged" />
```


```
public partial class Checkbox : ComponentBase
{
    [Parameter]
    public long Id {get; set;}

    [Parameter]
    public bool Checked {get; set;}

    [Parameter]
    public EventCallback<OnCheckEventArgs> CheckChanged {get; set;}

    protected async Task OnCheckChange(bool checked)
    {
        // do stuff
        await CheckChanged.InvokeAsync(new OnCheckEventArgs { IsChecked = checked, ItemId = @Id });
    }
}
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-05-24 18:09:12

------------

You Need to assign it to a eventcallback wich only receive one parameter, so you can create a dictionary or make a class and pass it in the object.

You pass the whole item to the Parent component, then you can get the id from here.

```
@foreach (var item in items)
{
    @foreach (var score in item.scores)
    {
        <span @onclick="OnItemClick.InvokeAsync(item)">
            @item.name
        </span>
    }
}

@code {
    [Parameter] public EventCallback<_TYPE_OF_YOUR_ITEM> OnItemClick { get; set; }
}
```


```
<Child OnItemClick="HandleItemClick">

@code {
   private void HandleItemClick(_TYPE_OF_YOUR_ITEM) 
   {
      // Handle your event here
   }
}
```


With a dictionary

```
@foreach (var item in items)
{
    @foreach (var score in item.scores)
    {
        <span @onclick="HandleClick(item, score.id)">
            @item.name
        </span>
    }
}

@code {
    [Parameter] public EventCallback<Dictionary<string, dynamic>> OnItemClick { get; set; }

    private void HandleClick(dynamic item, int score)
    {
        var parameter = new Dictionary<string, dynamic>();
        parameter.Add("Item", item);
        parameter.Add("ScoreId", score);

        OnItemClick.InvokeAsync(parameter);
}
}
```


```
private void HandleItemClick(dictionary<string, dynamic> parameters) 
   {
      // extract your parameters and handle
      // parameters["ScoreId"], parameters["item"]

   }
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-05-24 18:02:08

------------

I am assuming that "ItemClicked" is EventCallback parameter.
If so, you can create an object like 

```
Class Student
{
   public int Id { get; set; }
   public string Name { get; set; }
}
```


And then you can use like 



```
private void ItemClicked(Student student)
    {
        Console.WriteLine(student.Name);
        Console.WriteLine(student.Id);
    }
```




```
<span @onclick = "ItemClicked(student)"> @item.name </span>
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2020-05-24 18:15:25

------------

According to Microsoft documentation, maybe you want to do binding instead?
Here's the link:
[https://learn.microsoft.com/en-us/aspnet/core/blazor/data-binding?view=aspnetcore-3.1](https://learn.microsoft.com/en-us/aspnet/core/blazor/data-binding?view=aspnetcore-3.1)

search for "Child-to-parent binding with chained bind"

```
<h1>Child Component</h1>
<input @oninput="OnPasswordChanged" 
   required 
   type="@(showPassword ? "text" : "password")" 
   value="@Password" />
@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }


}
```


parent:

```
<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```



------------
    
    
## Answer \#5

 Vote: 1

Created at 2020-05-24 18:34:22

------------

Another answer: since you changed your ask

maybe event handling: [https://learn.microsoft.com/en-us/aspnet/core/blazor/event-handling?view=aspnetcore-3.1](https://learn.microsoft.com/en-us/aspnet/core/blazor/event-handling?view=aspnetcore-3.1) 

Read this section: EventCallback and think about EventCallback 

child:
    
        Trigger a Parent component method
    

```
[Parameter]
public EventCallback<MouseEventArgs> OnClickCallback { get; set; }
```


parent:

```
<ChildComponent Title="Panel Title from Parent"
            OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

@code{

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }

}
```


you might need @on{EVENT}:preventDefault 


------------
    
    