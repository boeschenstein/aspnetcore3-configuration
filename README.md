# Configuration in ASP.NET Core 3

## Goal

Get some understanding of .NET Core configuration principles.

## Configuration

Your `program.cs` usually calls CreateDefaultBuilder.

Look at the source code of CreateDefaultBuilder: <https://source.dot.net/#Microsoft.AspNetCore/WebHost.cs,39a38c5c70f8b573> (this ist the older IWebHostBuilder of .Net core 2, but it looks similar)

Reads configuration in the following order:

1. Read `appsettings.json` using the JSON configuration provider.
1. Read `appsettings.<Environment>.json` using the JSON configuration provider. For example, appsettings.Production.json and appsettings.Development.json.
1. App secrets when the app runs in the Development environment.
1. Environment variables using the Environment Variables configuration provider.
1. Command-line arguments using the Command-line configuration provider.

## Custom Configuration Provider

<https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.1#custom-configuration-provider>

>---- wip ----

>---- wip ----

## Environments

<https://docs.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-3.1>

## Options

### 1. Create option class

``` c#
public class MyOptions
{
    public MyOptions()
    {
        Option1 = "value1_from_ctor"; // Set default value.
    }
    public string Option1 { get; set; }
    public int Option2 { get; set; } = 5; // Set default value.
}
```

### 2. Active option class

``` c#
// Register the Configuration instance which MyOptions binds against.
services.Configure<MyOptions>(Configuration);
```

### 3. Use option class

``` c#
```

## Secrets

### Store Secrets

To store credentials on your computer, open cmd in the folder of `<your-project>.csproj` and run this:

``` dos
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

## What's next

- Add Third party logger (Serilog) to your backend: <>
- Swagger/OpenApi are tools which can create your Angular code to access the backend: check this <https://github.com/boeschenstein/angular9-dotnetcore-openapi-swagger>

## Additional Information

### Links

- .NET Core Configuration: <https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.1>
- Secrets: <https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows>
- Options: <https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1>
- ASP.NET WebApi: <https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio>
- About me: <https://github.com/boeschenstein>

### Current Versions

- Visual Studio 2019 16.5.4
- .NET core 3.1
- npm 6.14.4
- node 12.16.1
- Angular CLI 9.1
