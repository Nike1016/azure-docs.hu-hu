---
title: A .NET nyomkövetési naplók megismerése az Azure Application Insights és a ILogger használatával
description: Minták az Azure Application Insights ILogger Provider ASP.NET Core és konzolos alkalmazásokkal való használatáról.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 925264bb69093ab70465665e1d2da615a7a3e53d
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261762"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider a .NET Core ILogger-naplókhoz

A ASP.NET Core olyan naplózási API-t támogat, amely különböző típusú beépített és külső naplózási szolgáltatók esetén működik. A naplózást a **log ()** vagy annak egy változata hívja meg a *ILogger* -példányokon. Ez a cikk bemutatja, hogyan rögzíthet ILogger-naplókat a konzolon és ASP.NET Core alkalmazásokban a *ApplicationInsightsLoggerProvider* használatával. A cikk azt is leírja, hogyan integrálható a ApplicationInsightsLoggerProvider más Application Insights telemetria.
További információ: [bejelentkezés ASP.net Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Alkalmazások ASP.NET Core

A ApplicationInsightsLoggerProvider alapértelmezés szerint engedélyezve van a [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) -verzióban, a 2.7.0-beta3 (és újabb verziók), amikor a normál módszerek valamelyikével bekapcsolja a rendszeres Application Insights figyelést:
- A **UseApplicationInsights** -bővítmény metódusának meghívásával a IWebHostBuilder 
- A **AddApplicationInsightsTelemetry** -bővítmény metódusának meghívásával a IServiceCollection

A ApplicationInsightsLoggerProvider által rögzített ILogger-naplók ugyanazon konfigurációra vonatkoznak, mint a többi összegyűjtött telemetria. Ugyanazzal a TelemetryInitializers és TelemetryProcessors rendelkeznek, ugyanazokat a TelemetryChannel használják, és ugyanúgy vannak összehasonlítva és mint a többi telemetria. Ha a 2.7.0-beta3 vagy újabb verzióját használja, a ILogger-naplók rögzítéséhez nincs szükség beavatkozásra.

Alapértelmezés  szerint csak a figyelmeztetési vagy a magasabb ILogger-naplók (az összes kategóriából) lesznek elküldve a Application Insights. A [viselkedés módosításához](#control-logging-level)azonban szűrőket is alkalmazhat. A ILogger-naplók **program.cs** vagy **Startup.cs**való rögzítéséhez további lépések szükségesek. (Lásd: [ILogger-naplók rögzítése ASP.net Core alkalmazásokban a Startup.cs és a program.cs](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Ha a Microsoft. ApplicationInsights. AspNet SDK egy korábbi verzióját használja, vagy ha más Application Insights figyelés nélkül kívánja használni a ApplicationInsightsLoggerProvider-t, kövesse az alábbi eljárást:

1. Telepítse a NuGet csomagot:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Módosítsa a **program.cs** az itt látható módon:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

A 2 `ApplicationInsightsLoggerProvider`. lépésben szereplő kód konfigurálja. A következő kód egy példa vezérlő osztályt mutat be, `ILogger` amely a naplók küldésére használja. A naplókat a Application Insights rögzíti.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ILogger-naplók rögzítése a Startup.cs és a Program.cs alkalmazásban ASP.NET Core alkalmazásokban

> [!NOTE]
> A ASP.net Core 3,0-es és újabb verzióiban már nem lehet beszúrni `ILogger` a Startup.cs és a program.cs. További https://github.com/aspnet/Announcements/issues/353 részletekért tekintse meg a következőt:.

Az új ApplicationInsightsLoggerProvider az alkalmazás-indítási folyamat elején rögzíthet naplókat. Bár a ApplicationInsightsLoggerProvider automatikusan engedélyezve van a Application Insights (a 2.7.0-beta3 verziótól kezdődően), nem rendelkezik a kialakítási kulccsal, amely a folyamat későbbi részében van beállítva. Így csak a **vezérlő**/other osztályaiból származó naplók lesznek rögzítve. Ha a **program.cs** és a **Startup.cs** -től kezdődően minden naplót rögzíteni szeretne, explicit módon engedélyeznie kell a ApplicationInsightsLoggerProvider rendszerállapot-kulcsát. Emellett a *TelemetryConfiguration* nincs teljesen beállítva, ha a **Program.cs** vagy a **Startup.cs** -ből jelentkezik be. Így ezek a naplók a InMemoryChannel-t használó minimális konfigurációval rendelkeznek, nem mintavételezéssel, és nem rendelkeznek szabványos telemetria-inicializálással vagy processzorokkal.

Az alábbi példák bemutatják ezt a képességet a **program.cs** és a **Startup.cs**.

#### <a name="example-programcs"></a>Példa Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Példa Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Áttelepítés a régi ApplicationInsightsLoggerProvider

A Microsoft. ApplicationInsights. AspNet SDK-verziók, mielőtt a 2.7.0-Beta2 a már elavult naplózási szolgáltatót támogatta. Ezt a szolgáltatót engedélyezte a ILoggerFactory **AddApplicationInsights ()** bővítményi metódusa. Javasoljuk, hogy telepítse át az új szolgáltatóra, amely két lépést is magában foglal:

1. A kettős naplózás elkerüléséhez távolítsa el a *ILoggerFactory. AddApplicationInsights ()* metódust a **Startup. configure ()** metódusból.
2. Alkalmazza újra a kód szűrési szabályait, mivel azokat az új szolgáltató nem veszi figyelembe. A *ILoggerFactory. AddApplicationInsights ()* túlterhelései minimális naplózási szint vagy szűrési funkciókat vettek igénybe. Az új szolgáltatónál a szűrés a naplózási keretrendszer részét képezi. Ezt a Application Insights szolgáltató nem végzi el. Így a *ILoggerFactory. AddApplicationInsights ()-* n keresztül biztosított szűrőket el kell távolítani. És a szűrési szabályokat a [vezérlés naplózási szintjének](#control-logging-level) útmutatása szerint kell megadni. Ha a *appSettings. JSON* használatával szűri a naplózást, az továbbra is együttműködik az új szolgáltatóval, mert mindkettő ugyanazt a szolgáltatói aliast használja, mint a *ApplicationInsights*.

Továbbra is használhatja a régi szolgáltatót. (A rendszer csak a főverzió változását veszi át 3 értékre. *XX*.) Javasoljuk azonban, hogy a következő okokból telepítse át az új szolgáltatóra:

- Az előző szolgáltató nem támogatja a [naplózási hatóköröket](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). Az új szolgáltatóban a hatókör tulajdonságai automatikusan egyéni tulajdonságokként lesznek hozzáadva az összegyűjtött telemetria.
- A naplók mostantól jóval korábban is rögzíthetők az alkalmazás indítási folyamatában. A programból  származó naplók és az **indítási** osztályok mostantól rögzíthetők.
- Az új szolgáltató esetében a szűrés a keretrendszer szintjén történik. A naplókat a Application Insights szolgáltatóhoz ugyanúgy szűrheti, mint a többi szolgáltatónál, például a beépített szolgáltatók, például a konzol, a hibakeresés és így tovább. Ugyanezeket a szűrőket is alkalmazhatja több szolgáltatóra is.
- A ASP.NET Core (2,0-es és újabb verziók) esetében a [naplózási szolgáltatók engedélyezésének](https://github.com/aspnet/Announcements/issues/255) ajánlott módja a ILoggingBuilder a **program.cs** -ben való használatának javasolt módszere.

> [!Note]
> Az új szolgáltató a NETSTANDARD 2.0-s vagy újabb verzióját használó alkalmazásokhoz érhető el. Ha az alkalmazás olyan régebbi .NET Core-verziókat céloz meg, mint például a .NET Core 1,1, vagy ha a .NET-keretrendszert célozza meg, folytassa a régi szolgáltató használatával.

## <a name="console-application"></a>Konzol alkalmazás

A következő kód egy példaként szolgáló konzolos alkalmazást mutat be, amely a ILogger-Nyomkövetések Application Insights való küldésére van konfigurálva.

Telepített csomagok:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

Ez a példa az önálló csomagot `Microsoft.Extensions.Logging.ApplicationInsights`használja. Alapértelmezés szerint ez a konfiguráció a "csupasz minimális" TelemetryConfiguration használja az adatok Application Insights való küldéséhez. A csupasz minimális érték azt jelenti, hogy a InMemoryChannel a használt csatorna. Nincs mintavételezés és standard TelemetryInitializers. Ezt a viselkedést felülbírálhatja egy Console-alkalmazás esetében, az alábbi példában látható módon.

Telepítse ezt a kiegészítő csomagot:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A következő szakasz bemutatja, hogyan bírálhatja felül az alapértelmezett TelemetryConfiguration a **szolgáltatások használatával. Konfigurálja\<a TelemetryConfiguration > ()** metódust. Ez a példa beállítja `ServerTelemetryChannel` és mintavételezést végez. Hozzáadja az egyéni ITelemetryInitializer a TelemetryConfiguration.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Vezérlés naplózási szintje

A ASP.NET Core *ILogger* infra beépített mechanizmussal rendelkezik a [naplózási szűrés](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)alkalmazásához. Így szabályozhatja az egyes regisztrált szolgáltatók számára eljuttatott naplókat, beleértve a Application Insights szolgáltatót is. A szűrés a konfigurációban végezhető el (jellemzően egy *appSettings. JSON* fájl használatával) vagy a kódban. Ezt a lehetőséget maga a keretrendszer biztosítja. Nem jellemző a Application Insights szolgáltatóra.

Az alábbi példák a ApplicationInsightsLoggerProvider szűrési szabályokat alkalmaznak.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Szűrési szabályok létrehozása a konfigurációban a appSettings. JSON-vel

A ApplicationInsightsLoggerProvider esetében a szolgáltató aliasa `ApplicationInsights`a következő:. A *appSettings. JSON* következő szakasza a naplókat konfigurálja  a figyelmeztetési és a feletti *hibákra* vonatkozóan, és a fenti, a "Microsoft `ApplicationInsightsLoggerProvider`" kezdetű kategóriáktól kezdve a következő kategóriába tartozó kategóriákat.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Szűrési szabályok létrehozása a kódban

A következő kódrészlet az összes kategóriából származó *Figyelmeztetési* és ennél magasabb szintű naplókat konfigurálja, valamint a "Microsoft" `ApplicationInsightsLoggerProvider`kifejezéssel kezdődően *a (z* ) " Ez a konfiguráció megegyezik a *appSettings. JSON*előző szakaszával.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Melyek a ApplicationInsightsLoggerProvider régi és új verziói?

A [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) tartalmaz egy beépített ApplicationInsightsLoggerProvider (Microsoft. ApplicationInsights. AspNetCore. Logging. ApplicationInsightsLoggerProvider), amely engedélyezve volt a **ILoggerFactory** kiterjesztési módszerek. Ez a szolgáltató elavultként van megjelölve az 2.7.0-Beta2 verzióban. A rendszer a következő főverzió változásakor teljesen eltávolítja. A [Microsoft. ApplicationInsights. AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) csomag maga nem elavult. A kérések, a függőségek és egyebek figyelésének engedélyezéséhez szükséges.

A javasolt alternatíva a [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)új önálló csomagja, amely továbbfejlesztett ApplicationInsightsLoggerProvider tartalmaz ( Microsoft. Extensions. Logging. ApplicationInsights. ApplicationInsightsLoggerProvider) és bővítményi metódusok a ILoggerBuilder-on a engedélyezéséhez.

[Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) -verzió: 2.7.0 – a beta3 az új csomagtól függ, és lehetővé teszi a ILogger automatikus rögzítését.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Miért jelenik meg kétszer a Application Insights ILogger-naplója?

Az ismétlődés akkor fordulhat elő, ha a ApplicationInsightsLoggerProvider régebbi (már elavult) verziója engedélyezve van a `AddApplicationInsights` hívással `ILoggerFactory`. Ellenőrizze, hogy a **configure** metódus rendelkezik-e a következőkkel, majd távolítsa el:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Ha a Visual studióból történő hibakereséskor a kettős naplózást tapasztalja, a következő módon állítsa `EnableDebugLogger` a False (hamis) *értéket* a Application Insightst engedélyező kódban. Ez az ismétlődés és javítás csak az alkalmazás hibakeresése esetén releváns.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Frissítettem a [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) -2.7.0-beta3-re, és a rendszer automatikusan rögzíti a naplókat a ILogger. Hogyan teljesen kikapcsolja ezt a funkciót?

Tekintse meg a [vezérlők naplózási szintjének](../../azure-monitor/app/ilogger.md#control-logging-level) című szakaszt, amelyből megtudhatja, hogyan szűrheti az általános naplókat. A ApplicationInsightsLoggerProvider kikapcsolásához használja `LogLevel.None`a következőt:

**A kódban:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**A konfigurációban:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Miért nem ugyanazokkal a tulajdonságokkal rendelkeznek a ILogger-naplók, mint mások?

Application Insights rögzíti és elküldi a ILogger-naplókat ugyanazzal a TelemetryConfiguration, amelyet minden más telemetria használ. Kivétel történt. Alapértelmezés szerint a TelemetryConfiguration nincs teljesen beállítva a **program.cs** vagy a **Startup.cs**szolgáltatásba való bejelentkezéskor. Ezen helyekről származó naplók nem rendelkeznek az alapértelmezett konfigurációval, így nem fognak futni az összes TelemetryInitializers és TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>A Microsoft. Extensions. Logging. ApplicationInsights önálló csomagot használom, és manuálisan szeretnék bejelentkezni néhány további egyéni telemetria. Hogyan tehetem ezt meg?

Ha az önálló csomagot használja, `TelemetryClient` a nem a di tárolóba van befecskendezve, ezért létre kell hoznia egy új `TelemetryClient` példányt, és ugyanazt a konfigurációt kell használnia, mint amelyet a naplózó szolgáltató használ, ahogy az alábbi kód mutatja. Ez biztosítja, hogy ugyanazt a konfigurációt használja az összes egyéni telemetria, valamint a ILogger telemetria.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Ha a Microsoft. ApplicationInsights. AspNetCore csomagot használja a Application Insights engedélyezéséhez, módosítsa ezt a kódot közvetlenül `TelemetryClient` a konstruktorba való beolvasáshoz. Példaként tekintse meg [ezt a gyakori kérdéseket](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Milyen Application Insights telemetria-típust hoz létre a ILogger-naplókból? Vagy Hol láthatom a ILogger-naplókat Application Insights?

A ApplicationInsightsLoggerProvider rögzíti a ILogger-naplókat, és létrehozza a TraceTelemetry. Ha a ILogger a **log ()** metódushoz egy kivétel objektumot ad át, a *ExceptionTelemetry* a TraceTelemetry helyett jön létre. Ezek a telemetria elemek ugyanazon a helyen találhatók, mint bármely más TraceTelemetry vagy ExceptionTelemetry, beleértve Application Insights a portált, az elemzést vagy a Visual Studio helyi hibakeresőjét is.

Ha szeretné mindig elküldeni a TraceTelemetry, használja a következő kódrészletet:```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Nem telepítettem az SDK-t, és az Azure Web Apps bővítmény használatával Engedélyezem a Application Insights a ASP.NET Core alkalmazásokhoz. Hogyan használja az új szolgáltatót? 

Az Azure Web Apps Application Insights bővítménye a régi szolgáltatót használja. Az alkalmazáshoz tartozó *appSettings. JSON* fájlban módosíthatja a szűrési szabályokat. Az új szolgáltató kihasználása érdekében használja a NuGet függőséget az SDK-val. Ez a cikk akkor frissül, ha a bővítmény az új szolgáltató használatára vált.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>A Microsoft. Extensions. Logging. ApplicationInsights önálló csomagja és a Application Insights Provider engedélyezése a Builder hívásával **. AddApplicationInsights ("rendszerállapotkulcsot")** . Van lehetőség rendszerállapot-kulcs beszerzésére a konfigurációból?


Módosítsa a Program.cs és a appSettings. JSON fájlt a következőképpen:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Kapcsolódó szakasz `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Ez a kód csak önálló naplózási szolgáltató használata esetén szükséges. A rendszeres Application Insights figyeléséhez a rendszer automatikusan betölti a kialakítási kulcsot a konfigurációs *útvonal ApplicationInsights: Instrumentationkey*. A appSettings. JSON fájlnak így kell kinéznie:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>További lépések

További információk az alábbiakról:

* [Bejelentkezés ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [.NET-nyomkövetési naplók a Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
