# Configuration in ASP.NET Core 3.1

## Goal

Get some understanding of .NET Core configuration principles.

## Configuration

Your `program.cs` usually calls CreateDefaultBuilder.

Look at the source code of CreateDefaultBuilder: <https://source.dot.net/#Microsoft.AspNetCore/WebHost.cs,39a38c5c70f8b573> (this ist the older IWebHostBuilder of .Net core 2, but it looks similar)

Reads configuration in the following **order**:

1. Read `appsettings.json` using the JSON configuration provider.
1. Read `appsettings.<Environment>.json` using the JSON configuration provider. For example, appsettings.Production.json and appsettings.Development.json.
1. App secrets when the app runs in the Development environment.
1. Environment variables using the Environment Variables configuration provider.
1. Command-line arguments using the Command-line configuration provider.

## Configuration API

This is a deep dive into configuration: <https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.1>

Usually, you don't use configuration directly. If you have any application specific configuration or settings, you can use [Options](https://github.com/boeschenstein/aspnetcore3-configuration#options).

## Environments

<https://docs.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-3.1>

## Options

`appsettings.json` can contain application specific properties;

``` json
{
  "option1": "value1_from_json",
  "option2": -1,
}
```

You can create typed access (classes) to access your configuration settings.

### 1. Create option class

``` c#
public class MyOptions
{
    public MyOptions()
    {
        Option1 = "default_value1_from_ctor"; // Set default value.
    }
    public string Option1 { get; set; }
    public int Option2 { get; set; } = 5; // Set default value.
}
```

### 2. Configure option class

Add the configuration to the [dependency injection]((https://github.com/boeschenstein/definition#dependency-injection)) configuration in ConfigureServices of your `startup.cs`:

``` c#
// Register the Configuration instance which MyOptions binds against.
services.Configure<MyOptions>(Configuration);
```

### 3. Use option class

Have the configuration [injected](https://github.com/boeschenstein/definition#dependency-injection) configuration in your [service](https://github.com/boeschenstein/definition#what-is-a-service):

``` c#
public class MyClass
{
    private readonly MyOptions _myOption;

    public MyService(IOptions<MyOptions> options)
    {
        _myOption = options.Value;
    }
}
```

### Namespace, Category, Suboptions

You can structure your application properties in `appsettings.json` in subsections:

``` json
{
  "option1": "value1_from_json",
  "option2": -1,
  "subsection": {
    "suboption1": "subvalue1_from_json",
    "suboption2": 200
  }
}
```

Create normal classes ...

``` c#
public class MySubOptions
{
    public MySubOptions()
    {
        SubOption1 = "default_value1_from_ctor"; // Set default value.
    }
    public string SubOption1 { get; set; }
    public int SubOption2 { get; set; } = 5; // Set default value.
}
```

.. and [inject]((https://github.com/boeschenstein/definition#dependency-injection)) them like normal options.

Details: <https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1#suboptions-configuration>

### Untyped options

Sometimes you cannot inject a typed configuration (for example if you use a generic, multipurpose factory). Use `Bind()` in this case:

``` c#
public class MyTesterClass
    {
        private readonly IConfiguration _configuration;

        public MyTesterClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }

        public void Test()
        {
            var options = new MyOptions();
            _configuration.Bind("<OptionNamespace>", options);
            var options = new MyOptions();
            _configuration.Bind("<OptionNamespace>:<OptionSubNamespace>", options); // suboptions
        }
    }
}
```

## Secrets

### Store Secrets

You do not want to store any confidential information (like passwords) in your source code. With secrets, you can store the passwords and other information you want to keep outside of your source code.

To store credentials on your computer, open cmd in the folder of `<your-project>.csproj` and run this:

``` dos
dotnet user-secrets init
dotnet user-secrets set "<myPurpose:mySecretItem>" "<mySecret>"
```

### Use secrets - by configuration

[Inject](https://github.com/boeschenstein/definition#dependency-injection) the configuration in your [service](https://github.com/boeschenstein/definition#what-is-a-service):

``` C#
public class MyClass
{
    private readonly IConfiguration _configuration;

    public MyService(IConfiguration configuration)
    {
        _configuration = configuration;
    }

    public void MyFunction(){
        _mySecret = _configuration["<myPurpose:mySecretItem>"];
    }
}
```

### Use secrets - by option

[Inject](https://github.com/boeschenstein/definition#dependency-injection) the typed option in your [service](https://github.com/boeschenstein/definition#what-is-a-service):

``` C#
public class MyClass
{
    private readonly MyOptions _myOptions;

    public MyService(IOptions<MyOptions> options)
    {
        _myOptions = options.Value;
    }

    public void MyFunction(){
        _mySecret = _myOptions.Option1;
    }
}
```

> Tip: You can check and manage user secrets by right-click on your main project: "Manage User Secrets".

## What's next

- Add Third party logger (Serilog) to your backend: <https://github.com/boeschenstein/angular9-dotnetcore3-logging>
- Swagger/OpenApi are tools which can create your Angular code to access the backend: check this <https://github.com/boeschenstein/angular9-dotnetcore-openapi-swagger>

## Additional Information

### Links

- .NET Core Configuration: <https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.1>
- Secrets: <https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows>
- Options: <https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1>
- Configuration: <https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.1>
- ASP.NET WebApi: <https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio>
- About me: <https://github.com/boeschenstein>

### Current Versions

- Visual Studio 2019 16.5.4
- .NET core 3.1
- npm 6.14.4
- node 12.16.1
- Angular CLI 9.1
