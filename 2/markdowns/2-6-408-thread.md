
# Post \#58828878 [Link](https://stackoverflow.com/questions/58828878/)

## Blazor Component Reference Null on First Render

**Vote**: 19 (269/702) **Views**: 22451 (278/702) 

**Internal ID** \#2-6-408

Created at 2019-11-13 01:02:39

Tags: `c#` `asp.net-core` `blazor` `blazor-client-side`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I have a custom  component with an event Action called TabChanged. In my Razor page I set the reference to it up like so:

```
<TabSet @ref="tabSet">
 ...
</TabSet>

@code {
    private TabSet tabSet;   
    ...
}
```


In the  method I assign a handler to the event:

```
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if(firstRender)
    {
        tabSet.TabChanged += TabChanged;
    }       
}
```


The first time the page renders I get a  error.

If I switch to use subsequent renders it works fine:

```
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if(!firstRender)
    {
        tabSet.TabChanged += TabChanged;
    }       
}
```


But of course this is sloppy and I will be firing multiple event handlers as they stack up during renders.

How can I assign the reference one time and on first render? I am following the docs as outlined [here](https://learn.microsoft.com/en-us/aspnet/core/blazor/components?view=aspnetcore-3.0#capture-references-to-components)



Here is the TabSet.razor file:

```
@using Components.Tabs

<!-- Display the tab headers -->
<CascadingValue Value="this">
    <ul class="nav nav-tabs">
        @ChildContent
    </ul>
</CascadingValue>

<!-- Display body for only the active tab -->
<div class="nav-tabs-body" style="padding:15px; padding-top:30px;">
    @ActiveTab?.ChildContent
</div>

@code {

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public ITab ActiveTab { get; private set; }

    public event Action TabChanged;


    public void AddTab(ITab tab)
    {
        if (ActiveTab == null)
        {
            SetActiveTab(tab);
        }
    }

    public void RemoveTab(ITab tab)
    {
        if (ActiveTab == tab)
        {
            SetActiveTab(null);
        }
    }

    public void SetActiveTab(ITab tab)
    {
        if (ActiveTab != tab)
        {
            ActiveTab = tab;
            NotifyStateChanged();
            StateHasChanged();
        }
    }

    private void NotifyStateChanged() => TabChanged?.Invoke();

}
```


TabSet also uses Tab.razor:

```
@using Components.Tabs
@implements ITab

<li>
    <a @onclick="Activate" class="nav-link @TitleCssClass" role="button">
        @Title
    </a>
</li>

@code {
    [CascadingParameter]
    public TabSet ContainerTabSet { get; set; }

    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }


    private string TitleCssClass => ContainerTabSet.ActiveTab == this ? "active" : null;

    protected override void OnInitialized()
    {
        ContainerTabSet.AddTab(this);
    }

    private void Activate()
    {
        ContainerTabSet.SetActiveTab(this);
    }
}
```


And ITab.cs Interface

```
using Microsoft.AspNetCore.Components;

namespace PlatformAdmin.Components.Tabs
{
    public interface ITab
    {
        RenderFragment ChildContent { get;  }

        public string Title { get; }
    }
}
```


It's taken from a Steve Sanderson example found [here](https://gist.github.com/SteveSandersonMS/f10a552e1761ff759b1631d81a4428c3)



Here is the debugger showing tabSet is null on first render:

[](https://i.stack.imgur.com/ONxFB.jpg)

And not null on additional renders:

[](https://i.stack.imgur.com/WhN1m.jpg)


----------
        
## Answer \#0

**Accepted** Vote: 22

Created at 2019-11-14 15:17:49

------------

As [Dani Herrera](https://stackoverflow.com/users/842935/dani-herrera) pointed out in the comments this may be due to the component being withing an if/else statement and indeed it was. Previously I had the component hidden if an object was null:
```
@if(Account != null)
{
    <TabSet @ref="tabSet">
     ...
    </TabSet>
}
```

I left this out for brevity and made the incorrect assumption that the issue was not the conditional. I was very wrong as on first render the object is null and therefore the component does not exist! So be careful out there. I resolved it by moving my conditionals to the  sections within the component:
```
<TabSet @ref="tabSet">
    @if(Account != null)
    {
        <Tab>
         ...
        </Tab>
        <Tab>
         ...
        </Tab>
    }
</TabSet>
```



------------
    
    
## Answer \#1

 Vote: 4

Created at 2019-11-13 16:48:50

------------

In first render, component is rendered. After first render, referent object is refer to component. So, at first time, ref of component is null(not ref).
For more detail, document of blazor is detail issue in [here](https://learn.microsoft.com/vi-vn/aspnet/core/blazor/components?view=aspnetcore-3.0)

The tabSet variable is only populated after the component is rendered and its output includes the TabSet element. Until that point, there's nothing to reference. To manipulate components references after the component has finished rendering, use the OnAfterRenderAsync or OnAfterRender methods.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-05-05 13:03:07

------------

in my case it's solved by initialize the object
EX: private TabSet tabSet = new TabSet();


------------
    
    