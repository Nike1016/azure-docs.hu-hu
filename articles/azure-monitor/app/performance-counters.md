---
title: Teljesítményszámlálók a Application Insightsban | Microsoft Docs
description: A rendszer és az egyéni .NET-teljesítményszámlálók figyelése Application Insightsban.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: c681b58b01979b95e35ae57cefde38c56a787543
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68360251"
---
# <a name="system-performance-counters-in-application-insights"></a>Rendszerteljesítmény-számlálók a Application Insightsban

A Windows [teljesítményszámlálók](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) széles választékát biztosítja, amelyek például a processzor foglaltságáról, vagy a memória-, merevlemez- és hálózathasználatról biztosítanak adatokat. Megadhatja saját teljesítményszámlálók is. A teljesítményszámlálók gyűjteménye akkor támogatott, ha az alkalmazás az IIS alatt fut egy helyszíni gazdagépen vagy olyan virtuális gépen, amelyhez rendszergazdai hozzáféréssel rendelkezik. Habár az Azure Web Apps-t futtató alkalmazások nem rendelkeznek közvetlen hozzáféréssel a teljesítményszámlálók eléréséhez, a rendelkezésre álló számlálók egy részét Application Insights gyűjti.

## <a name="view-counters"></a>Számlálók megtekintése

A metrikák ablaktáblán a teljesítményszámlálók alapértelmezett készlete látható.

![Application Insightsben jelentett teljesítményszámlálók](./media/performance-counters/performance-counters.png)

A ASP.NET/ASP.NET Core-webalkalmazások gyűjtésére konfigurált aktuális alapértelmezett számlálók a következők:
- Feldolgozási\\idő%-ban a processzoron
- A processzor\\processzoridő (%) normalizálása folyamatban van
- Rendelkezésre\\álló memória bájtjai
- ASP.NET kérelmek/másodperc
- .NET CLR-kivételek/mp
- ASP.NET ApplicationsRequest végrehajtási ideje
- Saját\\bájtok feldolgozása
- Feldolgozási\\IO-adatmennyiség (bájt/s)
- \\ASP.net-kérelmek az alkalmazás-várólistán
- Processzor (összesen)\\processzoridő (%)

## <a name="add-counters"></a>Számlálók hozzáadása

Ha a használni kívánt teljesítményszámláló nem szerepel a metrikák listáján, akkor hozzáadhatja azt.

1. A következő PowerShell-parancs használatával megtudhatja, hogy mely számlálók érhetők el a kiszolgálón:

    `Get-Counter -ListSet *`

    (Lásd [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx):.)
2. Nyissa meg a ApplicationInsights. config fájlt.

   * Ha a fejlesztés során Application Insightst adott hozzá az alkalmazáshoz, szerkessze a ApplicationInsights. config fájlt a projektben, majd telepítse újra a kiszolgálókra.
3. A Performance Collector direktíva szerkesztése:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.net Core alkalmazások nem rendelkeznek `ApplicationInsights.config`, ezért a fenti módszer nem érvényes ASP.net Core alkalmazásokhoz.

Rögzítheti a standard számlálókat és a saját maga által végrehajtott módosításokat is. `\Objects\Processes`egy példa egy szabványos számlálóra, amely minden Windows rendszeren elérhető. `\Sales(photo)\# Items Sold`egy olyan egyéni számlálóra mutat példát, amely egy webszolgáltatásban valósítható meg.

A formátum `\Category(instance)\Counter"`, vagy olyan kategóriák esetében, amelyek nem rendelkeznek példányokkal `\Category\Counter`, csak.

`ReportAs`a nem egyező `[a-zA-Z()/-_ \.]+` számlálók neve esetén szükséges, azaz olyan karaktereket tartalmaznak, amelyek nem szerepelnek a következő készletekben: betűk, kerek zárójelek, perjel, kötőjel, aláhúzás, szóköz, pont.

Ha megad egy példányt, a rendszer a jelentett metrika "CounterInstanceName" dimenzióját fogja gyűjteni.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Teljesítményszámlálók gyűjtése a kódban a ASP.NET webalkalmazások vagy a .NET/.NET Core Console-alkalmazások számára
A rendszerteljesítmény-számlálók összegyűjtéséhez és a Application Insightsba való küldéséhez az alábbi kódrészletet módosíthatja:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Vagy megteheti ugyanezt a létrehozott egyéni metrikákkal:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Teljesítményszámlálók gyűjtése a kódban ASP.NET Core webalkalmazásokhoz

Módosítsa `ConfigureServices` a`Startup.cs` metódust az osztályban az alábbi módon.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Teljesítményszámlálók az Analyticsben
A teljesítményszámláló-jelentéseket az elemzésekben keresheti meg [](../../azure-monitor/app/analytics.md)és jelenítheti meg.

A **performanceCounters** séma megjeleníti az `category` `counter` egyes teljesítményszámlálók nevét és `instance` nevét.  Az egyes alkalmazások telemetria csak az adott alkalmazás számlálói láthatók. Például a rendelkezésre álló számlálók megtekintéséhez: 

![Teljesítményszámlálók a Application Insights Analyticsben](./media/performance-counters/analytics-performance-counters.png)

(A "példány" itt a teljesítményszámláló példányára hivatkozik, nem a szerepkörre vagy a kiszolgáló virtuálisgép-példányára. A teljesítményszámláló-példány neve jellemzően a folyamat vagy alkalmazás nevével ellátott számlálókat (például a processzoridő) adja meg.)

A rendelkezésre álló memória diagramjának lekérése a legutóbbi időszakban: 

![Memória idődiagramját a Application Insights Analyticsben](./media/performance-counters/analytics-available-memory.png)

A többi telemetria hasonlóan a **performanceCounters** is tartalmaz egy `cloud_RoleInstance` oszlopot, amely jelzi annak a gazdagép-példánynak az identitását, amelyen az alkalmazás fut. Ha például össze szeretné hasonlítani az alkalmazás teljesítményét a különböző gépeken: 

![A Application Insights Analytics szerepkör-példánya által szegmentált teljesítmény](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET és Application Insights darabszám

*Mi a különbség a kivételek és a kivételek mérőszámai között?*

* A *kivételek aránya* a rendszer teljesítményszámlálói. A CLR megszámlálja az összes kezelt és nem kezelt kivételt, és egy mintavételi intervallumban osztja el az összeget az intervallum hosszával. A Application Insights SDK ezt az eredményt gyűjti, és elküldi a portálnak.

* A kivételek a diagram mintavételi intervallumában a portál által fogadott TrackException-jelentések száma. Csak azokat a kezelt kivételeket tartalmazza, amelyekben írásos TrackException-hívás szerepel a kódban, és nem tartalmazza az összes nem [kezelt kivételt](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Teljesítményszámlálók az Azure-ban futó alkalmazások teljesítményszámlálói Web Apps

Az Azure Web Apps üzembe helyezett ASP.NET-és ASP.NET Core-alkalmazások egy speciális sandbox-környezetben futnak. Ez a környezet nem teszi lehetővé a rendszerteljesítmény-számlálók közvetlen elérését. A számlálók korlátozott részhalmaza azonban környezeti változókként van kitéve az [itt](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables)leírtak szerint. A ASP.NET és a ASP.NET Core Application Insights SDK ezeket a speciális környezeti változókból gyűjti a teljesítményszámlálók az Azure-ból Web Apps. Ebben a környezetben csak a számlálók egy részhalmaza érhető el, és a teljes lista [itt található.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Teljesítményszámlálók ASP.NET Core alkalmazásokban

* [ASP.net Core SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.4.1-es és újabb verziója a teljesítményszámlálókat gyűjti, ha az alkalmazás az Azure Web App (Windows) rendszerben fut

* Az SDK 2.7.0-beta3 és újabb verziója a teljesítményszámlálókat gyűjti, ha az alkalmazás Windows rendszeren fut, és `NETSTANDARD2.0` célzott vagy magasabb.
* A .NET-keretrendszert célzó alkalmazások esetén a teljesítményszámlálók az SDK összes verziójában támogatottak.
* Ez a cikk akkor frissül, ha a nem Windows rendszerű teljesítményszámláló-támogatás hozzá van adva.

## <a name="alerts"></a>Riasztások
Más mérőszámokhoz hasonlóan riasztást is [beállíthat](../../azure-monitor/app/alerts.md) , amely figyelmezteti, ha a teljesítményszámláló a megadott korláton kívül esik. Nyissa meg a riasztások ablaktáblát, és kattintson a riasztás hozzáadása lehetőségre.

## <a name="next"></a>Következő lépések

* [Függőségek nyomon követése](../../azure-monitor/app/asp-net-dependencies.md)
* [Kivétel követése](../../azure-monitor/app/asp-net-exceptions.md)

