
# Post \#56143613 [Link](https://stackoverflow.com/questions/56143613/)

## Inject generic interface in .NET Core

**Vote**: 28 (207/702) **Views**: 46265 (176/702) 

**Internal ID** \#2-6-394

Created at 2019-05-15 07:17:55

Tags: `c#` `asp.net-core` `dependency-injection` `.net-core` `asp.net-core-mvc`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I want to inject this interface to my controllers:
```
public interface IDatabaseService<T>
{
    IEnumerable<T> GetList();
    ...
}
```

I want to use generic, because in my `WebApi` project i have controllers like `ProjectController`, `TaskController` etc and i want to use generic interface to each of type (for example, `IDatabaseService<Project>`, `IdatabaseService<Task>` etc).
Class, that will be injected to controller will look like this:
```
public class ProjectService : IDatabaseService<Project>
{
    private readonly DbContext context;

    public ProjectService(DbContext context)
    {
        this.context = context;
    }

    public IEnumerable<Project> GetList() { }
    ...
}
```

But when I try to ineject in my `Startup.cs`:
```
services.AddScoped<IDatabaseService<T>>();
```

I need to pass `T` type.
My question is, how to make injection generic and how inject it properly in controller? For example:
```
public class ProjectController : ControllerBase
{
    private readonly ProjectService projectService;

    public ProjectController (IDatabaseService<Project> projectService)
    {
        this.projectService = projectService;
    }
}
```

If it will work? And is it good practice to make generic interface to inject into controllers? If no, how to do it better?


----------
        
## Answer \#0

**Accepted** Vote: 27

Created at 2019-05-15 08:11:54

------------

1.) if you want to write hard code

```
services.AddScoped<IDatabaseService<Project>, ProjectService>();
```


2.) if you want to register dynamically that all types of implemented `IDatabaseService<>`

```
System.Reflection.Assembly.GetExecutingAssembly()
            .GetTypes()
            .Where(item => item.GetInterfaces()
            .Where(i => i.IsGenericType).Any(i => i.GetGenericTypeDefinition() == typeof(IDatabaseService<>)) && !item.IsAbstract && !item.IsInterface)
            .ToList()
            .ForEach(assignedTypes =>
            {
                var serviceType = assignedTypes.GetInterfaces().First(i => i.GetGenericTypeDefinition() == typeof(IDatabaseService<>));
                services.AddScoped(serviceType, assignedTypes);
            });
```



------------
    
    
## Answer \#1

 Vote: 71

Created at 2019-05-15 07:27:48

------------

You can do this by adding the below line in `Startup.cs`

```
// best practice  
services.AddTransient(typeof(IDatabaseService<>),typeof(DatabaseService<>));
```


Visit [Here](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-2.2) to know more about 


------------
    
    
## Answer \#2

 Vote: 21

Created at 2019-05-15 09:08:44

------------

You can use services.AddScoped to use only 1 instance in the scope request. So in general improvement compare to AddTransient

```
services.AddScoped(typeof(IGenericRepository<>), typeof(GenericRepository<>));
```


So my interface and class will look like this

```
public interface IGenericRepository<T> where T : class
```


```
public class GenericRepository<T> : IGenericRepository<T> where T : class
```



------------
    
    
## Answer \#3

 Vote: 3

Created at 2020-05-20 19:39:21

------------

Feel free to use helpers:



```
services.AddAllGenericTypes(typeof(IDatabaseService<>), new[] {typeof(ProjectService).GetTypeInfo().Assembly});
```


With extensions from:
[https://gist.github.com/GetoXs/5caf0d8cfe6faa8a855c3ccef7c5a541](https://gist.github.com/GetoXs/5caf0d8cfe6faa8a855c3ccef7c5a541)


------------
    
    