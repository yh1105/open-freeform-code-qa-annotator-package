
# Post \#71954271 [Link](https://stackoverflow.com/questions/71954271/)

## How can I read the appsettings.json in a .Net 6 console application?

**Vote**: 41 (153/702) **Views**: 44054 (181/702) 

**Internal ID** \#2-6-406

Created at 2022-04-21 12:11:47

Tags: `c#` `json` `console-application` `.net-6.0`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#` `json`)

----------

**Notepad**


----------

I try to create a .Net 6 Console Application but having troubles reading my appsettings.json file. In a web application I could use this...
```
var builder = WebApplication.CreateBuilder(args);
```

But what would I use in a console application? I get this error when trying to add it to program.cs. `"The name 'WebApplication' does not exist in the current context"`


----------
        
## Answer \#0

**Accepted** Vote: 15

Created at 2022-08-10 02:59:53

------------

Using .NET 6 Console app, try:
```
using IHost host = Host.CreateDefaultBuilder(args).Build();

IConfiguration config = host.Services.GetRequiredService<IConfiguration>();

string con= config.GetValue<string>("ConnectionStrings:conn1");
//OR
string connectionString = config["ConnectionStrings:conn1"];

Console.WriteLine($"Hello, World! {connectionString}");
```

appsettings.json (Properties): (CopyToOutputDirectory = Always):
```
"ConnectionStrings": {
    "conn1": "Server=localhost;Database=MyDatabase;Trusted_Connection=True", 
  }
```



------------
    
    
## Answer \#1

 Vote: 54

Created at 2022-04-21 12:24:31

------------

Add these two nuget packages in your application
```
Microsoft.Extensions.Configuration

Microsoft.Extensions.Configuration.Json
```

Then you can use `ConfigurationBuilder` to use `appsettings.json` file
```
var configuration =  new ConfigurationBuilder()
     .AddJsonFile($"appsettings.json");
            
var config = configuration.Build();
var connectionString = config.GetConnectionString("ConnectionString");
```

[Getting Values from AppSettings in Console Application](https://stackoverflow.com/questions/65110479/how-to-get-values-from-appsettings-json-in-a-console-application-using-net-core)
[Adding AppSettings in .net core app](https://blog.bitscry.com/2017/05/30/appsettings-json-in-net-core-console-app/)


------------
    
    
## Answer \#2

 Vote: 20

Created at 2022-04-21 12:38:06

------------

Link to docs: [https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration](https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration)
Nuget packages are needed to do it:
```
<PackageReference Include="Microsoft.Extensions.Configuration.Binder" Version="6.0.0" />
    <PackageReference Include="Microsoft.Extensions.Configuration.Json" Version="6.0.0" />
    <PackageReference Include="Microsoft.Extensions.Configuration.EnvironmentVariables" Version="6.0.0" />
```

using Microsoft.Extensions.Configuration;
```
// Build a config object, using env vars and JSON providers.
    IConfiguration config = new ConfigurationBuilder()
        .AddJsonFile("appsettings.json")
        .AddEnvironmentVariables()
        .Build();
    
    // Get values from the config, given their key and their target type.
    Settings settings = config.GetRequiredSection("Settings").Get<Settings>();
    
    // Write the values to the console.
    Console.WriteLine($"KeyOne = {settings.KeyOne}");
    Console.WriteLine($"KeyTwo = {settings.KeyTwo}");
    Console.WriteLine($"KeyThree:Message = {settings.KeyThree.Message}");

// Application code which might rely on the config could start here.
// This will output the following:
//   KeyOne = 1
//   KeyTwo = True
//   KeyThree:Message = Oh, that's nice...
```

JSON File (appsettings.json)
```
{
    "Settings": {
        "KeyOne": 1,
        "KeyTwo": true,
        "KeyThree": {
            "Message": "Oh, that's nice..."
        }
    }
}
```

UPD: I checked the approach, and it works;
My code:
```
// See https://aka.ms/new-console-template for more information

using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.Json;

Console.WriteLine("Hello, World!");
ConfigurationBuilder configurationBuilder = new ConfigurationBuilder();
IConfiguration c = configurationBuilder.AddJsonFile("appsettings.json").AddEnvironmentVariables().Build();
var k = c.GetRequiredSection("Settings").Get<Settings>().KeyOne;
var n = 1;

public class NestedSettings
{
    public string Message { get; set; } = null!;
}
public class Settings
{
    public int KeyOne { get; set; }
    public bool KeyTwo { get; set; }
    public NestedSettings KeyThree { get; set; } = null!;
}
```



------------
    
    
## Answer \#3

 Vote: 5

Created at 2022-04-21 12:19:26

------------

In a console app I add the `appsettings.json` and `user secrets` like this, you may also have a development json file as well.
```
internal class Program
{
    internal static IConfigurationRoot Configuration;

    public static void Main()
        => new Program().MainAsync().GetAwaiter().GetResult();

    private async Task MainAsync()
    {
        var configurationBuilder = new ConfigurationBuilder();
        configurationBuilder.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true);
        configurationBuilder.AddUserSecrets(typeof(Program).GetTypeInfo().Assembly, optional: false);
        Configuration = configurationBuilder.Build();

        ...
    }
}
```

Then elsewhere in the console app in any class you can simply call it like this
```
var someSetting = Program.Configuration["SomeSetting"];
```

If you want a strongly typed class then see this answer [.net core Console application strongly typed Configuration](https://stackoverflow.com/questions/46014871/net-core-console-application-strongly-typed-configuration) on SO


------------
    
    
## Answer \#4

 Vote: 3

Created at 2022-10-09 19:26:53

------------

I would prefer the following code because it will automatically read the appsettings.json file from the project directory. Pay attention to `_.Configuration` I used during configuring DbContext to read the connection string.
```
var builder = Host.CreateDefaultBuilder(args)
        .ConfigureServices(
                (_, services) => services
                    .AddTransient<IService, Service>()
                    .AddDbContext<ApplicationDbContext>(options =>
                        options.UseNpgsql(
                      _.Configuration.GetConnectionString("DefaultConnection"),
                            b => b.MigrationsAssembly(typeof(ApplicationDbContext).Assembly.FullName)))
                    );
var host = builder.Build();


var dbContext = host.Services.GetRequiredService<ApplicationDbContext>();
```

I put this together from this link: [https://bjdejongblog.nl/net-core-6-dependency-injection-console-program/](https://bjdejongblog.nl/net-core-6-dependency-injection-console-program/)


------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-10-03 10:06:30

------------

Install the following packages
```
Install-Package Microsoft.Extensions.Configuration.Json
Install-Package Microsoft.Extensions.Hosting
```

create a file call `appsettings.json`
```
{
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "defaultConnection": "data source=.;initial catalog=ExchangeGatewayDb;integrated security=true;MultipleActiveResultSets=True;"
  }
}
```

add this code to `Programe.cs`
```
var hostBuilder = new HostBuilder().ConfigureHostConfiguration(config =>
        {
            config.AddEnvironmentVariables();

            if (args != null)
            {
                config.AddCommandLine(args);
            }
        }).ConfigureAppConfiguration((context, builder) =>
        {
            var env = context.HostingEnvironment;
            builder.SetBasePath(AppContext.BaseDirectory)
            .AddJsonFile("appsettings.json", optional: false)
            .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true).AddEnvironmentVariables();

            var configuration = builder.Build();
            var connectionString = configuration["ConnectionStrings:defaultConnection"];
        }).ConfigureServices(services =>
        {
            services.Configure<ConsoleLifetimeOptions>(opts => opts.SuppressStatusMessages = true);
            //your code
        });

var buildHost = hostBuilder.Build();
buildHost.Run();
```



------------
    
    