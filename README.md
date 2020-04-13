# Syncfusion Blazor Localization

Syncfusion Blazor components will translate the locale content to desired language using the own ASP.Net core approach. In this page, we will show you how to use this localization feature in your application.

## Server-side application

For server-side application, we have given the support using the Resource (resx) file. In that file, it will hold the key value pair of locale content in the following format.

```csharp
<Component_Name>_<Feature_Name>_<Locale_Key>
```

By default, en-US culture locale text will be loaded from Syncfusion Blazor assembly itself. Suppose, if you want to load the different locale to Syncfusion Blazor component. Follow these steps.o set a different culture, other than `English`, follow the below steps:

### Step 1: Enable the localization in application

To enable the different culture in your application, we suggest you to go through the following resources, which will help you to learn the framework wise localization support and their architecture. 

* [Blazor Localization](https://docs.microsoft.com/en-us/aspnet/core/blazor/globalization-localization?view=aspnetcore-3.1)

* [ASP.Net Core Localization](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization?view=aspnetcore-3.0)

The culture can be set using one of the following approaches:

* [Cookies](https://docs.microsoft.com/en-us/aspnet/core/blazor/globalization-localization?view=aspnetcore-3.1#cookies)

* [Provide UI to choose the culture](https://docs.microsoft.com/en-us/aspnet/core/blazor/globalization-localization?view=aspnetcore-3.1#provide-ui-to-choose-the-culture)

### Step 2: Load the Resx file in application

We have placed all locale files for different cultures in this [github](./src/) location. You can get any Resource files from there and utilize it in your application.

For e.g. We can get locale files from this location and place in “Application directory/Resources/” as like in the following screenshot.

![Resource](https://blazor.syncfusion.com/documentation/common/images/resource.png) 

### Step 3:  Implementing the ISyncfusionStringLocalizer

We want to implement the ISyncfusionStringLocalizer, which act as a middleware to connect the Blazor components and Resource files. 

> **Note:** We should map the SfResources Resource manager to this interface manager. 

```csharp
public class SampleLocalizer: ISyncfusionStringLocalizer
    {
        // To get the locale key from mapped resources file
        public string Get(string key)
        {
            return this.Manager.GetString(key);
        }

        // To access the resource file and get the exact value for locale key

        public System.Resources.ResourceManager Manager { 
            get
            {
                return ApplicationNamespace.Resources.SfResources.ResourceManager;
            }
        }
    }
```

### Step 4: Configure the locale service

We should add the Syncfusion locale service in `Startup.cs` page, which is after the Syncfusion default service. Therefore, application level Syncfusion locale service injection will override the default Syncfusion locale service.

> **Note:** We should map the folder path to ResourcesPath in default Localization service. 


```csharp

public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();

            #region Localization
            // Set the Resx file folder path to access
            services.AddLocalization(options => options.ResourcesPath = "Resources");
            services.AddSyncfusionBlazor();
            // register a Syncfusion locale service to customize the Syncfusion Blazor component locale culture
            services.AddSingleton(typeof(ISyncfusionStringLocalizer), typeof(SampleLocalizer));
            services.Configure<RequestLocalizationOptions>(options =>
            {
                // Define the list of cultures your app will support
                var supportedCultures = new List<CultureInfo>()
            {
                new CultureInfo("en-US"),
                new CultureInfo("de")
            };

                // Set the default culture
                options.DefaultRequestCulture = new RequestCulture("en-US");

                options.SupportedCultures = supportedCultures;
                options.SupportedUICultures = supportedCultures;
            });
            #endregion

            services.AddRazorPages();
            services.AddServerSideBlazor();

        }

```