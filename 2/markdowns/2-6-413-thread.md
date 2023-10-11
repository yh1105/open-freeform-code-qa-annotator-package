
# Post \#59118282 [Link](https://stackoverflow.com/questions/59118282/)

## Nullable reference type in C#8 when using DTO classes with an ORM

**Vote**: 34 (179/702) **Views**: 21050 (294/702) 

**Internal ID** \#2-6-413

Created at 2019-11-30 16:52:51

Tags: `c#` `orm` `dto` `c#-8.0` `nullable-reference-types`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I activated this feature in a project having data transfer object (DTO) classes, as given below:

```
public class Connection
    {
        public string ServiceUrl { get; set; }
        public string? UserName { get; set; }
        public string? Password { get; set; }
        //... others 
    }
```


But I get the error:

> `CS8618`: Non-nullable property 'ServiceUrl' is uninitialized. Consider declaring the property as nullable.

This is a DTO class, so I'm not initializing the properties. This will be the responsibility of the code initializing the class to ensure that the properties are non-null. 

For example, the caller can do:

```
var connection = new Connection
{
  ServiceUrl=some_value,
  //...
}
```


My question: How to handle such errors in DTO classes when C#8's nullability context is enabled?


----------
        
## Answer \#0

**Accepted** Vote: 38

Created at 2019-11-30 17:05:52

------------

You can do either of the following:

1. EF Core suggests initializing to null! with null-forgiving operator public string ServiceUrl { get; set; } = null! ;
//or
public string ServiceUrl { get; set; } = default! ;
2. Using backing field: private string _ServiceUrl;
public string ServiceUrl
{
    set => _ServiceUrl = value;
    get => _ServiceUrl
           ?? throw new InvalidOperationException("Uninitialized property: " + nameof(ServiceUrl));
}




------------
    
    
## Answer \#1

 Vote: 10

Created at 2019-11-30 17:02:42

------------

If it's non nullable, then what can the compiler do when the object is initialized?

The default value of the string is null, so you will 


1. either need to assign a string default value in the declaration public string ServiceUrl { get; set; } = String.Empty;
2. Or initialize the value in the default constructor so that you will get rid of the warning
3. Use the ! operator (that you can't use)
4. Make it nullable as robbpriestley mentioned.




------------
    
    
## Answer \#2

 Vote: 9

Created at 2020-11-01 11:26:14

------------

I've been playing with the new `Nullable Reference Types` () feature for a while and I must admit the biggest complain I've is that the compiler is giving you those warnings in the classes' declarations.
At my job I built a micro-service trying to solve all those warnings leading to quite a complicated code especially when dealing with EF Core, AutoMapper and DTOs that are shared as a Nuget package for .NET Core consumers.
This very simple micro-service quickly became a real mess just because of that NRT feature leading me to crazy non-popular coding styles.
Then I discovered the awesome [SmartAnalyzers.CSharpExtensions.Annotations](https://www.nuget.org/packages/SmartAnalyzers.CSharpExtensions.Annotations) Nuget package after reading Cezary Piątek's article [Improving non-nullable reference types handling](https://cezarypiatek.github.io/post/better-non-nullable-handling).
This Nuget package is shifting the non-nullbable responsibility to the caller code where your objects are instantiated rather than the class declaration one.
In his article he is saying we can activate this feature in the whole assembly by writing the following line in one of your `.cs` files
```
[assembly: InitRequiredForNotNull]
```

You could put it in your `Program.cs` file for example but I personally prefer to activate this in my `.csproj` directly
```
<ItemGroup>
    <AssemblyAttribute Include="SmartAnalyzers.CSharpExtensions.Annotations.InitRequiredForNotNullAttribute" />
</ItemGroup>
```

Also I changed the default `CSE001 Missing initialization for properties` errors to warnings by setting this in my `.editorconfig` file
```
[*.cs]
dotnet_diagnostic.CSE001.severity = warning
```

You can now use your `Connection` class as you'd normally do without having any error
```
var connection = new Connection()
{
    ServiceUrl = "ServiceUrl"
};
```

Just one thing to be aware of. Let's consider your class like this
```
public class Connection
{
    public string ServiceUrl { get; }
    public string? UserName { get; }
    public string? Password { get; }

    public Connection(string serviceUrl, string? userName = null, string? password = null)
    {
        if (string.IsNullOrEmpty(serviceUrl))
            throw new ArgumentNullException(nameof(serviceUrl));

        ServiceUrl = serviceUrl;
        UserName = userName;
        Password = password;
    }
}
```

In that case when you instantiate your object like
```
var connection = new Connection("serviceUrl");
```

The `SmartAnalyzers.CSharpExtensions.Annotations` Nuget package isn't analyzing your constructor to check if you're really initializing all non-nullable reference types. It's simply trusting it and trusting that you did things correctly in the constructor. Therefore it's not raising any error even if you forgot a non-nullable member like this
```
public Connection(string serviceUrl, string? userName = null, string? password = null)
{
    if (string.IsNullOrEmpty(serviceUrl))
        throw new ArgumentNullException(serviceUrl);

    UserName = userName;
    Password = password;
}
```

I hope you will like the idea behind this Nuget package, it had become the default package I install in all my new .NET Core projects.


------------
    
    
## Answer \#3

 Vote: 6

Created at 2020-10-19 09:41:31

------------

Usually DTO classes are stored in separate folders, so I simply disable this diagnostic in .editorconfig file based on path pattern:
```
[{**/Responses/*.cs,**/Requests/*.cs}]
# CS8618: Non-nullable field is uninitialized. Consider declaring as nullable.
dotnet_diagnostic.CS8618.severity = none
```



------------
    
    
## Answer \#4

 Vote: 3

Created at 2020-04-13 10:37:04

------------

Another thing that might come handy in some scenarios:

```
[SuppressMessage("Compiler", "CS8618")]
```


Can be used on top of member or whole type.


---



Yet another thing to consider is adding `#nullable disable` on top of file to disable nullable reference for the whole file.


------------
    
    
## Answer \#5

 Vote: 1

Created at 2020-06-17 14:06:34

------------

The benefit of Nullable reference types is to manage null reference exceptions effectively. So it is better to use the directive #nullable enable. It helps developers to avoid null reference exceptions at run time.


The below 2 things compiler will make sure during static flow analysis.


1. The variable has been definitely assigned to a non-null value.
2. The variable or expression has been checked against null before de-referencing it.



If does not satisfy the above conditions, compiler will throw warnings.
Note : All these activities emitted during compile time.



After C# 8.0, reference types are considered as non nullable. So that compiler throw timely warnings if the reference type variables are not handle the nulls properly.




1. Appended '?' before the variable (Example) string? Name
2. Using "null forgiving operator" (Example) string Name {get;set;} = null!; or string Name {get;set;} = default!;
3. Using "backing fields" (Example): private string _name; public string Name {get { return _name; } set {_name = value;} }





If it is non nullable initialize the property using constructors. 




1. Better handling of null reference exceptions
2. With the help of compile time warnings, developers can correct their code timely.
3. Developers will convey the intent to compiler while design the class. Thereafter compiler will enforce the intent through out the code.




------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-11-22 11:30:24

------------

I created a helper `struct` that defines whether a value was set or not (independent of whether it is `null` or not), so your DTO would look like this:
```
public class Connection
{
    public Settable<string> ServiceUrl { get; set; }
    public Settable<string?> UserName { get; set; }
    public Settable<string?> Password { get; set; }
    //... others 
}
```

As a `struct` is always initialized, no nullable reference type warnings appear, and if you attempt to access an uninitialized property, an `InvalidOperationException` is thrown.
Details see NuGet package `Hafner.Tools.Settable` ([https://www.nuget.org/packages?q=hafner.tools.settable](https://www.nuget.org/packages?q=hafner.tools.settable)) and Git repo [https://github.com/HugoRoss/Hafner.Tools.Settable](https://github.com/HugoRoss/Hafner.Tools.Settable).


------------
    
    
## Answer \#7

 Vote: -1

Created at 2021-07-07 05:35:13

------------

To get rid of the warnings on DTOs, at the start of the DTO's cs file, specify:
#pragma warning disable CS8618
And at the end:
#pragma warning restore CS8618


------------
    
    